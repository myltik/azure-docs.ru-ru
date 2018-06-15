---
title: Мониторинг операций Центра Интернета вещей Azure | Документация Майкрософт
description: Использование мониторинга операций Центра Интернета вещей Azure для отслеживания состояния операций Центра Интернета вещей в реальном времени.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: nberdy
ms.openlocfilehash: 0a1da3812d6f11aa6525857596b394fbfa3dc88a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634810"
---
# <a name="iot-hub-operations-monitoring"></a>Мониторинг операций Центра Интернета вещей

Мониторинг операций Центра Интернета вещей позволяет отслеживать состояние операций в Центре Интернета вещей в режиме реального времени. Центр Интернета вещей отслеживает события по нескольким категориям операций. Вы можете выбрать отправку событий из одной или нескольких категорий в конечную точку Центра Интернета вещей для обработки. Вы можете отслеживать данные на наличие ошибок или настроить более сложную обработку на основе закономерностей в данных.

>[!NOTE]
>Функцию мониторинга операций Центра Интернета вещей не рекомендуется использовать. Она будет удалена из Центра Интернета вещей 10 октября 2018 года. Сведения о мониторинге операций и работоспособности Центра Интернета вещей см. в статье [Мониторинг работоспособности Центра Интернета вещей Azure и быстрая диагностика неполадок][lnk-monitor]. Дополнительные сведения о временной шкале удаления функции см. в статье [Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health][lnk-blog-announcement] (Мониторинг решений Интернета вещей Azure с помощью служб Azure Monitor и "Работоспособность ресурсов Azure").

Центр Интернета вещей отслеживает шесть категорий событий:

* Операции с удостоверениями устройства
* Телеметрия устройства
* Получение сообщений из облака на устройство
* Подключения
* Передача файлов
* Маршрутизация сообщений

> [!IMPORTANT]
> Мониторинг операций Центра Интернета вещей не гарантирует надежную или упорядоченную доставку событий. В зависимости от базовой инфраструктуры Центра Интернета вещей некоторые события могут быть утеряны или доставлены в неправильном порядке. Используйте мониторинг операций для создания оповещений на основе сигналов об ошибках, таких как сбои при попытке подключения к устройствам или частое отключение устройств. Не следует полагаться на события мониторинга операций при создании согласованного хранилища состояний устройства, например хранилища, отслеживающего состояние подключения устройства. 

## <a name="how-to-enable-operations-monitoring"></a>Включение мониторинга операций

1. Создайте Центр Интернета вещей. Инструкции по созданию Центра Интернета вещей см. в руководстве [по началу работы][lnk-get-started].

1. Откройте колонку центра IoT. В колонке щелкните **Мониторинг операций**.

    ![Доступ к конфигурации мониторинга операций на портале][1]

1. Выберите категории для наблюдения, а затем нажмите кнопку **Сохранить**. События доступны для чтения из совместимой с концентраторами событий конечной точки, указанной в разделе **Параметры мониторинга**. Конечная точка Центра Интернета вещей называется `messages/operationsmonitoringevents`.

    ![Настройка мониторинга операций в Центре Интернета вещей][2]

> [!NOTE]
> Если выбрать **Подробный** мониторинг для категории **Подключения**, то Центр Интернета вещей будет создавать дополнительные сообщения с диагностическими сведениями. Для всех других категорий параметр **Подробный** изменяет количество сведений, которые Центр Интернета вещей включает в каждое сообщение об ошибке.

## <a name="event-categories-and-how-to-use-them"></a>Категории событий и их использование

Каждая категория мониторинга операций отслеживает отдельный тип взаимодействия с Центром Интернета вещей и имеет схему, которая определяет способ структурирования событий в этой категории.

### <a name="device-identity-operations"></a>Операции с удостоверениями устройства

Категория операций с удостоверениями устройств отслеживает ошибки, возникающие, когда вы пытаетесь создать, обновить или удалить запись реестра удостоверений центра IoT. Отслеживание этой категории целесообразно для сценариев подготовки.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Телеметрия устройства

Категория телеметрии устройств отслеживает ошибки, которые возникают в центре IoT и связаны с конвейером телеметрии. В эту категорию входят ошибки, возникающие при отправке событий телеметрии (например: регулирование) и при получении событий телеметрии (например: неавторизованный модуль чтения). Эта категория не может перехватывать ошибки, которые вызваны кодом, выполняемым на самом устройстве.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Отправка команд из облака на устройство

