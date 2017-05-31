---

redirect_url: https://azure.microsoft.com/services/cosmos-db/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2bb9fa3c151b0e36f73ba0c1432529499ee7062b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017



---
# <a name="how-to-partition-data-using-client-side-support-in-azure-cosmos-db"></a>Как секционировать данные с помощью поддержки на стороне клиента в Azure Cosmos DB
Azure Cosmos DB поддерживает [автоматическое секционирование коллекций](documentdb-partition-data.md). Однако существуют варианты использования, когда полезно иметь более точный контроль над поведением секционирования. Для уменьшения объема кода, необходимого для задач секционирования, мы добавили в пакеты SDK для Java, .NET и Node.js функции, которые упрощают создание приложений, масштабируемых по нескольким коллекциям.

В этой статье мы рассмотрим классы и интерфейсы пакета SDK для .NET и расскажем, как их использовать для разработки секционированных приложений. Другие пакеты SDK, например для Java, Node.js и Python, поддерживают аналогичные методы и интерфейсы для секционирования на стороне клиента.

## <a name="client-side-partitioning-with-the-sdk"></a>Секционирование на стороне клиента с помощью пакета SDK
Прежде чем углубиться в секционирование, освежим в памяти основные понятия Cosmos DB, связанные с секционированием. Каждая учетная запись базы данных Azure Cosmos DB состоит из набора баз данных, каждая из которых содержит несколько коллекций с хранимыми процедурами, триггерами, определяемыми пользователем функциями, документами и соответствующими вложениями. Коллекции могут содержать одну секцию или сами быть секционированными. Они обладают следующими свойствами.

* Коллекции обеспечивают изоляцию производительности. Поэтому упорядочивание схожих документов в одной коллекции позволяет повысить производительность. Например, в случае данных временного ряда можно поместить данные за последний месяц, которые часто запрашиваются, в коллекцию с более высокой подготовленной пропускной способностью, а более старые данные — в коллекции с более низкой пропускной способностью.
* Транзакции ACID, т. е. хранимые процедуры и триггеры, не могут перекрывать коллекцию. Транзакции находятся в пределах одного значения ключа секции в коллекции.
* Коллекции не применяют схемы, поэтому их можно использовать для документов JSON одного или разных типов.

Начиная с версии [1.5.x пакетов SDK для Azure Cosmos DB](documentdb-sdk-dotnet.md), можно выполнять операции с документами непосредственно в базе данных. [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) внутренне использует PartitionResolver, указанный вами для базы данных, чтобы перенаправлять запросы в соответствующую коллекцию.

> [!NOTE]
> [Секционирование на стороне сервера](documentdb-partition-data.md) появилось в REST API 2015-12-16, и в пакетах SDK 1.6.0 и более поздних версий мы отказались от методики сопоставителя секций на стороне клиента в отношении простых вариантов использования. Тем не менее, секционирование на стороне клиента является более гибким и позволяет управлять изоляцией производительности в секциях, контролировать степень параллелизма при чтении результатов из нескольких секций и использовать методики диапазонного или пространственного секционирования и хэш.
> 
> 

