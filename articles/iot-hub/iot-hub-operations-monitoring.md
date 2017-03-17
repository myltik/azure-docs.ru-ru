---
title: "Мониторинг операций Центра Интернета вещей Azure | Документация Майкрософт"
description: "Использование мониторинга операций Центра Интернета вещей Azure для отслеживания состояния операций Центра Интернета вещей в реальном времени."
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
ms.date: 12/13/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 796bf9b1219b7f0e2c68688c5f5b51163ef4b49b
ms.lasthandoff: 03/07/2017


---
# <a name="iot-hub-operations-monitoring"></a>Мониторинг операций центра IoT
Мониторинг операций центра IoT позволяет отслеживать состояние операций в центре IoT в режиме реального времени. Центр Интернета вещей отслеживает события по нескольким категориям операций. Вы можете выбрать отправку событий из одной или нескольких категорий в конечную точку Центра Интернета вещей для обработки. Вы можете отслеживать данные на наличие ошибок или настроить более сложную обработку на основе закономерностей в данных.

Центр Интернета вещей отслеживает шесть категорий событий:

* Операции с удостоверениями устройства
* Телеметрия устройства
* Получение сообщений из облака на устройство
* Подключения
* Передача файлов
* Маршрутизация сообщений

## <a name="how-to-enable-operations-monitoring"></a>Включение мониторинга операций
1. Создайте центр IoT. Инструкции по созданию Центра Интернета вещей см. в руководстве [по началу работы][lnk-get-started].
2. Откройте колонку центра IoT. В колонке щелкните **Мониторинг операций**.
   
    ![][1]
3. Выберите категории для наблюдения, а затем нажмите кнопку **Сохранить**. События доступны для чтения из совместимой с концентраторами событий конечной точки, указанной в разделе **Параметры мониторинга**. Конечная точка центра IoT называется `messages/operationsmonitoringevents`.
   
    ![][2]

> [!NOTE]
> Если выбрать **Подробный** мониторинг для категории **Подключения**, то Центр Интернета вещей будет создавать дополнительные сообщения с диагностическими сведениями. Для всех других категорий параметр **Подробный** изменяет количество сведений, которые Центр Интернета вещей включает в каждое сообщение об ошибке.

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
Категория отправки команд из облака на устройство отслеживает ошибки, которые возникают в Центре Интернета вещей и связаны с конвейером сообщений из облака на устройство. В эту категорию входят ошибки, возникающие при отправке сообщений из облака на устройство (например, неавторизированный отправитель), при получении сообщений из облака на устройство (например, превышение числа доставок) и при получении отзыва на сообщение из облака на устройство (например, истечение срока действия отзыва). Эта категория не перехватывает ошибки с устройства, которое неправильно обрабатывает сообщение из облака на устройство, если такое сообщение было успешно доставлено.

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

### <a name="message-routing"></a>Маршрутизация сообщений
Категория маршрутизации сообщений отслеживает ошибки, возникающие во время оценки маршрута сообщений и при определении состояния работоспособности конечной точки Центром Интернета вещей. В эту категорию входят следующие события: когда правило возвращает значение "Не определено", когда Центр Интернета вещей помечает конечную точку как неиспользуемую, а также любые другие ошибки, полученные от конечной точки. Обратите внимание, что эта категория не содержит конкретных ошибок о самих сообщениях (таких как ошибки регулирования устройств), которые попадают в категорию "телеметрии устройств".
        
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

## <a name="next-steps"></a>Дальнейшие действия
Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]
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
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer

