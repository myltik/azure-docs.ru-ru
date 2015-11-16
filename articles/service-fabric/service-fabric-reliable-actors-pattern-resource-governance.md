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
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Шаблон разработки надежных субъектов: управление ресурсами

Этот шаблон и связанные сценарии легко распознаются корпоративными или другими разработчиками, у которых ограничены ресурсы на локальной площадке или в облаке, масштабирование которых невозможно или которые хотят отправить крупномасштабные приложения и данные в облако.

В корпоративной среде такие ограниченные ресурсы, например базы данных, выполняются на вертикально масштабируемом оборудовании. Любой пользователь с большим опытом работы в корпоративной среде знает, что это распространенная ситуация на локальных площадках. Даже в масштабе облака мы наблюдали эту ситуацию при попытке облачной службы превысить лимит в 64 тыс. подключений TCP между парами адресов и портов или при попытке подключения к облачной базе данных, ограничивающей число одновременных подключений.

Обычно в прошлом эта проблема решалась путем регулирования через промежуточный слой на основе сообщений или путем специально построенных механизмов организации пулов и фасадов. Это достаточно сложная задача, особенно если требуется масштабировать средний уровень и при этом поддерживать нужное количество подключений. Это хрупкая и сложная система.

Как и шаблон «Смарт-кэш», данный шаблон охватывает несколько сценариев и клиентов, у которых уже есть работающие системы с ограниченными ресурсами. Они создают системы, где требуется масштабировать не только службы, но и их состояние в памяти, а также сохраненное состояние в постоянном хранилище.

Такой сценарий показан на схеме ниже:

![][1]

## Моделирование сценариев кэша с субъектами

По сути мы моделируем доступ к ресурсам в качестве одного или нескольких субъектов, выполняющих роль прокси-серверов (например, подключений) для ресурса или группы ресурсов. Затем можно напрямую управлять ресурсом с помощью отдельных субъектов или использовать субъект координации, управляющий субъектами ресурсов. Для большей конкретности мы рассмотрим распространенную потребность в работе с секционированным (сегментированным) уровнем хранения, которая связана с задачами производительности и масштабируемости. Наш первый вариант очень прост: можно воспользоваться статической функцией для сопоставления и разрешения наших субъектов в виде нижестоящих ресурсов. Такая функция может возвращать, например, строку подключения с заданными входными данными. Реализация этой функции полностью зависит от нас. Конечно, у этого подхода есть и свои недостатки, такие как статическое сходство, которое усложняет перераспределение ресурсов или переназначение субъекта на ресурсы. Ниже приведен очень простой пример. Мы вычисляем остаток от деления для определения имени базы данных с помощью идентификатора пользователя (userId) и идентифицируем сервер базы данных по региону.

## Образец кода для управления ресурсами — статическое разрешение

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

Это просто, но не очень гибко. Теперь рассмотрим более продвинутый и полезный подход. Во-первых, мы моделируем сходство между физическими ресурсами и субъектами. Это осуществляется посредством субъекта, называемого «Сопоставитель» (Resolver), который понимает сопоставление между пользователями, логическими разделами и физическими ресурсами. Сопоставитель хранит свои данные в постоянном хранилище, однако они кэшируются для упрощения поиска. Как мы видели в примере шаблона смарт-кэша «Обменный курс», сопоставитель может проактивно получать последние сведения с помощью таймера. Как только субъект пользователя распознает ресурс, который необходимо использовать, он кэширует его в локальную переменную с именем \_resolution и использует ее в течение времени своего существования. Мы выбрали решение на основе поиска (показанное ниже) вместо простого хэширования или хэширования диапазона благодаря гибкости в таких операциях, как внутреннее или внешнее масштабирование либо перемещение пользователя с одного ресурса на другой.

![][2]

На приведенном выше рисунке видно, что субъект B23 сначала распознает ресурс (resolution) DB1, а затем кэширует его. В последующих операциях теперь можно использовать кэшированную переменную resolution для доступа к ограниченному ресурсу. Поскольку субъекты поддерживает однопоточное выполнение, разработчикам больше не нужно беспокоиться об одновременном доступе к ресурсу. Субъекты «Пользователь» и «Сопоставитель» выглядят следующим образом:

## Образец кода для управления ресурсами — сопоставитель

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


public class User : Actor<UserState>, IUser
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

Управление ресурсами — пример сопоставителя

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

public class Resolver : Actor<ResolverState>, IResolver
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

