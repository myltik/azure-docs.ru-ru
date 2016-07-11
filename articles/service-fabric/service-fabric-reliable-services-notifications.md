<properties
   pageTitle="Уведомления надежной службы | Microsoft Azure"
   description="Содержательная документация по уведомлениям надежной службы Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/24/2016"
   ms.author="mcoskun"/>

# Уведомления надежной службы

Уведомления позволяют клиентам отслеживать изменения, которые вносятся в интересующий их объект. Существует два типа объектов, поддерживающих уведомления: **диспетчер надежных состояний** и **надежный словарь**.

Распространенные причины для использования уведомлений:

- Построение материализованных представлений, например вторичных индексов или агрегированных отфильтрованных представлений состояния реплики. Например, это может быть отсортированный индекс всех ключей в надежном словаре.
- Отправка данных мониторинга, например количества пользователей, добавленных за последний час.

Уведомления активируются в ходе применения операций. Так как они являются частью применения операции, необходимо отметить, что обработка уведомлений должна быть максимально быстрой, и в синхронных событиях не должно быть ресурсоемких операций.

## Уведомления диспетчера надежных состояний

Диспетчер надежных состояний предоставляет уведомления для следующих событий:

- транзакция:
    - Фиксация
- Диспетчер состояний
    - перестроение;
    - добавление надежного состояния;
    - удаление надежного состояния.

Диспетчер надежных состояний отслеживает текущие обрабатываемые транзакции. Как только состояние транзакции меняется, при фиксации этой транзакции срабатывает уведомление.

Диспетчер надежных состояний поддерживает коллекцию надежных состояний, таких как надежный словарь и надежная очередь. Диспетчер надежных состояний активирует уведомления при изменении этой коллекции. Это может быть добавление или удаление надежного состояния, а также перестроение всей коллекции. Перестроение коллекции диспетчера надежных состояний происходит в трех случаях.

- Восстановление. При запуске реплики ее предыдущее состояние будет восстановлено с диска. По завершении восстановления активируется событие с экземпляром **NotifyStateManagerChangedEventArgs**, содержащим набор восстановленных **IReliableState**.
- Полная копия. Прежде чем реплику можно будет присоединить к набору конфигурации, нужно выполнить ее сборку. В некоторых случаях может потребоваться применить к неактивной вторичной реплике полную копию состояния диспетчера надежных состояний из первичной реплики. Диспетчер надежных состояний для вторичной реплики активирует событие с экземпляром **NotifyStateManagerChangedEventArgs**, содержащим набор **IReliableState**, полученный из первичной реплики.
- Восстановление. В сценариях аварийного восстановления состояние реплики можно восстановить из резервной копии с помощью **RestoreAsync**. В таких случаях диспетчер надежных состояний для первичной реплики активирует событие с экземпляром **NotifyStateManagerChangedEventArgs**, содержащим набор **IReliableState**, восстановленный из резервной копии.

### Как использовать уведомления диспетчера надежных состояний
Для получения уведомлений о транзакциях и (или) уведомлений диспетчера состояний пользователь должен выполнить регистрацию в диспетчере надежных состояний, используя событие **TransactionChanged** или **StateManagerChanged**, соответственно. Чаще всего для регистрации в этих обработчиках событий используется конструктор StatefulService. Причина этого состоит в том, что используя регистрацию в конструкторе, клиент гарантирует получение всех уведомлений, вызванных изменениями во время существования **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

EventHandler для **TransactionChanged** использует **NotifyTransactionChangedEventArgs** для предоставления сведений о событии. Эти сведения содержат свойство Action (например, **NotifyTransactionChangedAction.Commit**), указывающее тип изменения, и свойство Transaction, которое содержит ссылку на измененную транзакцию.

>[AZURE.NOTE] Сейчас события TransactionChanged порождаются только в том случае, если фиксируется транзакция, т. е. свойство Action будет равно NotifyTransactionChangedAction.Commit. Тем не менее в будущих обновлениях могут быть добавлены дополнительные действия. Таким образом рекомендуется проверять свойство Action и обрабатывать событие, только если оно ожидается.

Ниже приведен пример обработчик событий **TransactionChanged**.

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

EventHandler для **StateManagerChanged** использует **NotifyStateManagerChangedEventArgs** для предоставления сведений о событии. У **NotifyStateManagerChangedEventArgs** есть два подкласса: NotifyStateManagerRebuildEventArgs и NotifyStateManagerSingleEntityChangedEventArgs. Свойство Action в **NotifyStateManagerChangedEventArgs** следует использовать для приведения **NotifyStateManagerChangedEventArgs** к правильному подклассу.

- NotifyStateManagerChangedAction.Rebuild: NotifyStateManagerRebuildEventArgs.
- NotifyStateManagerChangedAction.Add и NotifyStateManagerChangedAction.Remove: NotifyStateManagerSingleEntityChangedEventArgs.

