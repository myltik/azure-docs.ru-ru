---
title: "Обзор интерфейсов API концентраторов событий Azure | Документация Майкрософт"
description: "Сводные сведения о некоторых ключевых клиентских API .NET концентраторов событий."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7f3b6cc0-9600-417f-9e80-2345411bd036
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: c68b5cfadc3e98e15c119b3717c8098887ca67d0
ms.openlocfilehash: e8c97689f84d3bedb312ec4443427181a542e95b


---
# <a name="event-hubs-api-overview"></a>Общие сведения об API концентраторов событий
В этой статье перечислены некоторые ключевые клиентские API .NET концентраторов событий. Существуют две категории: API управления и API среды выполнения. API среды выполнения состоят из всех операций, необходимых для отправки и получения сообщения. Операции управления позволяют управлять состоянием сущности концентраторов событий путем создания, обновления и удаления сущностей.

Сценарии мониторинга распространяются как на управление, так и на среду выполнения. Подробную справочную документацию по API .NET см. в руководстве по [служебной шине .NET](/dotnet/api) и [API EventProcessorHost](/dotnet/api).

## <a name="management-apis"></a>API управления
Для выполнения указанных ниже операций управления требуется разрешение на **управление** пространством имен концентраторов событий.

### <a name="create"></a>Создание
```csharp
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### <a name="update"></a>Блокировка изменений
```csharp
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### <a name="delete"></a>Удалить
```csharp
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## <a name="run-time-apis"></a>Интерфейсы API среды выполнения
### <a name="create-publisher"></a>Создание издателя
```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>Публикация сообщения
```csharp
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

### <a name="create-consumer"></a>Создание потребителя
```csharp
// Create the Event Hubs client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
EventHubConsumerGroup defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Использование сообщения
```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>API узла обработчика событий
Эти API обеспечивают отказоустойчивость рабочих процессов, которые могут стать недоступными, и распределяют сегменты между всеми имеющимися исполнителями.

```csharp
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

Интерфейс [IEventProcessor](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) определяется следующим образом.

```csharp
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

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о сценариях концентраторов событий см. в разделах, ссылки на которые указаны ниже.

* [Что такое концентраторы событий Azure?](event-hubs-what-is-event-hubs.md)
* [Обзор концентраторов событий](event-hubs-overview.md)
* [Руководство по программированию концентраторов событий](event-hubs-programming-guide.md)
* [Примеры кода концентраторов событий](http://code.msdn.microsoft.com/site/search?query=event hub&f\[0\].Value=event hubs&f\[0\].Type=SearchText&ac=5)

Ссылки на API-интерфейсы .NET:

* [Справочник по служебной шине и API .NET концентраторов событий](/dotnet/api)
* [Справочник по API узла обработчика событий](/dotnet/api)




<!--HONumber=Nov16_HO4-->