Категория отправки команд из облака на устройство отслеживает ошибки, которые возникают в Центре Интернета вещей и связаны с конвейером сообщений из облака на устройство. В эту категорию входят ошибки, возникающие при отправке сообщений из облака на устройство (например, неавторизированный отправитель), при получении сообщений из облака на устройство (например, превышение числа доставок) и при получении отзыва на сообщение из облака на устройство (например, истечение срока действия отзыва). Эта категория не перехватывает ошибки с устройства, которое неправильно обрабатывает сообщение из облака на устройство, если такое сообщение было успешно доставлено.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Подключения

Категория подключений отслеживает ошибки, возникающие при подключении устройств к центру IoT или отключении от него. Отслеживание этой категории полезно для определения попыток несанкционированных подключений и для отслеживания потери подключений к устройствам в областях с проблемами связи.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Передача файлов

Категория передачи файлов позволяет отслеживать ошибки, которые возникают в центре IoT и связаны с функцией передачи файлов. В эту категорию входят:

* ошибки, связанные с универсальным кодом ресурса (URI) SAS (например, если срок его действия истекает до того, как устройство уведомит центр о завершении передачи);
* сбои передач, о которых сообщает устройство;
* ошибки, связанные с отсутствием файла в хранилище при создании уведомления для Центра Интернета вещей.

Обратите внимание, что эта категория не может перехватывать ошибки, которые возникают, когда устройство непосредственно передает файл в хранилище.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Маршрутизация сообщений

Категория маршрутизации сообщений отслеживает ошибки, возникающие во время оценки маршрута сообщений и при определении состояния работоспособности конечной точки Центром Интернета вещей. В эту категорию входят следующие события: когда правило возвращает значение "Не определено", когда Центр Интернета вещей помечает конечную точку как неиспользуемую, а также любые другие ошибки, полученные от конечной точки. Эта категория не содержит конкретных ошибок, связанных с самими сообщениями (например, ошибки регулирования устройств), которые попадают в категорию "телеметрии устройств".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="view-events"></a>Просмотр событий

Чтобы быстро проверить, что Центр Интернета вещей создает события мониторинга, можно использовать *iothub-explorer*. Инструкции по его установке см. в репозитории GitHub [iothub-explorer][lnk-iothub-explorer].

1. Убедитесь, что на портале для категории мониторинга **Подключения** задано значение **Подробные сведения**.

1. В командной строке выполните следующую команду для чтения из конечной точки мониторинга:

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. В другой командной строке выполните следующую команду для имитации устройства, отправляющего сообщения в облако:

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. В первой командной строке события мониторинга отображаются после подключения имитированного устройства к Центру Интернета вещей.

## <a name="connect-to-the-monitoring-endpoint"></a>Подключение к конечной точке мониторинга

Конечная точка мониторинга в Центре Интернета вещей совместима с концентраторами событий. Чтобы считывать сообщения мониторинга из этой конечной точки, можно использовать любой механизм, который работает с концентраторами событий. В этом примере создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. Дополнительные сведения о способах обработки сообщений из концентраторов событий см. в руководстве [Приступая к работе с концентраторами событий][lnk-eventhubs-tutorial].

Чтобы подключиться к конечной точке мониторинга, потребуется строка подключения и имя конечной точки. Ниже показано, как найти необходимые значения на портале.

1. На портале перейдите к колонке ресурсов Центра Интернета вещей.

1. Выберите **Мониторинг операций** и запишите значения полей **Event Hub-compatible name** (Имя, совместимое с концентраторами событий) и **Event Hub-compatible endpoint** (Конечная точка, совместимая с концентраторами событий).

    ![Значения конечной точки, совместимой с концентраторами событий][img-endpoints]

1. Выберите **Политики общего доступа**, а затем — **служба**. Запишите значение поля **Первичный ключ**.

    ![Первичный ключ политики общего доступа службы][img-service-key]

Следующий пример кода C# взят из проекта консольного **классического приложения Windows** на языке C# в Visual Studio. В состав этого проекта входит установленный пакет NuGet **WindowsAzure.ServiceBus**.

* Замените заполнитель строки подключения строкой подключения, в которой используются записанные ранее значения **конечной точки, совместимой с концентраторами событий**, и **первичного ключа** службы, как показано в следующем примере:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Замените заполнитель имени конечной точки мониторинга записанным ранее значением **имени конечной точки, совместимой с концентраторами событий**.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

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
            }
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация
Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]
* [Развертывание ИИ на пограничных устройствах с использованием Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