Ниже приведен пример обработчика уведомлений **StateManagerChanged**.

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## Уведомления надежного словаря

Надежный словарь предоставляет уведомления для следующих событий:

- Перестроение. Вызывается при восстановлении состояния **ReliableDictionary** с локального диска либо путем копирования из первичной реплики или резервной копии.
- Очистка. Вызывается при очистке состояния **ReliableDictionary** с помощью метода **ClearAsync**.
- Добавление. Вызывается при добавлении элемента в **ReliableDictionary**.
- Обновление. Вызывается при обновлении элемента в **IReliableDictionary**.
- Удаление. Вызывается при удалении элемента из **IReliableDictionary**.

### Как использовать уведомления надежного словаря
Для получения уведомлений надежного словаря пользователю нужно выполнить регистрацию обработчика событий **DictionaryChanaged** для **IReliableDictionary**. Чаще всего для регистрации этих обработчиков событий используется уведомление о добавлении **ReliableStateManager.StateManagerChanged**. Причина этого состоит в том, что регистрация во время добавления **IReliableDictionary** для **IReliableStateManager** гарантирует, что вы не пропустите какие-либо уведомления.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] ProcessStateManagerSingleEntityNotification является примером метода, который вызывается OnStateManagerChangedHandler в приведенном выше примере.

Обратите внимание, что указанный выше код задает IReliableNotificationAsyncCallback, а также **DictionaryChanged**. Так как **NotifyDictionaryRebuildEventArgs** содержит **IAsyncEnumerable**, для которого требуется асинхронное перечисление, уведомления о перестроении срабатывают посредством **RebuildNotificationAsyncCallback** вместо **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] В приведенном выше коде в ходе обработки уведомлений о перестроении сначала очищается агрегированное поддерживаемое состояние. Причина этого состоит в том, что так как надежная коллекция перестраивается с новым состоянием, то все прежние уведомления несущественны.

EventHandler для **DictionaryChanged** использует **NotifyDictionaryChangedEventArgs** для предоставления сведений о событии. У **NotifyDictionaryChangedEventArgs** есть пять подклассов. Свойство Action в **NotifyDictionaryChangedEventArgs** следует использовать для приведения **NotifyDictionaryChangedEventArgs** к правильному подклассу.

- NotifyDictionaryChangedAction.Rebuild: NotifyDictionaryRebuildEventArgs.
- NotifyDictionaryChangedAction.Clear: NotifyDictionaryClearEventArgs
- NotifyDictionaryChangedAction.Add и NotifyDictionaryChangedAction.Remove: NotifyDictionaryItemAddedEventArgs.
- NotifyDictionaryChangedAction.Update: NotifyDictionaryItemUpdatedEventArgs.
- NotifyDictionaryChangedAction.Remove: NotifyDictionaryItemRemovedEventArgs.

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## Рекомендации

- Завершайте события уведомлений максимально быстро.
- НЕ выполняйте какие-либо ресурсоемкие операции (например, операции ввода-вывода) в составе синхронных событий.
- Проверяйте тип действия перед обработкой события. Новые типы действий могут быть добавлены в будущем.

При этом нужно помнить о следующем:
- Уведомления срабатывают в ходе выполнения операции. Например, уведомление о восстановлении сработает на последнем шаге восстановления, и восстановление не будет завершено, пока это уведомление не будет обработано.
- Так как уведомления активируются при применении операций, клиенты будут видеть только уведомления для локально зафиксированных операций. Обратите внимание, что так как гарантируется только локальная фиксация операций (другими словами, ведение их журнала), то возможность в дальнейшем отменить их может быть как доступна, так и недоступна.
- На пути повтора для каждой примененной операции будет активировано одно уведомление. Это означает, что если транзакция T1 содержит операции Create(X), Delete(X), Create(X), то вы получите одно уведомление о создании X, одно — об удалении и еще одно — о создании, причем в указанном порядке.
- Для транзакций, которые содержат несколько операций, эти операции будут применены в порядке, в котором они были получены от пользователя в первичной реплике.
- При обработке хода выполнения с результатом false некоторые операции могут быть отменены. Для таких операций отмены будут создаваться уведомления и выполнен откат состояния реплики до стабильный точки. Одно важное отличие уведомлений об отмене — события с повторяющимися ключами будут агрегированы. Например, если упомянутая выше транзакция отменяется T1, то пользователь увидит одно уведомление для операции Delete(X).

## Дальнейшие действия

- [Краткое руководство по надежным службам Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Архивация и восстановление (аварийное восстановление) надежных служб](service-fabric-reliable-services-backup-restore.md)
- [Справочник разработчика по надежным коллекциям](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0629_2016-->