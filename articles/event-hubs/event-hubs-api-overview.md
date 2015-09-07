<properties 
   pageTitle="Общие сведения об API концентраторов событий"
	description="Сводные сведения о некоторых ключевых клиентских API .NET концентраторов событий."
	services="event-hubs"
	documentationCenter="na"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>
<tags 
   ms.service="event-hubs"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd"
	ms.date="07/10/2015"
	ms.author="sethm"/>

# Общие сведения об API концентраторов событий

В этой статье перечислены некоторые ключевые клиентские API .NET концентраторов событий. Существует две категории: API управления и API среды выполнения. API среды выполнения состоят из всех операций, необходимых для отправки и получения сообщения. Операции управления позволяют управлять состоянием сущности концентраторов событий путем создания, обновления и удаления сущностей.

Сценарии мониторинга распространяются как на управление, так и на среду выполнения. Подробную справочную документацию по API .NET см. в разделах [Библиотека классов .NET](https://msdn.microsoft.com/library/jj933431.aspx) и [API EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.aspx).

## API управления

Для выполнения указанных ниже операций управления требуется разрешение на **управление** пространством имен служебной шины.

### Создание

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### Блокировка изменений

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### Удалить

```
namespaceManager.DeleteEventHubAsync("event hub name").Wait();
```

## API среды выполнения

### Создание издателя

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("event hub name");
```

### Публикация сообщения

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### Создание потребителя

```
// Create the Event Hub client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default subscriber group
EventHubSubscriberGroup defaultSubscriberGroup = eventHubClient.GetDefaultSubscriberGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### Использование сообщения

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## API узла обработчика событий

Эти API-интерфейсы обеспечивают отказоустойчивость рабочих процессов, которые могут стать недоступными, и распространяют сегменты между всеми имеющимися исполнителями.

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.
string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

Интерфейс [IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx) определяется следующим образом:

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## Дальнейшие действия

Дополнительные сведения о сценариях концентраторов событий см. в разделах, ссылки на которые указаны ниже.

- [Руководство по программированию концентраторов событий](event-hubs-programming-guide.md)
- [Обзор концентраторов событий](event-hubs-overview.md)
- [Примеры кода концентраторов событий](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)

Ссылки на API-интерфейсы .NET:

- [Справочник по служебной шине и API .NET концентраторов событий](https://msdn.microsoft.com/library/jj933424.aspx)
- [Справочник по API узла обработчика событий](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)

<!---HONumber=August15_HO9-->