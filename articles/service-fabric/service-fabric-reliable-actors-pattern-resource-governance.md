<properties
   pageTitle="Конструктивный шаблон управления ресурсами | Microsoft Azure"
   description="Конструктивный шаблон, показывающий, как можно использовать субъекты Reliable Actors Service Fabric для моделирования приложения, которое требуется масштабировать с использованием ограниченных ресурсов."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Конструктивный шаблон Reliable Actors: управление ресурсами

Этот шаблон и связанные сценарии хорошо знакомы корпоративным и другим разработчикам, у которых ограничены ресурсы на локальной площадке или в облаке, немедленное масштабирование которых невозможно. Они также знакомы разработчикам, которым приходится отправлять крупномасштабные приложения и данные в облако.

В корпоративной среде такие ограниченные ресурсы, например базы данных, выполняются на вертикально масштабируемом оборудовании. Любой пользователь с большим опытом работы в корпоративной среде знает, что это распространенная ситуация на локальных площадках. Даже в масштабе облака разработчики сталкиваются с ситуацией, когда облачная служба пытается превысить ограничение в 64 000 TCP-подключений между кортежем адреса и порта. Это также происходит во время попыток установить подключение к облачной базе данных, которая ограничивает число одновременных подключений.

В прошлом эта проблема обычно решалась путем регулирования через промежуточный слой на основе сообщений или с помощью специально построенных механизмов организации пулов и фасадов. Однако это достаточно сложная задача, особенно если требуется масштабировать средний уровень и при этом поддерживать нужное количество подключений. Это хрупкое и сложное решение.

Как и шаблон смарт-кэша, в этом шаблоне используется несколько сценариев и клиентов, у которых уже есть работающие системы с ограниченными ресурсами. В этих системах нужно масштабировать не только службы, но и их состояние в памяти, а также сохраненное состояние в постоянном хранилище.

Такой сценарий показан на схеме ниже:

![Субъекты без отслеживания состояния, секционирование и ограниченные ресурсы][1]

## Моделирование сценариев кэша с субъектами

Вы можете моделировать доступ к ресурсам, используя один или несколько субъектов, выполняющих роль прокси-объектов для ресурса или группы ресурсов (например, подключения). Затем можно управлять ресурсом напрямую с помощью отдельных субъектов или использовать субъект координации, управляющий субъектами ресурсов.

Чтобы уточнить концепцию, мы рассмотрим распространенную потребность в работе с секционированным (сегментированным) хранилищем, которая связана с задачами производительности и масштабируемости. Первый вариант довольно прост. Вы можете воспользоваться статической функцией для сопоставления и разрешения своих субъектов для нижестоящих ресурсов. Такая функция может возвращать, например, строку подключения с заданными входными данными. Реализация этой функции зависит от вас. Но у этого подхода есть и свои недостатки, такие как статическое сходство, которое усложняет перераспределение ресурсов или переназначение субъектов ресурсам.

Ниже приведен простой пример. Мы выполняем арифметические операции над абсолютными значениями чисел, чтобы определить имя базы данных с помощью идентификатора **userId**. Кроме того, мы определяем сервер базы данных с помощью параметра **region**.

### Пример кода для управления ресурсами — статическое разрешение

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

Это простое, но не очень гибкое решение. Теперь рассмотрим более совершенный и удобный подход.

Во-первых, мы моделируем сходство между физическими ресурсами и субъектами. Это выполняется с помощью субъекта, который называется **сопоставителем**. Он сопоставляет пользователей, логические разделы и физические ресурсы. Сопоставитель хранит свои данные в постоянном хранилище, но благодаря возможности кэширования их можно легко найти. Как мы обсуждали на [примере шаблона смарт-кэша "Обменный курс"](service-fabric-reliable-actors-pattern-smart-cache.md), сопоставитель может заранее получать последние сведения с помощью таймера. Как только субъект пользователя распознает ресурс, который необходимо использовать, он кэширует его в локальную переменную **\_resolution** и использует ресурс на протяжении своего существования.

Мы выбрали решение на основе поиска (см. рисунок ниже) вместо простого хэширования или хэширования диапазона из-за гибкости в операциях, которую обеспечивает это решение. Такие операции могут включать внутреннее или внешнее масштабирование либо перемещение пользователя с одного ресурса на другой.

![Решение "Сопоставитель поиска"][2]

На приведенном выше рисунке видно, что субъект B23 сначала распознает ресурс (разрешение) **DB1**, а затем кэширует его. В последующих операциях теперь можно использовать кэшированную переменную resolution для доступа к ограниченному ресурсу. Поскольку субъекты поддерживает однопоточное выполнение, разработчикам больше не нужно беспокоиться об одновременном доступе к ресурсу. См. субъекты сопоставителя и пользователя в следующем примере кода.

### Пример кода для управления ресурсами — Resolver

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : StatefulActor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

