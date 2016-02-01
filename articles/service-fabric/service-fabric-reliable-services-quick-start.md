<properties
   pageTitle="Приступая к работе со службами Reliable Services | Microsoft Azure"
   description="Вводные сведения о создании приложения Service Fabric Microsoft Azure со службами с отслеживанием состояния и без него."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/15/2015"
   ms.author="vturecek"/>

# Начало работы со службами Reliable Services в Service Fabric

Приложение Azure Service Fabric содержит одну или несколько служб, которые выполняют код. В этом руководстве показано, как создавать приложения Service Fabric с отслеживанием состояния и без отслеживания состояния с помощью [Reliable Services](service-fabric-reliable-services-introduction.md).

## Создание службы без отслеживания состояния

Сейчас в облачных приложениях обычно используются службы без отслеживания состояния. Термин "без отслеживания состояния" означает, что сама служба не содержит данные, которым требуется надежное хранение или обеспечение высокого уровня доступности. Когда экземпляр службы без отслеживания состояния завершает работу, все данные о его внутреннем состоянии будут утрачены. Чтобы обеспечить высокую доступность и надежность в службах этого типа, состояния должны сохраняться во внешнее хранилище, например в таблицы Azure или базу данных SQL.

Запустите Visual Studio 2015 RC от имени администратора и создайте новый проект приложения Service Fabric с именем *HelloWorld*:

