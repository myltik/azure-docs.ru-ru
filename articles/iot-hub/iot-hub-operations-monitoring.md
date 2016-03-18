<properties
 pageTitle="Мониторинг операций центра IoT"
 description="Обзор мониторинга операций центра IoT Azure, позволяющего пользователям отслеживать состояние операций в их центре IoT в режиме реального времени."
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="nberdy"/>

# Вводные сведения о мониторинге операций

Мониторинг операций центра IoT позволяет пользователям отслеживать состояние операций в их центре IoT в режиме реального времени. Центр IoT отслеживает события по нескольким категориям операций, поэтому пользователи могут выбрать отправку событий из одной или нескольких категорий в конечную точку центра IoT для обработки. Пользователи могут отслеживать данные на наличие ошибок или настроить более сложную обработку на основе закономерностей в данных.

Центр IoT отслеживает четыре категории событий:

- Операции с удостоверениями устройства
- Телеметрия устройства
- Отправка команд из облака на устройство
- Подключения

## Включение мониторинга операций

1. Создайте центр IoT. Инструкции по созданию центра IoT можно найти в руководстве [по началу работы][lnk-get-started].

2. Откройте колонку центра IoT. В ней щелкните **Все параметры**, а затем — **Мониторинг операций**.

    ![][1]

3. Выберите категории для наблюдения, а затем нажмите кнопку **Сохранить**. События доступны для чтения из совместимой с концентратором событий конечной точки, указанной в разделе **Параметры мониторинга**.

    ![][2]

## Категории событий и их использование

Каждая категория мониторинга операций отслеживает отдельный тип взаимодействия с центром IoT и имеет схему, которая определяет способ структурирования событий в этой категории.

### Операции с удостоверениями устройства

Категория операций с удостоверениями устройств отслеживает ошибки, возникающие, когда пользователь пытается создать, обновить или удалить запись реестра удостоверений центра IoT. Отслеживание этой категории целесообразно для сценариев подготовки.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": “userAgent”,
         "sharedAccessPolicy": "accessPolicy"
    }

### Телеметрия устройства

Категория телеметрии устройств отслеживает ошибки, которые возникают в центре IoT и связаны с конвейером телеметрии. В их число входят ошибки, возникающие при отправке событий телеметрии (например регулирование) и при получении событий телеметрии (например неавторизованный модуль чтения). Обратите внимание, что эта категория не может перехватывать ошибки, которые вызваны кодом, выполняемым на самом устройстве.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
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

### Отправка команд из облака на устройство

Категория отправки команд из облака на устройство отслеживает ошибки, которые возникают в центре IoT и связаны с конвейером команд устройства. В их число входят ошибки, возникающие при отправке команд (например: неавторизованный отправитель), при получении команд (например: превышено число доставок для сообщений) и при получении отзыва команды (например: истек срок действия отзыва). Эта категория не перехватывает ошибки с устройств, которые неправильно обрабатывают команду, если команда была успешно доставлена.

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### Подключения

Категория подключений отслеживает события, вызванные устройствами, которые подключаются к центру IoT или отключаются от него. Отслеживание этой категории полезно для определения попыток несанкционированных подключений и для отслеживания потери подключений к устройствам в областях с проблемами связи.

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

## Дальнейшие действия

Вы ознакомились с общими сведениями о мониторинге операций. Чтобы узнать больше, перейдите по этим ссылкам.

- [Метрики диагностики центра IoT][lnk-diagnostic-metrics]
- [Масштабирование центра IoT][lnk-scaling]
- [Высокая доступность и аварийное восстановление центра IoT][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0204_2016-->