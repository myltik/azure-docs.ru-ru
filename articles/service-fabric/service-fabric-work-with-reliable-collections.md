---
title: Работа с Reliable Collections | Документация Майкрософт
description: Рекомендации по работе с API-интерфейсами Reliable Collections.
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: 2568e116fdb3f80976d49787877d2ecf68f128ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210823"
---
# <a name="working-with-reliable-collections"></a>Работа с Reliable Collections
Платформа Service Fabric предлагает модель программирования с отслеживанием состояния, которая доступна разработчикам .NET через API-интерфейсы Reliable Collections. В частности, Service Fabric предоставляет такие классы, как Reliable Dictionaries (далее — надежные словари) и Reliable Queues (далее — надежные очереди). При использовании этих классов состояние секционируется (для масштабируемости), реплицируется (для доступности) и обрабатывается в ходе транзакции в секции (для семантики ACID). Рассмотрим типичное использование объекта надежного словаря, чтобы узнать, какие функции этот объект выполняет.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

Для всех операций с объектами надежных словарей (за исключением операции ClearAsync, которая является обратимой) требуется объект ITransaction. С этим объектом связываются все изменения, которые вы пытаетесь внести в любые объекты надежных словарей или очередей в одной секции. Чтобы получить объект ITransaction, вызовите метод CreateTransaction секции StateManager.

В приведенном выше коде объект ITransaction передается в метод AddAsync надежного словаря. Внутри методы словаря, которые принимают ключ, устанавливают блокировки на чтение или запись, связанные с ключом. Если метод изменяет значение ключа, метод устанавливает блокировку на запись в ключе. Если этот метод только считывает данные из значения ключа, к ключу применяется блокировка чтения. Так как метод AddAsync изменяет значение ключа на новое значение, которое было передано, к ключу применяется блокировка на запись. Таким образом, если два (или более) потока пытаются одновременно добавить значения с тем же ключом, один поток получит блокировку записи, а остальные потоки заблокируются. По умолчанию методы блокируются до четырех секунд для получения блокировки. Через четыре секунды методы создают исключение TimeoutException. Используя перегрузки методов, вы можете при желании указать значение времени ожидания.

Для этого нужно написать код, который будет реагировать на исключение TimeoutException, перехватывая его и повторяя всю операцию (как показано в приведенном выше коде). Мой простой код вызывает передачу Task.Delay через каждые 100 миллисекунд. Но на самом деле, возможно, будет лучше использовать экспоненциальную задержку.

После получения блокировки метод AddAsync добавляет ссылки на объект ключа и значения для внутренних временных словарей, связанных с объектом ITransaction. Это делается для обеспечения семантики, которая позволяет владельцу считывать собственные записи. То есть, если после вызова AddAsync вызвать метод TryGetValueAsync (с использованием того же объекта ITransaction), значение будет возвращено, даже если транзакция еще не зафиксирована. Затем метод AddAsync сериализует объекты ключа и значения в массивы байтов, а также добавляет эти массивы байтов в файл журнала на локальном узле. Наконец, метод AddAsync отправляет массивы байтов во все вторичные реплики, чтобы реплики имели ту же информацию о ключе и значении. Хотя информация о ключе и значении записана в файл журнала, эти сведения не считаются частью словаря, пока не будет зафиксирована связанная с ними транзакция.

В приведенном выше коде вызов метода CommitAsync фиксирует все операции транзакции. В частности, этот вызов добавляет в файл журнала на локальном узле сведения о фиксации, а также отправляет запись о фиксации во все вторичные реплики. После получения ответа от большинства реплик все изменения данных считаются постоянными, и все блокировки, связанные с ключами, которые обрабатывались с помощью объекта ITransaction, отменяются. Теперь другие потоки или транзакции могут обрабатывать те же ключи и их значения.

Если метод CommitAsync не вызывается (обычно из-за исключения), объект ITransaction ликвидируется. Когда незафиксированный объект ITransaction удаляется, Service Fabric добавляет в файл журнала на локальном узле сведения о прерывании операции. Во вторичные реплики ничего не отправляется. Затем отменяются все блокировки, связанные с ключами, которые обрабатывались с помощью транзакции.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Типичные проблемы и способы их решения
Теперь, когда вы понимаете принцип работы API-интерфейсов Reliable Collections, рассмотрим некоторые распространенные случаи их неправильного использования. Взгляните на этот код.

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Если вы работаете с обычным словарем .NET, вы можете добавить в словарь ключ и значение, а затем изменить значение свойства (например, LastLogin). Но этот код не будет правильно работать с надежным словарем. Как вы помните, вызов метода AddAsync сериализует объекты ключей и значений в массивы байтов, сохраняет эти массивы в локальный файл, а также отправляет их во вторичные реплики. Если впоследствии изменить свойство, изменится только значение свойства в памяти. Оно не повлияет на локальный файл или данные, отправленные в реплики. При сбое процесса все данные в памяти будут утеряны. Если запускается новый процесс или основной репликой становится другая реплика, доступным остается только старое значение свойства.