![Создание нового приложения Service Fabric с помощью диалогового окна "Создание проекта"](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Затем создайте проект службы без отслеживания состояния с именем *HelloWorldStateless*.

![Во втором диалоговом окне создайте проект службы без отслеживания состояния](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Теперь решение содержит два проекта:

 - *HelloWorld*. Это проект *приложения*, который содержит ваши *службы*. В нем также содержится манифест приложения, описывающий приложение, и ряд скриптов PowerShell для развертывания приложения.
 - *HelloWorldStateless*. Это проект службы. Он содержит реализацию службы без отслеживания состояния.


## Реализация службы

Откройте файл **HelloWorldStateless.cs** в проекте службы. В Service Fabric служба может выполнять любую бизнес-логику. API службы предоставляет две точки входа для кода.

 - Метод *RunAsync* с открытой точкой входа, в котором можно начать выполнение любой рабочей нагрузки, например долго выполняющихся вычислений.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Точка входа для обмена данными, в которой можно подключить любой стек связи, например веб-API ASP .NET. С ее помощью вы можете получать запросы от пользователей и других служб.

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

В этом учебнике рассматривается метод точки входа `RunAsync()`. Используя его, вы можете сразу же начать выполнение кода. Шаблон проекта включает пример реализации `RunAsync()`, который увеличивает показания счетчика.

> [AZURE.NOTE]Дополнительные сведения о работе со стеком связи см. в статье [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md).


### Метод RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancelServiceInstance)
{
    // TODO: Replace the following sample code with your own logic.

    int iterations = 0;
    // This service instance continues processing until the instance is terminated.
    while (!cancelServiceInstance.IsCancellationRequested)
    {

        // Log what the service is doing
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServiceInstance);
    }
}
```

Платформа вызывает этот метод, когда экземпляр службы размещен и готов к выполнению. Для службы без отслеживания состояния это означает, что экземпляр службы открыт. Токен отмены предоставляется для координации закрытия экземпляра вашей службы. В Service Fabric этот цикл открытия и закрытия экземпляра службы может выполняться много раз за все время существования службы. Это может происходить по ряду причин.

- Система перемещает экземпляры служб для распределения ресурсов.
- В коде возникают ошибки.
- Выполняется обновление приложения или системы.
- Возникает аппаратный сбой.

Этой оркестрацией управляет система для сохранения высокой доступности и правильного баланса службы.

`RunAsync()` выполняется в своей собственной задаче. Обратите внимание, что в приведенном выше фрагменте кода мы сразу перешли к циклу *while*. Нет необходимости планировать отдельную задачу для рабочей нагрузки. Отмена рабочей нагрузки является совместным действием, координированным предоставленным токеном отмены. Чтобы продолжать работу, система должна дождаться завершения задачи (успешного завершения, отмены или завершения вследствие ошибки). Когда система запрашивает отмену задачи, важно как можно быстрее подтвердить токен отмены, завершить все выполняющиеся операции и выйти из `RunAsync()`.

В этом примере службы без отслеживания состояния счетчик хранится в локальной переменной. Но так как это служба без отслеживания состояния, хранимое значение существует только для текущего жизненного цикла экземпляра службы. Если службу переместить или перезапустить, значение будет утеряно.

## Создание службы с отслеживанием состояния

Service Fabric представляет новый вид службы с отслеживанием состояния. Служба с отслеживанием состояния может надежно поддерживать состояние в самой службе (совмещая с кодом, который его использует). Service Fabric делает состояние высокодоступным без необходимости сохранения состояния во внешнее хранилище.

Чтобы преобразовать значение счетчика без учета состояния в высокодоступное и постоянное (даже при перемещении или перезапуске службы), нужно использовать службу с отслеживанием состояния.

В том же приложении *HelloWorld* можно добавить новую службу, щелкнув правой кнопкой мыши проект приложения и выбрав пункт **Добавить службу Service Fabric**.

![Добавление службы в приложение Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Выберите **Служба с отслеживанием состояния** и присвойте имя *HelloWorldStateful*. Нажмите кнопку **ОК**.

![Создание службы с отслеживанием состояния в Service Fabric с помощью диалогового окна "Создание проекта"](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Теперь в вашем приложении должно быть две службы: служба *HelloWorld* без отслеживания состояния и служба *HelloWorldStateful*с отслеживанием состояния.

Откройте **HelloWorldStateful.cs** в службе *HelloWorldStateful*, содержащей следующий метод RunAsync.

```C#
protected override async Task RunAsync(CancellationToken cancelServicePartitionReplica)
{
    // TODO: Replace the following sample code with your own logic.

    // Gets (or creates) a replicated dictionary called "myDictionary" in this partition.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    // This partition's replica continues processing until the replica is terminated.
    while (!cancelServicePartitionReplica.IsCancellationRequested)
    {

        // Create a transaction to perform operations on data within this partition's replica.
        using (var tx = this.StateManager.CreateTransaction())
        {

            // Try to read a value from the dictionary whose key is "Counter-1".
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

            // Log whether the value existed or not.
            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            // If the "Counter-1" key doesn't exist, set its value to 0
            // else add 1 to its current value.
            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            // Committing the transaction serializes the changes and writes them to this partition's secondary replicas.
            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is sent to this partition's secondary replicas.
            await tx.CommitAsync();
        }

        // Pause for one second before continuing processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### Метод RunAsync

Служба с отслеживанием состояния имеет такие же точки входа, как и служба без отслеживания состояния. Основное различие заключается в доступности Reliable Collections и диспетчера состояний, так как метод `RunAsync()` работает одинаково в службах с отслеживанием состояния и без него. Однако в службе с отслеживанием состояния платформа выполняет дополнительные действия от вашего имени перед выполнением `RunAsync()`. Эти действия могут включать подготовку диспетчера состояний и коллекций Reliable Collections к использованию.

### Reliable Collections и диспетчер состояний

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

*IReliableDictionary* — это реализация словаря, которая позволяет надежно хранить состояние службы. Она входит в состав встроенных коллекций [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) в Service Fabric. В Service Fabric с помощью Reliable Collections можно хранить данные непосредственно в службе без использования внешнего постоянного хранилища. Таким образом можно обеспечить высокую доступность данных. Service Fabric выполняет эту задачу путем автоматического создания нескольких *реплик* службы и управления ими. Кроме того, платформа предоставляет API-интерфейс, который упрощает управление этими репликами и переходы между их состояниями.

В Reliable Collections можно хранить любые типы объектов .NET, включая пользовательские типы. Необходимо только учитывать следующее.

 - Service Fabric делает состояние высокодоступным, *реплицируя* его между узлами и сохраняя на локальном диске. Это означает, что все объекты, хранящиеся в Reliable Collections, должны поддерживать *сериализацию*. По умолчанию в Reliable Collections для сериализации используется [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx). Поэтому при использовании сериализатора по умолчанию убедитесь, что ваши типы [поддерживаются сериализатором контрактов данных](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx).

 - Когда вы зафиксируете транзакции в Reliable Collections, объекты реплицируются и становятся высокодоступными. Объекты, сохраненные в Reliable Collections, хранятся в локальной памяти вашей службы. Это означает, что у вас есть локальная ссылка на объект.

    Очень важно не изменять локальные экземпляры этих объектов без обновления коллекции в транзакции, так как эти изменения не реплицируются автоматически.

Диспетчер состояний автоматически управляет коллекциями Reliable Collections. Просто запросите у диспетчера состояний имя коллекции в любое время и в любом расположении службы, и вы гарантированно получите ссылку. Мы не рекомендуем сохранять ссылки на экземпляры коллекции в переменные или свойства членов класса. Необходимо внимательно следить, чтобы ссылка указывала на экземпляр на протяжении всего жизненного цикла службы. Диспетчер состояний выполняет за вас эту работу. Кроме того, он оптимизирован для повторных визитов.

### Транзакционные и асинхронные операции

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

В коллекциях Reliable Collections повторяются многие операции, которые есть в их аналогах, `System.Collections.Generic` и `System.Collections.Concurrent`, включая LINQ. Однако операции в надежных коллекциях являются асинхронными. Это происходит потому, что операции записи в Reliable Collections *реплицируются*. Эти операции отправляются другим репликам службы на разных узлах для обеспечения высокого уровня доступности.

Они также поддерживают *транзакционные* операции, которые позволяют сохранять согласованное состояние для нескольких коллекций Reliable Collections. Например, вы можете вывести рабочий элемент из надежной очереди, выполнить с ним какую-либо операцию и сохранить результат в надежном словаре — и это все в рамках одной транзакции. Такая операция называется атомарной. Предусматривается, что либо вся операция завершится успешно, либо она не завершится вообще. Если ошибка возникнет после выведения элемента из очереди, но до сохранения результата, будет выполнен откат всей транзакции, а элемент останется в очереди для обработки.

## Выполнение приложения

Теперь вернемся к приложению *HelloWorld*. Теперь вы можете построить и развернуть свои службы. Когда вы нажмете клавишу **F5**, приложение будет построено и развернуто в вашем локальном кластере.

Запустив службы, вы можете просмотреть созданные события трассировки событий Windows в окне **События диагностики**. Обратите внимание, что в приложении отображаются события как из службы без отслеживания состояния, так и из службы с отслеживанием состояния. Поток можно приостановить, нажав кнопку **Приостановить**. Затем можно просмотреть подробные сведения о сообщении, развернув его.

>[AZURE.NOTE]Перед запуском приложения убедитесь, что кластер локальной разработки запущен. Сведения о настройке локальной среды см. в [руководстве по началу работы](service-fabric-get-started.md).

![Просмотр событий диагностики в Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Дальнейшие действия

[Отладка приложения Service Fabric с помощью Visual Studio](service-fabric-debugging-your-application.md)

[Приступая к работе со службами веб-API Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md)

[Дополнительные сведения о надежных коллекциях](service-fabric-reliable-services-reliable-collections.md)

[Развертывание приложения](service-fabric-deploy-remove-applications.md)

[Обновление приложения](service-fabric-application-upgrade.md)

[Справочник разработчика по надежным службам](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0121_2016-->