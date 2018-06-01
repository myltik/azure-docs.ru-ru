---
title: Отслеживание работоспособности Центра Интернета вещей Azure | Документация Майкрософт
description: Использование Azure Monitor и службы "Работоспособность ресурсов Azure" для наблюдения за Центром Интернета вещей и быстрой диагностики неполадок
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: bf6202b002aaf6d89a30c7c653fdcee00cb50290
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202226"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Мониторинг работоспособности Центра Интернета вещей Azure и быстрая диагностика неполадок

Компании, внедряющие Центр Интернета вещей Azure, ожидают надежной работы ресурсов. Чтобы помочь вам внимательно отслеживать действия, Центр Интернета вещей полностью интегрирован с [Azure Monitor][lnk-AM] и службой [Работоспособность ресурсов Azure][lnk-ARH]. Эти две службы работают совместно, чтобы предоставить необходимые данные для обеспечения работоспособности решений Центра Интернета вещей. 

Azure Monitor является единым источником мониторинга и ведения журнала для всех служб Azure. Вы можете отправлять журналы, которые генерирует Azure Monitor, в Log Analytics, концентраторы событий или службу хранилища Azure для пользовательской обработки. Параметры метрик и диагностик Azure Monitor позволяют отслеживать производительность ресурсов в режиме реального времени. Прочтите эту статью, чтобы узнать, как [пользоваться Azure Monitor](#use-azure-monitor) с помощью Центра Интернета вещей. 

Служба "Работоспособность ресурсов Azure" помогает выполнять диагностику и получать необходимую поддержку, если неполадки Azure влияют на ресурсы. В настраиваемых панелях мониторинга отображаются сведения о текущих и прошлых состояниях работоспособности Центра Интернета вещей. Прочтите эту статью, чтобы узнать, как [использовать службу "Работоспособность ресурсов Azure"](#use-azure-resource-health) с Центром Интернета вещей. 

Помимо интеграции с этими двумя службами, Центр Интернета вещей также предоставляет собственные метрики, которые можно использовать для оценки состояния ресурсов Интернета вещей. Дополнительные сведения см. в статье [Общие сведения о метриках Центра Интернета вещей][lnk-metrics].

## <a name="use-azure-monitor"></a>Использование Azure Monitor

Azure Monitor предоставляет сведения диагностики на уровне ресурсов, что позволяет отслеживать операции, выполняемые в рамках Центра Интернета вещей. 

Параметры диагностики Azure Monitor заменяют операции мониторинга Центра Интернета вещей. Если вы сейчас используете мониторинг операций, необходимо перенести рабочие процессы. Дополнительную информацию см. в статье [Перенос Центра Интернета вещей для перехода с операций мониторинга к параметрам диагностики][lnk-migrate].

Дополнительные сведения о конкретных метриках и событиях, за которыми следит Azure Monitor, см. в статье [Метрики, поддерживаемые Azure Monitor][lnk-AM-metrics] и [Поддерживаемые службы, схемы и категории для журналов диагностики Azure][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Общие сведения о журналах

Azure Monitor отслеживает различные операции, выполняемые в Центре Интернета вещей. Каждая категория имеет схему, определяющую, как выполняются отчеты о событиях в этой категории. 

#### <a name="connections"></a>Подключения

Категория подключений отслеживает ошибки, возникающие при подключении устройств к центру IoT или отключении от него. Отслеживание этой категории полезно для определения попыток несанкционированных подключений и для отслеживания потери подключений к устройствам в областях с проблемами связи.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Отправка команд из облака на устройство

Категория отправки команд из облака на устройство отслеживает ошибки, которые возникают в Центре Интернета вещей и связаны с конвейером сообщений из облака на устройство. В эту категорию входят ошибки, возникающие при отправке сообщений из облака на устройство (например, неавторизированный отправитель), при получении сообщений из облака на устройство (например, превышение числа доставок) и при получении отзыва на сообщение из облака на устройство (например, истечение срока действия отзыва). Эта категория не перехватывает ошибки с устройства, которое неправильно обрабатывает сообщение из облака на устройство, если такое сообщение было успешно доставлено.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>Операции с удостоверениями устройства

Категория операций с удостоверениями устройств отслеживает ошибки, возникающие, когда вы пытаетесь создать, обновить или удалить запись реестра удостоверений центра IoT. Отслеживание этой категории целесообразно для сценариев подготовки.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>Маршруты

Категория маршрутизации сообщений отслеживает ошибки, возникающие во время оценки маршрута сообщений и при определении состояния работоспособности конечной точки Центром Интернета вещей. В эту категорию входят следующие события: когда правило возвращает значение "Не определено", когда Центр Интернета вещей помечает конечную точку как неиспользуемую, а также любые другие ошибки, полученные от конечной точки. Эта категория не содержит конкретных ошибок, связанных с самими сообщениями (например, ошибки регулирования устройств), которые попадают в категорию "телеметрии устройств".

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Телеметрия устройства

Категория телеметрии устройств отслеживает ошибки, которые возникают в центре IoT и связаны с конвейером телеметрии. В эту категорию входят ошибки, возникающие при отправке событий телеметрии (например: регулирование) и при получении событий телеметрии (например: неавторизованный модуль чтения). Эта категория не может перехватывать ошибки, которые вызваны кодом, выполняемым на самом устройстве.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>Операции отправки файлов

Категория передачи файлов позволяет отслеживать ошибки, которые возникают в центре IoT и связаны с функцией передачи файлов. В эту категорию входят:

* ошибки, связанные с универсальным кодом ресурса (URI) SAS (например, если срок его действия истекает до того, как устройство уведомит центр о завершении передачи);
* сбои передач, о которых сообщает устройство;
* ошибки, связанные с отсутствием файла в хранилище при создании уведомления для Центра Интернета вещей.

Обратите внимание, что эта категория не может перехватывать ошибки, которые возникают, когда устройство непосредственно передает файл в хранилище.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>Операции переноса из облака на двойник устройства

Категория операций переноса из облака на двойник устройства отслеживает события, инициируемые службой, на двойнике устройства. Эти операции могут включать получение двойника, обновление сообщаемых свойств и подписку на необходимые свойства.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>Операции переноса с двойника устройства в облако

Категория операций переноса с двойника устройства в облако отслеживает события, инициируемые устройством на двойниках устройства. Эти операции могут включать получение двойника, обновление или замещение тегов и необходимых свойств. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>Запросы к двойникам

Категория запросов к двойникам сообщает о запросах к двойникам устройств, инициируемых в облаке. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>Операции заданий

Категория операций заданий сообщает о запросах задания по обновлению двойников устройства или вызову прямого метода на нескольких устройствах. Эти запросы инициируются в облаке. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>Прямые методы

Категория прямых методов отслеживает взаимодействия типа запрос-ответ, отправленные на отдельные устройства. Эти запросы инициируются в облаке. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Чтение журналов из концентраторов событий Azure

После настройки ведения журнала событий в параметрах диагностики можно создавать приложения, которые считывают журналы, что позволяет выполнять действия на основе информации, содержащейся в них. В этом примере кода журналы извлекаются из концентратора событий:

```csharp
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
    static void Main(string[] args) 
    { 
        Console.WriteLine("Monitoring. Press Enter key to exit.\n"); 
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName); 
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds; 
        CancellationTokenSource cts = new CancellationTokenSource(); 
        var tasks = new List<Task>(); 
        foreach (string partition in d2cPartitions) 
        { 
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token)); 
        } 
        Console.ReadLine(); 
        Console.WriteLine("Exiting..."); 
        cts.Cancel(); 
        Task.WaitAll(tasks.ToArray()); 
    } 
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct) 
    { 
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow); 
        while (true) 
        { 
            if (ct.IsCancellationRequested) 
            { 
                await eventHubReceiver.CloseAsync(); 
                break; 
            } 
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10)); 
            if (eventData != null) 
            { 
                string data = Encoding.UTF8.GetString(eventData.GetBytes()); 
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data); 
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Использование службы "Работоспособность ресурса Azure"

Используйте службу "Работоспособность ресурсов Azure", чтобы отследить, запущен ли ваш Центр Интернета вещей и влияет ли региональный сбой на его работоспособность. Чтобы узнать конкретные сведения о состоянии работоспособности Центра Интернета вещей Azure, ознакомьтесь с разделом [Использование Azure Monitor](#use-azure-monitor). 

Центр Интернета вещей Azure указывает состояние работоспособности на региональном уровне. При региональном сбое, влияющем на Центр Интернета вещей, отображается состояние работоспособности **Неизвестно**. Дополнительные сведения о конкретных проверках работоспособности, которые выполняет служба "Работоспособность ресурсов Azure", см. в статье [Типы ресурсов и проверки работоспособности в службе работоспособности ресурсов Azure][lnk-ARH-checks].

Чтобы проверить работоспособность Центра Интернета вещей, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выберите **Работоспособность службы** > **Работоспособность ресурсов**.
1. Выберите подписку в раскрывающемся списке, а затем **Центр Интернета вещей**.

Дополнительные сведения об интерпретации данных о работоспособности см. в статье [Обзор службы работоспособности ресурсов Azure][lnk-ARH]

## <a name="next-steps"></a>Дополнительная информация

- [Общие сведения о метриках Центра Интернета вещей][lnk-metrics]
- [Удаленный мониторинг и отправка уведомлений в Центре Интернета вещей с помощью службы Azure Logic Apps, обеспечивающей подключение между Центром Интернета вещей и почтовым ящиком][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