Допустить описанную выше ошибку очень просто. А узнаете вы о ней, только когда произойдет сбой. Чтобы получить правильный код, достаточно поменять местами две строки:


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Вот другой пример, демонстрирующий распространенную ошибку.

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Опять же, если используются обычные словари .NET, приведенный выше код работает нормально. Это обычная ситуация — разработчик использует ключ для поиска значений. Если значение существует, разработчик меняет значение свойства. Но если используются API-интерфейсы Reliable Collections, в коде возникает та же проблема. **Не изменяйте объект, если он отправлен в Reliable Collections.**

Чтобы правильно обновить значение в API, нужно получить ссылку на существующее значение и считать объект, на который указывает эта ссылка, неизменяемым. Теперь создайте новый объект, который будет точной копией исходного объекта. Вы можете изменить состояние этого нового объекта и записать новый объект в коллекцию, чтобы он был сериализован в массив байтов, добавлен в локальный файл и отправлен в реплику. После фиксации изменений объекты в памяти, локальный файл и все реплики будут в одинаковом состоянии. Теперь все хорошо.

Следующий пример кода показывает, как правильно обновить значение в коллекции Reliable Collections.

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Определение неизменяемых типов данных для предотвращения ошибок разработчика
В идеале хотелось бы, чтобы компилятор сообщал об ошибках при случайном создании кода, изменяющего состояние объекта, который должен быть неизменяемым. Но компилятор C# не может это сделать. Таким образом, чтобы избежать потенциальных ошибок, мы настоятельно рекомендуем определять типы, используемые в Reliable Collections, как неизменяемые. Это означает, что вы будете использовать основные типы значений (например, числа [Int32, UInt64 и т. д.], DateTime, Guid, TimeSpan и т. п). И, конечно, вы также можете использовать тип String. Свойства коллекции лучше не использовать, так как их частая сериализация и десериализация может снизить производительность. Однако если вы хотите использовать свойства коллекции, мы настоятельно рекомендуем использовать библиотеку .NET неизменяемых коллекций ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Эту библиотеку можно скачать с сайта http://nuget.org. Также рекомендуется запечатывать классы и по возможности предоставлять к полям доступ только для чтения.

Тип UserInfo ниже демонстрирует, как определить неизменяемый тип, чтобы воспользоваться преимуществами упомянутых выше рекомендаций.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

Тип ItemId также является неизменяемым, как показано ниже.

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Управление версиями схемы (обновления)
На внутреннем уровне коллекции Reliable Collections сериализуют объекты с помощью DataContractSerializer .NET. Сериализованные объекты сохраняются на локальном диске основной реплики и передаются во вторичные реплики. По мере роста службы вы, вероятно, захотите изменить тип данных (схему), которые требуются службе. Управлять версиями данных необходимо предельно осторожно. Первое и самое главное: у вас должна быть возможность десериализовать старые данные. Это означает, что код десериализации должен быть неограниченно обратно совместимым: версия 333 кода службы должна поддерживать обработку данных, которые версия 1 кода службы разместила в надежной коллекции 5 лет назад.

Кроме того, код службы обновляет один домен обновления одновременно. Таким образом, во время обновления одновременно работают две различные версии кода службы. Проследите, чтобы новая версия кода службы не использовала новую схему, так как старые версии кода службы могут не работать с новой схемой. По возможности следует разрабатывать каждую новую версию службы так, чтобы она была совместима с версией 1. Это означает, что версия 1 кода службы должна просто игнорировать все элементы схемы, которые она явно не обрабатывает. Но она также должна обеспечивать хранение всех данных, которые ей явно неизвестны, просто записывая их обратно при обновлении ключа или значения словаря.

> [!WARNING]
> Во время изменения схемы ключа проследите, чтобы хэш-код и алгоритмы ключа оставались постоянными. Если вы измените работу одного из этих алгоритмов, вы никогда больше не сможете искать ключ в надежном словаре.
>
>

Кроме того, можно выполнить так называемое двухэтапное обновление. Используя двухэтапное обновление, вы обновите службу с версии 1 до версии 2. Версия 2 содержит код, который может обрабатывать изменения в новой схеме, но этот код не выполняется. Когда код версии 2 считывает данные версии 1, он работает с этими данными, а затем записывает их. Затем, когда обновление завершится на всех доменах обновления, вы можете сообщить запущенным экземплярам версии 2, что обновление завершено. Один из способов сообщить об этом — развернуть обновление конфигурации. Именно поэтому это обновление называется двухэтапным. Теперь экземпляры версии 2 могут читать данные версии 1, преобразовывать их в данные версии 2, работать с ними и записывать как данные версии 2. Когда другие экземпляры считывают данные версии 2, им не нужно преобразовывать их. Они могут работать с ними и записывать как данные версии 2.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании контрактов данных с прямой совместимостью см. в статье [Контракты данных, совместимые с любыми будущими изменениями](https://msdn.microsoft.com/library/ms731083.aspx).

Рекомендации по управлению версиями контрактов данных см. в статье [Управление версиями контракта данных](https://msdn.microsoft.com/library/ms731138.aspx).

Указания по реализации контрактов данных, которые не зависят от версий, см. в статье [Обратные вызовы сериализации, независимые от версий](https://msdn.microsoft.com/library/ms733734.aspx).

Указания по предоставлению структуры данных, которые могут взаимодействовать в нескольких версиях, см. в статье [Интерфейс IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