Например, в .NET каждый класс PartitionResolver — это конкретная реализация интерфейса [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx), у которого три метода: [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) и [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Запросы LINQ и итераторы ReadFeed используют метод ResolveForRead внутренне для перебора всех коллекций, соответствующих ключу секции для запроса. Аналогичным образом операции создания используют метод ResolveForCreate для перенаправления в нужную секцию. Для операций замены, удаления и чтения изменения не требуются, так как они используют документы, которые уже содержат ссылку на соответствующую коллекцию.

Пакеты SDK также содержат два класса, поддерживающие два канонических метода секционирования, хэширование и поиск по диапазону, с помощью [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) и [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). Эти классы можно использовать, чтобы легко добавить логику секционирования в приложение.  

## <a name="add-partitioning-logic-and-register-the-partitionresolver"></a>Добавление логики секционирования и регистрация PartitionResolver
Ниже приведен фрагмент кода, показывающий, как создать [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) и зарегистрироваться в DocumentClient для базы данных.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## <a name="create-documents-in-a-partition"></a>Создание документов в секции
После регистрации PartitionResolver вы можете выполнять операции создания и запросы непосредственно для базы данных, как показано ниже. В этом примере пакет SDK использует PartitionResolver для извлечения и хэширования UserId, а затем это значение используется для направления операции создания в правильную коллекцию.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## <a name="create-queries-against-partitions"></a>Создание запросов к секциям
Вы можете создавать запросы с помощью метода [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) , передавая базу данных и ключ секции. Запрос возвращает один результирующий набор по всем коллекциям в базе данных, которые сопоставляются с ключом секции.  

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## <a name="create-queries-against-all-collections-in-the-database"></a>Создание запросов ко всем коллекциям в базе данных
Можно также запрашивать все коллекции в базе данных и перечислять результаты, как показано ниже, пропуская аргумент ключа секции.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## <a name="hash-partition-resolver"></a>Хэширование сопоставителя секции
При секционировании по хэшу секции назначаются на основе значения хэш-функции, что позволяет равномерно распределить запросы и данные по нескольким разделам. Этот подход часто применяется для секционирования данных, создаваемых или используемых большим количеством уникальных клиентов, и очень удобен для хранения профилей пользователей, элементов каталога и данных телеметрии Интернета вещей. Хэш-секционирование также используется в Cosmos DB для поддержки секционирования на стороне сервера в коллекции.

**Хэш-секционирование.**
![Диаграмма, на которой показано, как хэш-секционирование равномерно распределяет запросы по секциям](media/documentdb-sharding/partition-hash.png)

Простая схема хэш-секционирования в *N* коллекциях: взять любой документ и вычислить *хэш(d) mod N*, чтобы определить, в какую коллекцию он помещается. Однако проблема с этим простым способом заключается в том, что он не работает при добавлении новых коллекций или удалении коллекций, так как для этого потребуется заново обработать почти все данные. [Согласованное хэширование](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) — это хорошо известный алгоритм, который решает эту проблему, используя схему хэширования, которая сводит к минимуму объем перемещаемых данных, необходимых при добавлении или удалении коллекций.

Класс [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) реализует логику для построения согласованного хэш-кольца по хэш-функции, указанной в интерфейсе [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx). По умолчанию HashPartitionResolver использует хэш-функцию MD5, но ее заменить на собственную реализацию хэширования. HashPartitionResolver создает 16 хэшей или "виртуальных узлов" в хэш-кольце для каждой коллекции, чтобы добиться более равномерного распределения документов по коллекциям, но это число можно изменить, чтобы справиться с асимметрией данных вычислений на стороне клиента.

**Согласованное хэширование с помощью HashPartitionResolver.**
![Диаграмма, на которой показано, как HashPartitionResolver создает хэш-кольцо](media/documentdb-sharding/HashPartitionResolver.JPG)

## <a name="range-partition-resolver"></a>Сопоставитель секций диапазона
При секционировании по диапазону разделы назначаются в зависимости от того, входит ли ключ раздела в определенный диапазон. Это часто используется для секционирования со свойствами метки времени (например, eventTime от 1 апреля 2015 г. до 14 апреля 2015 г.). Класс [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) помогает поддерживать сопоставление между Range\<T\> и ссылкой на коллекцию. 

[Range\<T\>](https://msdn.microsoft.com/library/azure/mt126048.aspx) — это простой класс, который управляет диапазонами типов, реализующими интерфейс IComparable\<T\> и IEquatable\<T\> как строки или числа. Для операций чтения и создания можно передать любой произвольный диапазон, а сопоставитель определит все коллекции-кандидаты, получив диапазоны секций, которые пересекаются с запрошенным диапазоном. Это полезно при выполнении запросов временного ряда данных в диапазоне.

**Секционирование по диапазону:**  

![ Диаграмма, на которой показано, как секционирование по диапазону равномерно распределяет запросы по секциям](media/documentdb-sharding/partition-range.png)  

Особый случай секционирования по диапазону — диапазон из всего одного значения, что иногда называют "уточняющим секционированием". Оно часто используется для секционирования по региону (например, секция для Скандинавии содержит Норвегию, Данию и Швецию) или клиентов секционирования в мультитенантном приложении.

## <a name="samples"></a>Примеры
Рассмотрим [проект примеров секционирования DocumentDB на портале GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning), содержащий фрагменты кода, в которых показано, как использовать эти сопоставители и расширить их для реализации собственных сопоставителей в соответствии с вашими требованиям, например как в следующем случае: 

* Как указать произвольное лямбда-выражение для GetPartitionKey и использовать его для реализации составных ключей секционирования или для секционирования различных типов объектов по-разному.
* Как создать простой сопоставитель [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) , который использует ручную таблицу уточняющих запросов для секционирования. Этот шаблон часто используется для секционирования на основе дискретных значений, таких как регион, идентификатор клиента или имя приложения.
* Как создать сопоставитель [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) , который автоматически создает коллекции на основе шаблона, определяющего схему именования, IndexingPolicy и хранимые процедуры, которые должны быть зарегистрированы для новых коллекций.
* Как создать [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) без схемы, который просто создает новые коллекции при заполнении старых.
* Как сериализовывать и десериализовывать состояние PartitionResolver в формате JSON, так, чтобы совместно использовать его для разных процессов и между выключениями. Эти данные можно сохранить в файлах конфигурации или даже в коллекции DocumentDB.
* Класс [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) для динамического добавления и удаления секций в базе данных, секционированной на основе согласованного хэширования. Внутренне [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) используется для маршрутизации операций чтения и записи во время миграции в одном из четырех режимов — чтение из старой схемы секционирования (ReadCurrent), новой (ReadNext), объединение результатов из обеих схем (ReadBoth) или недоступность во время миграции (None).

Образцы содержат открытый исходный код, и мы рекомендуем вам отправлять запросы с материалами, которые могут принести пользу другим разработчикам Cosmos DB. Инструкции о том, как принять участие, см. [здесь](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md).  

> [!NOTE]
> Операции создания коллекции ограничены Cosmos DB по скорости, поэтому для выполнения некоторых примеров методов, показанных здесь, может потребоваться несколько минут.
> 
> 

## <a name="faq"></a>Часто задаваемые вопросы
**Поддерживает ли Cosmos DB секционирование на стороне сервера?**

Да, Cosmos DB поддерживает [секционирование на стороне сервера](documentdb-partition-data.md). Cosmos DB также поддерживает секционирование на стороне клиента через сопоставители секций на стороне клиента для более сложных случаев использования.

**Когда следует использовать секционирование на стороне сервера, а когда — на стороне клиента?**
В большинстве случаев мы рекомендуем использовать секционирование на стороне сервера, так как оно обрабатывает административные задачи секционирования данных и маршрутизации запросов. Однако если необходимо секционирование по диапазонам или имеется особый вариант использования для изоляции производительности между разными значениями ключей секций, то лучшим подходом может быть секционирование на стороне клиента.

**Как добавить или удалить коллекцию в схеме секционирования?**

Взгляните реализацию DocumentClientHashPartitioningManager в проекте примеров, чтобы узнать, как можно реализовать повторное секционирование.

**Как сохранить или передать конфигурацию секционирования другим клиентам?**

Вы можете сериализовать состояние модуля секционирования в формате JSON и сохранить его в файлах конфигурации или даже в коллекции DocumentDB. Изучите метод RunSerializeDeserializeSample в проекте примеров в качестве примера.

**Как связать различные методы секционирования?**

Вы можете связать сопоставители PartitionResolvers, реализуя собственный IPartitionResolver, который использует один или несколько существующих сопоставителей. Изучите TransitionHashPartitionResolver в проекте примеров.

## <a name="references"></a>Ссылки
* [Секционирование в базе данных Azure Cosmos DB с помощью API DocumentDB](documentdb-partition-data.md)
* [Прекращение использования уровней производительности S1, S2 и S3](documentdb-performance-levels.md)
* [Примеры кода секционирования на GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)
* [Документацию для DocumentDB .NET SDK на портале MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Примеры DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
* [Блог с советами по повышению производительности DocumentDB](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)


