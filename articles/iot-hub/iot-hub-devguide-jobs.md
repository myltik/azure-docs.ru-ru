---
title: Общие сведения о заданиях Центра Интернета вещей Azure | Документация Майкрософт
description: Руководство разработчика. Планирование выполнения заданий на нескольких устройствах, подключенных к Центру Интернета вещей. Задания могут обновлять теги и требуемые свойства, а также вызывать прямые методы на нескольких устройствах.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 35b8536b944df39d0d47bf3529698fc94e51110e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633950"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Планирование заданий на нескольких устройствах

Центр Интернета вещей Azure включает ряд стандартных блоков ([свойства и теги двойников устройств][lnk-twin-devguide] и [прямые методы][lnk-dev-methods]).  Как правило, внутренние приложения позволяют администраторам и операторам устройств массово и в запланированное время обновлять устройства Интернета вещей и взаимодействовать с ними.  На запланированное время задания выполняют обновления для двойников устройств и прямых методов для ряда устройств.  Например, оператор использует внутреннее приложение, которое инициирует задание перезапуска ряда устройств на третьем этаже в здании №43 в определенное время и отслеживает ход его выполнения. При этом задание не будет нарушать выполнение других операций в здании.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Используйте задания, если необходимо запланировать выполнение задания на ряде устройств, а также настроить отслеживание выполнения действий:

* Обновление требуемых свойств
* Обновление тегов
* Вызов прямых методов

## <a name="job-lifecycle"></a>Жизненный цикл задания
Задания инициируются в серверной части решения, а осуществляются с помощью Центра Интернета вещей.  Вы можете инициировать задание, используя обращенный к службе универсальный код ресурса (URI) `{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14` и запросить данные о выполнении задания, используя аналогичный URI `{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`. Чтобы обновить состояние выполняемых заданий после запуска задания, выполните запрос задания.

> [!NOTE]
> При запуске задания в именах и значениях свойств должны содержаться только печатаемые буквенно-цифровые символы US-ASCII, кроме следующих: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Задания для выполнения прямого метода
Приведенный ниже фрагмент запроса HTTPS 1.1 предназначен для выполнения [прямого метода][lnk-dev-methods] с использованием задания на ряде устройств:

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

Условие запроса также может находиться в одном идентификаторе устройства или в списке идентификаторов устройств, как показано ниже.

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
В статье [Справочник по языку запросов Центра Интернета вещей для двойников устройств и заданий][lnk-query] подробно рассматривается язык запросов Центра Интернета вещей.

## <a name="jobs-to-update-device-twin-properties"></a>Задания для обновления свойств устройств-двойников
В следующем фрагменте показаны сведения по обновлению свойств устройства-двойника с использованием задания в запросе HTTPS 1.1.

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

## <a name="querying-for-progress-on-jobs"></a>Запрос на отслеживание выполнения заданий
Приведенный ниже фрагмент запроса HTTP 1.1 предусматривает [получение данных о ходе выполнения заданий][lnk-query].

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

Объект continuationToken получен в рамках ответа.  

## <a name="jobs-properties"></a>Свойства заданий
В таблице ниже содержится список свойств, которые можно использовать при выполнении запросов на задания и их результаты, а также описание этих свойств.

| Свойство | ОПИСАНИЕ |
| --- | --- |
| **jobId** |Идентификатор задания, указанный в приложении. |
| **startTime** |Время начала задания (ISO-8601), указанное в приложении. |
| **endTime** |Дата завершения задания (ISO-8601), указанная в Центре Интернета вещей. Она допустима только после перехода задания в завершенное состояние. |
| **type** |Типы заданий: |
| | **scheduledUpdateTwin** — задание по обновлению набора требуемых свойств или тегов. |
| | **scheduledDeviceMethod** — задание по вызову метода устройства для набора двойников устройств. |
| **состояние** |Текущее состояние задания. Возможные значения состояния: |
| | **pending** — задание запланировано и ожидает действий от службы заданий. |
| | **scheduled** — задание запланировано на будущее. |
| | **running** — задание сейчас активно. |
| | **canceled** — задание было отменено. |
| | **failed** — произошел сбой задания. |
| | **completed** — задание завершено. |
| **deviceJobStatistics** |Статистика задания. |
| | Свойства **deviceJobStatistics**: |
| | **deviceJobStatistics.deviceCount**: число устройств в задании. |
| | **deviceJobStatistics.failedCount**: количество устройств, на которых произошел сбой задания. |
| | **deviceJobStatistics.succeededCount**: количество устройств, на которых задание выполнено успешно. |
| | **deviceJobStatistics.runningCount**: количество устройств, на которых сейчас выполняется задание. |
| | **deviceJobStatistics.pendingCount**: количество устройств, на которых сейчас ожидается выполнение задания. |

### <a name="additional-reference-material"></a>Дополнительные справочные материалы
Другие справочные статьи в руководстве разработчика для Центра Интернета вещей:

* Статья [IoT Hub endpoints][lnk-endpoints] (Конечные точки Центра Интернета вещей) содержит сведения о конечных точках, которые каждый центр Интернета вещей предоставляет для операций управления и среды выполнения.
* Статья [Throttling and quotas][lnk-quotas] (Регулирование и квоты) содержит сведения о квотах, применимых к службе Центра Интернета вещей, и ожидаемом поведении регулирования при использовании службы.
* В статье [Пакеты SDK для устройств и служб Azure IoT][lnk-sdks] указаны различные языковые пакеты SDK, которые можно использовать при разработке приложений для устройств и служб, взаимодействующих с Центром Интернета вещей.
* [Язык запросов Центра Интернета вещей для двойников устройств, заданий и маршрутизации сообщений][lnk-query] описывает языковые запросы Центра Интернета вещей. Используйте этот язык запросов для получения сведений о двойниках устройств и заданиях из Центра Интернета вещей.
* Статья [Поддержка MQTT в Центре Интернета вещей][lnk-devguide-mqtt] содержит дополнительные сведения о поддержке протокола MQTT в Центре Интернета вещей.

## <a name="next-steps"></a>Дополнительная информация
Чтобы применить некоторые основные понятия, описанные в этой статье, просмотрите следующие руководства по Центру Интернета вещей:

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
