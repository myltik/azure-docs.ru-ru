---
title: "Создание настраиваемых правил в Azure IoT Suite | Документация Майкрософт"
description: "Создание настраиваемых правил в предварительно настроенном решении Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: d58c27234ea05a82aaa3e8d72f70c1449980df09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Создание настраиваемого правила в предварительно настроенном решении для удаленного мониторинга

## <a name="introduction"></a>Введение

В предварительно настроенных решениях вы можете создавать [правила, которые активируются при достижении определенного порога для значения телеметрии устройства][lnk-builtin-rule]. В статье [Использование динамической телеметрии с предварительно настроенным решением для удаленного мониторинга][lnk-dynamic-telemetry] описывается, как добавлять в решение пользовательские параметры телеметрии, например *ExternalTemperature*. В этой статье показано, как создать в решении настраиваемое правило для динамических типов данных телеметрии.

В этом руководстве мы с помощью простого виртуального устройства, созданного на основе Node.js, будем генерировать динамические данные телеметрии для отправки в серверную часть предварительно настроенного решения. Затем вы добавите настраиваемые правила в решение Visual Studio **RemoteMonitoring** и развернете настроенную серверную часть в подписке Azure.

Для работы с этим учебником необходимы указанные ниже компоненты.

* Активная подписка Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в статье о [бесплатной пробной версии Azure][lnk_free_trial].
* [Node.js][lnk-node] 0.12.x или более поздней версии для создания виртуального устройства.
* Visual Studio 2015 или Visual Studio 2017 для внесения новых правил в серверную часть предварительно настроенного решения.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

Запишите имя решения, которое вы выбрали для развертывания. Это имя понадобится вам дальше.

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

Вы можете остановить консольное приложение Node.js, когда убедитесь, что оно отправляет телеметрию **ExternalTemperature** в предварительно настроенное решение. Не закрывайте окно консоли, так как это консольное приложение Node.js нужно запустить еще раз, когда будет добавлено настраиваемое правило.

## <a name="rule-storage-locations"></a>Расположения хранения правил

Сведения о правилах сохраняются в двух расположениях:

* Таблица **DeviceRulesNormalizedTable**, в которой хранятся нормализованные ссылки на правила, определенные на портале решения. Когда портал решения отображает правила устройства, он отправляет запрос в эту таблицу на получение определений правил.
* Большой двоичный объект **DeviceRules**, который хранит все правила, определенные для всех зарегистрированных устройств. Он определен как источник ссылочных данных для заданий Azure Stream Analytics.
 
Когда вы изменяете существующее правило или создаете новое на портале решения, необходимые изменения вносятся одновременно в таблицу и в большой двоичный объект. Определения правил, которые отображаются на портале, поступают из таблицы хранилища. Определения правила, на которые ссылаются задания Stream Analytics, извлекаются из большого двоичного объекта. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Обновление решения Visual Studio RemoteMonitoring

Ниже показано, как добавить в решение Visual Studio RemoteMonitoring новое правило, которое использует телеметрию **ExternalTemperature**, отправляемую виртуальным устройством.

1. Клонируйте репозиторий **azure-iot-remote-monitoring** в любое удобное расположение на локальном компьютере, если вы этого не сделали ранее, с помощью такой команды Git:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. В Visual Studio откройте файл RemoteMonitoring.sln из локальной копии репозитория **azure-iot-remote-monitoring**.

3. Откройте файл Infrastructure\Models\DeviceRuleBlobEntity.cs и добавьте свойство **ExternalTemperature** следующим образом:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. В тот же файл добавьте свойство **ExternalTemperatureRuleOutput**:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Откройте файл Infrastructure\Models\DeviceRuleDataFields.cs и добавьте представленное ниже свойство **ExternalTemperature** после существующего свойства **Humidity**:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. В том же файле измените метод **_availableDataFields**, чтобы он содержал **ExternalTemperature**, вот так:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Откройте файл Infrastructure\Repository\DeviceRulesRepository.cs и измените метод **BuildBlobEntityListFromTableRows** следующим образом:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Заново создайте и повторно разверните это решение.

Теперь его можно развернуть в подписке Azure.

1. Откройте командную строку с повышенными правами и перейдите к корню локальной копии репозитория azure-iot-remote-monitoring.

2. Чтобы развернуть обновленное решение, выполните следующие команды, заменив **{deployment name}** именем предварительно настроенного решения, которое вы записали ранее:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Обновление задания Stream Analytics

Когда развертывание завершится, можно обновить задание Stream Analytics, чтобы оно использовало новые определения правил.

1. На портале Azure перейдите в группу ресурсов, которая содержит ресурсы предварительно настроенного решения. Эта группа ресурсов имеет то же имя, которое вы указали для решения во время развертывания.

2. Перейдите к заданию Stream Analytics {deployment name}-Rules. 

3. Щелкните **Остановить**, чтобы прекратить выполнение задания Stream Analytics. (Прежде чем вносить изменения в запрос, дождитесь остановки задания потоковой передачи.)

4. Щелкните **Запрос**. Измените запрос, добавив в него инструкцию **SELECT** для параметра **ExternalTemperature**. Далее представлен полный текст измененного запроса с новой инструкцией **SELECT**:

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Щелкните **Сохранить**, чтобы внести изменения в запрос для этого правила.

6. Щелкните **Запустить**, чтобы снова включить задание Stream Analytics.

## <a name="add-your-new-rule-in-the-dashboard"></a>Добавление нового правила на панель мониторинга

Теперь можно добавить правило **ExternalTemperature** в устройство на панели мониторинга решения.

1. Перейдите на портал решения.

2. Откройте панель **Устройства**.

3. Найдите созданное ранее пользовательское устройство, которое отправляет телеметрию **ExternalTemperature**, затем на панели **сведений об устройстве** щелкните **Добавить правило**.

4. Выберите значение **ExternalTemperature** для параметра **Поле данных**.

5. Для параметра **Пороговое значение** задайте значение 56. Щелкните **Сохранить и просмотреть правила**.

6. Вернитесь к панели мониторинга, чтобы просмотреть историю оповещений.

7. В открытом окне консоли запустите консольное приложение Node.js, которое начнет отправлять данные телеметрии **ExternalTemperature**.

8. Обратите внимание, что в таблице **Alarm History** появляются новые сигналы при каждой активации нового правила.
 
## <a name="additional-information"></a>Дополнительная информация

Изменение оператора **>** является более сложной задачей и выходит за рамки процессов, описанных в этом руководстве. Вы можете изменить задание Stream Analytics, чтобы оно использовало любой удобный оператор, но отобразить этот оператор на портале решения намного сложнее. 

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы узнали, как создавать настраиваемые правила, вы можете подробнее изучить предварительно настроенные решения.

- [Руководство. Подключение приложения логики к предварительно настроенному решению для удаленного мониторинга Azure IoT Suite][lnk-logic-app].
- [Метаданные сведений об устройстве в предварительно настроенном решении для удаленного мониторинга][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-logic-apps-tutorial.md