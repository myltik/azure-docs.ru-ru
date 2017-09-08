---
title: "Общие сведения о заданиях Центра Интернета вещей Azure | Документация Майкрософт"
description: "Руководство разработчика. Планирование выполнения заданий на нескольких устройствах, подключенных к Центру Интернета вещей. Задания могут обновлять теги и требуемые свойства, а также вызывать прямые методы на нескольких устройствах."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: juanpere
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 6e4ca8ad0c444930f5e45eed0a024412de82dbb1
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---
# <a name="schedule-jobs-on-multiple-devices"></a>Планирование заданий на нескольких устройствах
## <a name="overview"></a>Обзор
Как описано в предыдущих статьях, Центр Интернета вещей Azure включает ряд стандартных блоков ([свойства и теги двойников устройств][lnk-twin-devguide] и [прямые методы][lnk-dev-methods]).  Как правило, внутренние приложения позволяют администраторам и операторам устройств массово и в запланированное время обновлять устройства Интернета вещей и взаимодействовать с ними.  На запланированное время задания инкапсулируют выполнение обновлений для двойников устройств и прямых методов для ряда устройств.  Например, оператор использует внутреннее приложение, которое инициирует задание перезапуска ряда устройств на третьем этаже в здании №43 в определенное время и отслеживает ход его выполнения. При этом задание не будет нарушать выполнение других операций в здании.

### <a name="when-to-use"></a>Сценарии использования
Используйте задания, если в серверной части решения необходимо запланировать выполнение задания на ряде устройств, а также настроить отслеживание выполнения действий:

* Обновление требуемых свойств
* Обновление тегов
* Вызов прямых методов

## <a name="job-lifecycle"></a>Жизненный цикл задания
Задания инициируются в серверной части решения, а осуществляются с помощью Центра Интернета вещей.  Вы можете инициировать задание, используя обращенный к службе универсальный код ресурса (URI) `{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14` и запросить данные о выполнении задания, используя аналогичный URI `{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`.  Если запросить задание после запуска, внутреннее приложение обновит состояние выполняемых заданий.

> [!NOTE]
> При запуске задания в именах и значениях свойств должны содержаться только печатаемые буквенно-цифровые символы US-ASCII, кроме следующих: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

## <a name="reference-topics"></a>Справочные материалы
Далее предоставлены дополнительные сведения об использовании заданий.

## <a name="jobs-to-execute-direct-methods"></a>Задания для выполнения прямого метода
Приведенный ниже запрос HTTP 1.1 предназначен для выполнения [прямого метода][lnk-dev-methods] с использованием задания на ряде устройств:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
Условие запроса также может находиться в одном идентификаторе устройства или в списке идентификаторов устройств, как показано ниже.

**Примеры**
```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
В статье [Справочник по языку запросов Центр Интернета вещей для двойников устройств и заданий][lnk-query] подробно рассматривается язык запросов Центра Интернета вещей.

## <a name="jobs-to-update-device-twin-properties"></a>Задания для обновления свойств устройств-двойников
Запрос HTTP 1.1 ниже предполагает обновление свойств устройства-двойника с использованием задания:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Запрос на отслеживание выполнения заданий
Приведенный ниже запрос HTTP 1.1 предусматривает [получение данных о ходе выполнения заданий][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```

Объект continuationToken получен в рамках ответа.  

## <a name="jobs-properties"></a>Свойства заданий
В таблице ниже содержится список свойств, которые можно использовать при выполнении запросов на задания и их результаты, а также описание этих свойств.

| Свойство | Description (Описание) |
| --- | --- |
| **jobId** |Идентификатор задания, указанный в приложении. |
| **startTime** |Время начала задания (ISO-8601), указанное в приложении. |
| **endTime** |Дата завершения задания (ISO-8601), указанная в Центре Интернета вещей. Она допустима только после перехода задания в завершенное состояние. |
| **type** |Типы заданий: |
| **scheduledUpdateTwin** — задание по обновлению набора требуемых свойств или тегов. | |
| **scheduledDeviceMethod** — задание по вызову метода устройства для набора двойников устройств. | |
| **состояние** |Текущее состояние задания. Возможные значения состояния: |
| **pending** — задание запланировано и ожидает действий от службы заданий. | |
| **scheduled** — задание запланировано на будущее. | |
| **running** — задание сейчас активно. | |
| **cancelled** — задание отменено. | |
| **failed** — произошел сбой задания. | |
| **completed** — задание завершено. | |
| **deviceJobStatistics** |Статистика задания. |

Свойства **deviceJobStatistics**.

| Свойство | Description (Описание) |
| --- | --- |
| **deviceJobStatistics.deviceCount** |Количество устройств в задании. |
| **deviceJobStatistics.failedCount** |Количество устройств, на которых произошел сбой задания. |
| **deviceJobStatistics.succeededCount** |Количество устройств, на которых задание выполнено успешно. |
| **deviceJobStatistics.runningCount** |Количество устройств, на которых сейчас выполняется задание. |
| **deviceJobStatistics.pendingCount** |Количество устройств, на которых сейчас ожидается выполнение задания. |

### <a name="additional-reference-material"></a>Дополнительные справочные материалы
Другие справочные статьи в руководстве разработчика для Центра Интернета вещей:

* Статья [IoT Hub endpoints][lnk-endpoints] (Конечные точки Центра Интернета вещей) содержит сведения о конечных точках, которые каждый Центр Интернета вещей предоставляет для операций управления и среды выполнения.
* Статья [Throttling and quotas][lnk-quotas] (Регулирование и квоты) содержит сведения о квотах, применимых к службе Центра Интернета вещей, и ожидаемом поведении регулирования при использовании службы.
* В статье [Пакеты SDK для устройств и служб Интернета вещей Azure][lnk-sdks] указаны различные языковые пакеты SDK, которые можно использовать при разработке приложений для устройств и служб, взаимодействующих с Центром Интернета вещей.
* Статья [Справочник по языку запросов Центра Интернета вещей для двойников устройств, заданий и маршрутизации сообщений][lnk-query] содержит сведения о языке запросов, который можно использовать для получения сведений о двойниках устройств и заданиях из Центра Интернета вещей.
* Статья [Поддержка MQTT в Центре Интернета вещей][lnk-devguide-mqtt] содержит дополнительные сведения о поддержке протокола MQTT в Центре Интернета вещей.

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотели бы применить на практике некоторые основные понятия, описанные в этой статье, можно просмотреть следующее руководство по Центру Интернета вещей:

* [Планирование и трансляция заданий][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md

