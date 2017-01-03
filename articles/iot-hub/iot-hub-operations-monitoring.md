---
title: "Мониторинг операций центра IoT"
description: "Обзор мониторинга операций Центра Интернета вещей, позволяющего отслеживать состояние операций в Центре Интернета вещей в режиме реального времени."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: ce514e19370d2b42fb16b4e96b66f212d5fa999c
ms.openlocfilehash: 3c1ae13409c11ec49810209dd155e934b34c3a9b


---
# <a name="introduction-to-operations-monitoring"></a>Вводные сведения о мониторинге операций
Мониторинг операций центра IoT позволяет отслеживать состояние операций в центре IoT в режиме реального времени. Центр Интернета вещей отслеживает события по нескольким категориям операций. Вы можете выбрать отправку событий из одной или нескольких категорий в конечную точку Центра Интернета вещей для обработки. Вы можете отслеживать данные на наличие ошибок или настроить более сложную обработку на основе закономерностей в данных.

Центр IoT отслеживает пять категорий событий:

* Операции с удостоверениями устройства
* Телеметрия устройства
* Отправка команд из облака на устройство
* Подключения
* Передача файлов

## <a name="how-to-enable-operations-monitoring"></a>Включение мониторинга операций
1. Создайте центр IoT. Инструкции по созданию Центра Интернета вещей см. в руководстве [по началу работы][lnk-get-started].
2. Откройте колонку центра IoT. В колонке щелкните **Мониторинг операций**.
   
    ![][1]
3. Выберите категории для наблюдения, а затем нажмите кнопку **Сохранить**. События доступны для чтения из совместимой с концентраторами событий конечной точки, указанной в разделе **Параметры мониторинга**. Конечная точка центра IoT называется `messages/operationsmonitoringevents`.
   
    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>Категории событий и их использование
Каждая категория мониторинга операций отслеживает отдельный тип взаимодействия с центром IoT и имеет схему, которая определяет способ структурирования событий в этой категории.

### <a name="device-identity-operations"></a>Операции с удостоверениями устройства
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

### <a name="device-telemetry"></a>Телеметрия устройства
Категория телеметрии устройств отслеживает ошибки, которые возникают в центре IoT и связаны с конвейером телеметрии. В эту категорию входят ошибки, возникающие при отправке событий телеметрии (например: регулирование) и при получении событий телеметрии (например: неавторизованный модуль чтения). Обратите внимание, что эта категория не может перехватывать ошибки, которые вызваны кодом, выполняемым на самом устройстве.

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

### <a name="cloud-to-device-commands"></a>Отправка команд из облака на устройство
Категория отправки команд из облака на устройство отслеживает ошибки, которые возникают в центре IoT и связаны с конвейером команд устройства. В эту категорию входят ошибки, возникающие при отправке команд (например: неавторизованный отправитель), при получении команд (например: превышено число доставок для сообщений) и при получении отзыва команды (например: истек срок действия отзыва). Эта категория не перехватывает ошибки с устройств, которые неправильно обрабатывают команду, если команда была успешно доставлена.

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
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Подключения
Категория подключений отслеживает ошибки, возникающие при подключении устройств к центру IoT или отключении от него. Отслеживание этой категории полезно для определения попыток несанкционированных подключений и для отслеживания потери подключений к устройствам в областях с проблемами связи.

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

### <a name="file-uploads"></a>Передача файлов

Категория передачи файлов позволяет отслеживать ошибки, которые возникают в центре IoT и связаны с функцией передачи файлов. В эту категорию входят:

- ошибки, связанные с универсальным кодом ресурса (URI) SAS (например, если срок его действия истекает до того, как устройство уведомит центр о завершении передачи);
- сбои передач, о которых сообщает устройство;
- ошибки, связанные с отсутствием файла в хранилище при создании уведомления для Центра Интернета вещей.

Обратите внимание, что эта категория не может перехватывать ошибки, которые возникают, когда устройство непосредственно передает файл в хранилище.


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

## <a name="next-steps"></a>Дальнейшие действия
Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика][lnk-devguide]
* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