Теперь рассмотрим другой пример — монопольный доступ к ценным ресурсам, таким как БД, учетные записи хранилища и файловые системы с ограниченной пропускной способностью. Наш сценарий таков: мы хотели бы обрабатывать события с помощью субъекта с именем EventProcessor, который отвечает за обработку и сохранение события, в данном случае в CSV-файл для простоты. Хотя мы можем следовать описанному выше подходу с секционированием для масштабирования ресурсов, нам по-прежнему придется решать проблемы параллелизма. Вот почему мы выбрали пример на основе файла, чтобы проиллюстрировать этот конкретный момент — запись в один файл несколькими субъектами вызовет проблемы одновременного доступа. Чтобы устранить проблему, мы представляем другой субъект с именем EventWriter, который монопольно владеет ограниченными ресурсами. Сценарий изображен ниже:

![][3]

Субъекты EventProcessor мы помечаем как «Исполнители без сохранения состояния», что позволяет среде выполнения при необходимости масштабировать их по кластеру. Поэтому мы не используем для этих субъектов никакие идентификаторы с приведенной выше иллюстрации. Иными словами, субъекты без сохранения состояния представляют собой пул исполнителей, поддерживаемых средой выполнения. В приведенном ниже образце кода субъект EventProcessor выполняет две функции: сначала решает, какой EventWriter (и таким образом ресурс) использовать, затем вызывает выбранный субъект для записи обработанного события. Для простоты в качестве идентификатора для субъекта EventWriter мы выбрали «Тип событий». Иными словами, для данного типа событий будет один и только один EventWriter, обеспечивающий однопоточный и монопольный доступ к ресурсу.

## Образец кода для управления ресурсами — обработчик событий

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : Actor, IEventProcessor
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

Теперь рассмотрим субъект EventWriter. Он фактически ничего больше не делает, кроме управления монопольным доступом к ограниченному ресурсу, в данном случае к файлу и записи событий в него.
## Образец кода для управления ресурсами — субъект записи событий

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

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
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

Наличие одного субъекта, ответственного за ресурс, позволяет нам добавлять дополнительные возможности, например буферизацию. Мы можем буферизировать входящие события и периодически их записывать по таймеру или при заполнении буфера. Ниже приведен простой пример на основе таймера:
## Образец кода для управления ресурсами — субъект записи событий с буфером

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    public override Task OnActivateAsync()
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

Хотя приведенный выше код будут работать нормально, клиенты не узнают, попало ли событие в базовое хранилище. Чтобы реализовать буферизацию с уведомлением клиентов о том, что происходит с их запросами, мы представляем следующий подход, при котором клиенты будут дожидаться, пока их событие записывается в CSV-файл.

## Образец кода для управления ресурсами — асинхронная пакетная обработка

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

Мы будем использовать этот класс для создания и поддержания списка незавершенных задач (для блокирования клиентов) и выполнения их за один раз после записи буферизированных событий в хранилище. В классе EventWriter необходимо выполнить три действия: пометить класс субъекта как Reentrant, вернуть результат SubmitNext() и сбросить (Flush) таймер. Вот измененный код:

## Образец кода для управления ресурсами — буферизация с асинхронной пакетной обработкой

```csharp
public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    public override Task OnActivateAsync()
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

Кажется простым? Так и есть. Но простота искажает мощность предприятия. При такой архитектуре мы получаем следующие преимущества.

* Независимую от расположения адресацию ресурсов.
* Настраиваемый размер пула на основе простого изменения числа субъектов, действующих от имени ресурса.
* Координируемое на стороне клиента использование пула (как изображено) или на стороне сервера (просто представьте один субъект перед каждым из изображенных пулов).
* Масштабируемое добавление пулов (просто добавьте субъекты, представляющие новый ресурс).
* Субъект (как мы показали выше) может кэшировать результаты из внутреннего ресурса по запросу или выполнять предварительное кэширование по таймеру, уменьшая необходимость попадания во внутренний ресурс.
* Эффективную асинхронную отправку.
* Среду программирования, которая будет знакома любому разработчику, а не только специалистам по промежуточному слою.

Этот шаблон очень часто встречается в сценариях, где разработчики обладают ограниченными ресурсами, которые необходимо учитывать при разработке, или строят крупные масштабируемые системы.


## Дальнейшие действия

[Шаблон: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)

[Шаблон: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Шаблон: построение службы с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Шаблон: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=Nov15_HO2-->