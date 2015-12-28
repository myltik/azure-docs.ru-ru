<properties
   pageTitle="Начало работы с надежными службами | Microsoft Azure"
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

# Начало работы с надежными службами Service Fabric Microsoft Azure

Приложение Service Fabric содержит одну или несколько служб, которые выполняют код. В этом руководстве показано, как создавать приложения Service Fabric с отслеживанием состояния и без его учета с помощью [Reliable Services](service-fabric-reliable-services-introduction.md).

## Создание службы без отслеживания состояния

Сейчас в облачных службах преимущественно используется служба без отслеживания состояния. Служба без отслеживания состояния сама по себе не содержит данных, требующих надежного хранения или высокой доступности. Проще говоря, если экземпляр службы без отслеживания состояния завершает работу, все его внутреннее состояние теряется. Для обеспечения высокой доступности и надежности в службах этого типа состояния должны сохраняться во внешнее хранилище, например таблицы Azure или базы данных SQL.

Запустите Visual Studio RC 2015 от имени **администратора** и создайте новый проект **приложения Service Fabric** с именем *HelloWorld*.

![Создание нового приложения Service Fabric с помощью диалогового окна "Создание проекта"](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Затем создайте проект **службы без отслеживания состояния** с именем *HelloWorldStateless*.

![Во втором диалоговом окне создайте службу без отслеживания состояния.](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Теперь решение содержит два проекта.

 + **HelloWorld** — это проект *приложения*, который содержит ваши *службы*. В нем также содержится манифест приложения, описывающий приложение, и ряд скриптов PowerShell для развертывания приложения.
 + **HelloWorldStateless** — это проект службы, который содержит реализацию службы без отслеживания состояния.


## Реализация службы

Откройте файл **HelloWorldStateless.cs** в проекте службы. В Service Fabric служба может выполнять любую бизнес-логику. API службы предоставляет две точки входа для кода.

 - Вызывается метод *RunAsync* с открытой точкой входа, в котором можно начать выполнение любой рабочей нагрузки, например длительных вычислений.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Точка входа связи, в которой можно подключить стек связи по выбору, например веб-API, в котором вы можете получать запросы от пользователей или других служб.

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

В этом руководстве рассматривается метод точки входа `RunAsync()`, в котором можно немедленно начать выполнение кода. Шаблон проекта включает пример реализации `RunAsync()`, который инкрементирует счетчик.

> [AZURE.NOTE]Дополнительные сведения о работе со стеком связи см. в статье [Начало работы со службами веб API Microsoft Azure Service Fabric с собственным хостом OWIN](service-fabric-reliable-services-communication-webapi.md)


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

Платформа вызывает этот метод, когда экземпляр вашей службы размещен и готов к выполнению. Для службы без отслеживания состояния это означает, что экземпляр службы открыт. Токен отмены предоставляется для координации закрытия экземпляра вашей службы. В Service Fabric этот цикл открытия/закрытия экземпляра службы может происходить несколько раз за время существования службы как целого по различным причинам, включая следующие:

- Система может перемещать экземпляры ваших служб для распределения ресурсов.
- При выполнении вашего кода произошли ошибки.
- Выполняется обновление приложения или системы.
- При возникновении аппаратного сбоя.

Эта оркестрация управляется системой с целью сохранения высокой доступности и надлежащей сбалансированности службы.

`RunAsync()` выполняется в своей собственной **задаче**. Обратите внимание, что в приведенном выше фрагменте кода мы переходим прямо в цикл **while**, поэтому планировать отдельную задачу для рабочей нагрузки не нужно. Отмена рабочей нагрузки является совместным действием, координированным предоставленным токеном отмены. Для продолжения работы система будет ожидать завершения задачи (успешного завершения, отмены или завершения вследствие ошибки). Когда система запрашивает отмену задачи, **важно** как можно быстрее подтвердить токен отмены, завершить все выполняющиеся операции и выйти из `RunAsync()`.

В этом примере службы без отслеживания состояния счетчик хранится в локальной переменной. Но, поскольку это служба без отслеживания состояния, хранимое значение существует только в текущем жизненном цикле экземпляра службы, в котором оно находится. Если службу переместить или перезапустить, значение будет утеряно.

## Создание службы с отслеживанием состояния

Service Fabric представляет новый вид службы с отслеживанием состояния — это служба, которая может надежно поддерживать свое состояние, вместе с кодом, который ее использует. Service Fabric делает ваше состояние высокодоступным без необходимости сохранения состояния во внешнее хранилище.

Чтобы значение нашего счетчика было высокодоступным и постоянным даже в случае перемещения или перезапуска службы, нужно использовать службу с отслеживанием состояния.

В том же приложении **HelloWorld** добавьте новую службу, щелкнув правой кнопкой мыши проект приложения и выбрав пункт **Новая служба Fabric**.

![Добавление службы в приложение Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Выберите **Служба с отслеживанием состояния Service Fabric** и назовите его «HelloWorldStateful». Щелкните **Добавить**.

![Создание службы с отслеживанием состояния Service Fabric с помощью диалогового окна "Создание проекта"](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Теперь в вашем приложении должно быть две службы: служба *HelloWorld* без отслеживания состояния и служба *HelloWorldStateful*с отслеживанием состояния.

Откройте **HelloWorldStateful.cs** в *HelloWorldStateful*, содержащий следующий метод `RunAsync`.

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

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### Метод RunAsync

Служба с отслеживанием состояния имеет такие же точки входа, как и служба без отслеживания состояния. Их основное различие заключается в доступности *Надежных коллекций* и *Диспетчера состояний*. В обеих службах метод `RunAsync()` работает одинаково, только в службе без отслеживания состояния перед выполнением `RunAsync()` платформа проделывает некоторую работу от вашего имени, например проверяет готовность к использованию *Диспетчера состояний* и *Надежных коллекций*.

### Надежные коллекции и диспетчер состояний

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** является реализацией словаря, которая позволяет надежно хранить состояние службы. Она является частью [надежных коллекций](service-fabric-reliable-services-reliable-collections.md) Service Fabric. В надежных коллекциях Service Fabric можно хранить данные непосредственно в службе без использования внешнего постоянного хранилища, что делает данные высокодоступными. В Service Fabric это достигается созданием нескольких *реплик* службы и управлением ими с помощью API, который упрощает управление этими репликами и переходы между их состояниями.

В надежных коллекциях можно хранить любые типы объектов .NET, включая ваши пользовательские типы, с некоторыми предостережениями.

 1. Service Fabric делает ваше состояние высокодоступным, *реплицируя* его между узлами и сохраняя на локальном диске. Это означает, что все, что хранится в надежной коллекции, должно *сериализоваться*. По умолчанию надежные коллекции используют [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) для сериализации, поэтому при использовании сериализатора по умолчанию убедитесь, что ваши типы [поддерживаются сериализатором контракта данных](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx).

 2. После фиксации транзакции в надежной коллекции объекты реплицируются и получают высокий уровень доступности. Объекты, сохраненные в надежной коллекции, хранятся в локальной памяти вашей службы, а это означает, что у вас есть локальная ссылка на объект.

    Очень важно не изменять локальные экземпляры этих объектов без выполнения операции обновления в надежной коллекции в транзакции, так как эти изменения не реплицируются автоматически.

*StateManager* управляет надежными коллекциями. Запросите у StateManager имя надежной коллекции в любое время в любом месте службы, чтобы получить ссылку. Мы не рекомендуем сохранять ссылки на экземпляры надежной коллекции в переменных или свойствах членов класса, так как необходимо очень внимательно следить, чтобы ссылка была направлены на экземпляр на протяжении всего жизненного цикла службы. StateManager выполняет за вас эту работу, так как он оптимизирован для повторных визитов.

### Транзакционные и асинхронные операции

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

В надежных коллекциях повторяются многие операции, которые есть в их аналогах, `System.Collections.Generic` и `System.Collections.Concurrent`, включая LINQ. Однако операции в надежных коллекциях являются асинхронными. Это происходит потому, что операции записи в надежных коллекциях *реплицируются*, то есть отправляются другим репликам службы на разных узлах для обеспечения высокого уровня доступности.

Они также поддерживают *транзакционные* операции, которые позволяют поддерживать нескольких надежных коллекций в одинаковом состоянии. Например, вы можете исключить рабочий элемент из надежной очереди, выполнить над ним какую-либо операцию и сохранить результат в надежном словаре, и это все в пределах одной транзакции. Такая операция называется атомарной, она предусматривает, что вся операция завершится успешно, либо совсем не завершится. Если ошибка возникнет после удаления элемента из очереди, но до сохранения результата, вся транзакция будет откатана, и элемент останется в очереди для обработки.

## Выполнение приложения

Вернемся к приложению *HelloWorld*. Теперь вы можете построить и развернуть свои службы. Нажмите клавишу **F5**, и приложение будет построено и развернуто в вашем локальном кластере.

После запуска службы можно увидеть созданные события трассировки событий Windows в окне **События диагностики**. Обратите внимание, что в приложении отображаются события из службы без отслеживания состояния и с отслеживанием состояния. Поток можно приостановить нажатием кнопки *Приостановить* и просмотреть сведения о сообщении, развернув его.

>[AZURE.NOTE]Перед запуском приложения убедитесь, что кластер локальной разработки запущен. Чтобы получить настройки локальной среды, см. [Руководство по началу работы](service-fabric-get-started.md).

![Просмотр событий диагностики в Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Дальнейшие действия

[Отладка приложения Service Fabric в Visual Studio](service-fabric-debugging-your-application.md)

[Начало работы со службами веб API Microsoft Azure Service Fabric с собственным хостом OWIN](service-fabric-reliable-services-communication-webapi.md)

[Дополнительные сведения о надежных коллекциях](service-fabric-reliable-services-reliable-collections.md)

[Развертывание приложения](service-fabric-deploy-remove-applications.md)

[Обновление приложения](service-fabric-application-upgrade.md)

[Справочник разработчика по надежным службам](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_1217_2015-->