#### Управление ресурсами — пример Resolver

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : StatefulActor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## Доступ к ресурсам с ограниченной пропускной способностью

Теперь рассмотрим другой пример — монопольный доступ к ценным ресурсам, таким как базы данных, учетные записи хранения и файловые системы с ограниченной пропускной способностью. В этом случае мы будем обрабатывать события с помощью субъекта с именем EventProcessor. Этот субъект отвечает за обработку и сохранение событий. В нашем случае события для простоты сохраняются в CSV-файл. Хотя мы можем следовать описанному выше подходу с секционированием для масштабирования ресурсов, нам по-прежнему нужно решать проблемы, связанные с параллелизмом. Мы выбрали пример на основе файла, чтобы проиллюстрировать этот момент, так как запись в один файл из нескольких субъектов связана с упомянутыми проблемами. Чтобы устранить эту проблему, мы вводим другой субъект с именем EventWriter, который монопольно владеет ограниченными ресурсами. Сценарий изображен ниже:

![Запись и обработка событий с помощью субъектов EventWriter и EventProcessor][3]

Мы помечаем субъекты EventProcessor как рабочие роли без сохранения состояния, что позволяет среде выполнения при необходимости масштабировать их в кластере. Обратите внимание, что мы не используем для этих субъектов никакие идентификаторы с приведенного выше рисунка. Субъекты без сохранения состояния формируют пул рабочих ролей, поддерживаемых средой выполнения.

В приведенном ниже примере кода субъект EventProcessor выполняет две функции. Сначала он решает, какой из субъектов EventWriter (и, следовательно, ресурсов) нужно использовать, а затем вызывает выбранный субъект для записи обработанного события. Чтобы упростить пример, в качестве идентификатора для субъекта EventWriter мы выбрали "Тип событий". То есть для данного типа событий будет один и только один EventWriter, обеспечивающий однопоточный и монопольный доступ к ресурсу.

### Пример кода для управления ресурсами — EventProcessor

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : StatelessActor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

Теперь рассмотрим субъект EventWriter. Он управляет монопольным доступом к ограниченному ресурсу — в данном случае к файлу и записи событий в него. Это все, что он делает.

### Пример кода для управления ресурсами — EventWriter

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

Наличие одного субъекта, ответственного за ресурс, позволяет добавлять такие возможности, как буферизация. Вы можете буферизировать входящие события и периодически записывать их по таймеру или при заполнении буфера. Следующий пример кода является простым примером на основе таймера.

### Пример кода для управления ресурсами — субъект EventWriter с буфером

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

Хотя приведенный выше код будет работать нормально, клиенты не узнают, попало ли событие в базовое хранилище. Чтобы реализовать буферизацию с уведомлением клиентов о действиях с их запросами, следующий подход позволяет клиентам дождаться, когда их событие будет записано в CSV-файл.

### Пример кода для управления ресурсами — асинхронная пакетная обработка

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

Мы будем использовать этот класс для создания и поддержания списка незавершенных задач (для блокирования клиентов). Мы выполним их за один проход после записи буферизированных событий в хранилище.

В классе EventWriter необходимо выполнить три действия: пометить класс субъекта как Reentrant, вернуть результат **SubmitNext()** и сбросить таймер. См. измененный код ниже.

### Пример кода для управления ресурсами — буферизация с асинхронной пакетной обработкой

```csharp
public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

Простота этого подхода дает неверное представление о корпоративных ресурсах. Используя эту архитектуру, вы получаете следующие преимущества:

* Независимая от расположения адресация ресурсов.
* Настраиваемый размер пула на основе простого изменения числа субъектов, действующих от имени ресурса.
* Использование пула, координируемое на стороне клиента (как на изображении) или на стороне сервера (просто представьте один субъект перед каждым из изображенных пулов).
* Масштабируемое добавление пулов (просто добавьте субъекты, представляющие новый ресурс).
* Субъекты, которые могут кэшировать результаты из внутренних ресурсов по требованию или выполнять предварительное кэширование с помощью таймера, как было показано ранее. Это уменьшает необходимость обращаться к внутренним ресурсам.
* Эффективную асинхронную отправку.
* Среда программирования, которая будет знакома любому разработчику, а не только специалистам в области ПО промежуточного слоя.

Этот шаблон очень часто встречается в сценариях, где разработчики обладают ограниченными ресурсами, которые необходимо учитывать при разработке. Также разработчики используют эти сценарии, когда строят крупные масштабируемые системы.


## Дальнейшие действия

[Конструктивный шаблон Reliable Actors: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)

[Конструктивный шаблон Reliable Actors: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Конструктивный шаблон Reliable Actors в Service Fabric: построение служб с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Конструктивный шаблон Reliable Actors: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Конструктивный шаблон Reliable Actors: некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=AcomDC_0121_2016-->