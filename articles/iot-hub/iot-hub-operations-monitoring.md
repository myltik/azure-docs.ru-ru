---
title: Мониторинг операций центра IoT
description: Обзор мониторинга операций центра IoT Azure, позволяющего отслеживать состояние операций в центре IoT в режиме реального времени.
services: iot-hub
documentationcenter: ''
author: nberdy
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: nberdy

---
# Вводные сведения о мониторинге операций
Мониторинг операций центра IoT позволяет отслеживать состояние операций в центре IoT в режиме реального времени. Центр IoT отслеживает события по нескольким категориям операций, поэтому вы можете выбрать отправку событий из одной или нескольких категорий в конечную точку центра IoT для обработки. Вы можете отслеживать данные на наличие ошибок или настроить более сложную обработку на основе закономерностей в данных.

Центр IoT отслеживает пять категорий событий:

* Операции с удостоверениями устройства
* Телеметрия устройства
* Отправка команд из облака на устройство
* Подключения
* Передача файлов

## Включение мониторинга операций
1. Создайте центр IoT. Инструкции по созданию центра IoT см. в руководстве [по началу работы][lnk-get-started].
2. Откройте колонку центра IoT. В колонке щелкните **Мониторинг операций**.
   
    ![][1]
3. Выберите категории для наблюдения, а затем нажмите кнопку **Сохранить**. События доступны для чтения из совместимой с концентраторами событий конечной точки, указанной в разделе **Параметры мониторинга**. Конечная точка центра IoT называется `messages/operationsmonitoringevents`.
   
    ![][2]

## Категории событий и их использование
Каждая категория мониторинга операций отслеживает отдельный тип взаимодействия с центром IoT и имеет схему, которая определяет способ структурирования событий в этой категории.

### Операции с удостоверениями устройства
Категория операций с удостоверениями устройств отслеживает ошибки, возникающие, когда вы пытаетесь создать, обновить или удалить запись реестра удостоверений центра IoT. Отслеживание этой категории целесообразно для сценариев подготовки.

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

### Телеметрия устройства
Категория телеметрии устройств отслеживает ошибки, которые возникают в центре IoT и связаны с конвейером телеметрии. В эту категорию входят ошибки, возникающие при отправке событий телеметрии (например: регулирование) и при получении событий телеметрии (например: неавторизованный модуль чтения). Обратите внимание, что эта категория не может перехватывать ошибки, которые вызваны кодом, выполняемым на самом устройстве.

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
Категория отправки команд из облака на устройство отслеживает ошибки, которые возникают в центре IoT и связаны с конвейером команд устройства. В эту категорию входят ошибки, возникающие при отправке команд (например: неавторизованный отправитель), при получении команд (например: превышено число доставок для сообщений) и при получении отзыва команды (например: истек срок действия отзыва). Эта категория не перехватывает ошибки с устройств, которые неправильно обрабатывают команду, если команда была успешно доставлена.

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
Категория подключений отслеживает ошибки, возникающие при подключении устройств к центру IoT или отключении от него. Отслеживание этой категории полезно для определения попыток несанкционированных подключений и для отслеживания потери подключений к устройствам в областях с проблемами связи.

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

### Передача файлов
Категория передачи файлов позволяет отслеживать ошибки, которые возникают в центре IoT и связаны с функцией передачи файлов. В эту категорию входят ошибки, связанные с универсальным кодом ресурса (URI) SAS (например: если срок его действия истекает до того, как устройство уведомит центр о завершении передачи), сбоями передач, о которых сообщает устройство, а также отсутствием файла в хранилище при создании уведомления для центра IoT. Обратите внимание, что эта категория не может перехватывать ошибки, которые возникают, когда устройство непосредственно передает файл в хранилище.

    {
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

## Дальнейшие действия
Теперь, когда вы узнали о мониторинге операций, просмотрите дополнительные сведения об управлении центром IoT в статье [Настройка и управление доступом к центру IoT][lnk-itpro].

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Разработка решения][lnk-design]
* [Руководство разработчика по центру Azure IoT (IoT — Интернет вещей)][lnk-devguide]
* [Обзор управления устройствами центра IoT с помощью примера пользовательского интерфейса][lnk-dmui]
* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0817_2016-->
