---
title: "Настройка предварительно настроенных решений | Документация Майкрософт"
description: "Руководство по настройке предварительно настроенных решений набора Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: ba965b9bc23b96adb2b1b7c9306cb7f508f820bf
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2017
---
# <a name="customize-a-preconfigured-solution"></a>Настройка предварительно настроенного решения

Предварительно настроенные решения, входящие в состав набора IoT Azure, показывают, какие службы работают вместе в составе набора, формируя комплексное решение. Начиная с этой стартовой точки, есть несколько мест, в которых можно выполнить настройку и расширение решений для их адаптации к конкретным сценариям. В следующих разделах описываются эти точки настройки.

## <a name="find-the-source-code"></a>Поиск исходного кода

Исходный код для предварительно настроенного решения можно найти в GitHub в следующих репозиториях:

* Удаленный мониторинг: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Прогнозируемое обслуживание: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Подключенная фабрика: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

Исходный код для предварительно настроенных решений предоставляется для демонстрации шаблонов и методов, используемых для реализации полной функциональности решения IoT с помощью Azure IoT Suite. Дополнительные сведения о том, как создавать и развертывать решения, можно найти в репозиториях GitHub.

## <a name="change-the-preconfigured-rules"></a>Изменение предварительно настроенных правил

Решение удаленного мониторинга включает в себя три задания [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) для обработки логики сведений об устройстве, телеметрии и правил в решении.

Три задания Stream Analytics и их синтаксис подробно описаны в [пошаговом руководстве по работе с настроенным решением для удаленного мониторинга](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Эти задания можно редактировать напрямую, изменяя или добавляя логику для своего сценария. Задания Stream Analytics можно найти следующим образом:

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Перейдите к группе ресурсов, имя которой совпадает с именем вашего решения IoT. 
3. Выберите задание Azure Stream Analytics, которое вы хотите изменить. 
4. Остановите задание, выбрав **Остановить** в наборе команд. 
5. Измените входные данные, запрос и выходные данные.
   
    В качестве простого изменения запроса для задания **Правила** можно изменить **">"** на **"<"**. При изменении правила на портале в запросе по-прежнему отображается **">"**, но вы видите, как изменилось поведение из-за изменения базового задания.
6. Запустите задание

> [!NOTE]
> Панель удаленного мониторинга зависит от конкретных данных, поэтому изменение задания потенциально может вызвать сбой панели мониторинга.

## <a name="add-your-own-rules"></a>Добавление собственных правил

Наряду с изменением предварительно настроенных заданий Stream Analytics на портале Azure можно добавлять новые задания или новые запросы для существующих заданий.

## <a name="customize-devices"></a>Настройка устройств

Одно из наиболее распространенных действий расширения — работа с устройствами, относящимися к вашему сценарию. Существует несколько способов работы с устройствами. В число этих методов входят изменение виртуального устройства для соответствия вашему сценарию или подключение физического устройства к решению с помощью [пакета SDK устройства Интернета вещей][IoT Device SDK].

Пошаговые инструкции по добавлению устройств см. в статье [Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Windows)](iot-suite-v1-connecting-devices.md). Также вы можете использовать [пример пакета SDK для удаленного мониторинга](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Этот пример предназначен для работы с предварительно настроенным решением для удаленного мониторинга.

### <a name="create-your-own-simulated-device"></a>Создание собственного виртуального устройства

В [исходный код решения удаленного мониторинга](https://github.com/Azure/azure-iot-remote-monitoring) включен симулятор .NET. Этот симулятор подготавливается как часть решения, и его можно настроить для отправки разных метаданных и данных телеметрии, а также для реагирования на разные команды и методы.

Симулятор предварительно настроенного решения удаленного мониторинга имитирует устройство охлаждения, которое выдает данные телеметрии о температуре и влажности. Изменить симулятор можно в проекте [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) при создании разветвления в репозитории GitHub.

### <a name="available-locations-for-simulated-devices"></a>Доступные расположения для виртуальных устройств

Расположения по умолчанию находятся в Редмонде (Сиэтле), штат Вашингтон, США. Эти расположения можно изменить в файле [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Добавление обработчика изменения для требуемого свойства в симуляторе

На портале решения вы можете задать значение для требуемого свойства устройства. Обработку запроса на изменение свойства должно выполнять само устройство, получая новое значение требуемого свойства. Чтобы добавить поддержку для изменения значений свойства через требуемое свойство, необходимо добавить в симулятор соответствующий обработчик.

Симулятор содержит обработчики для свойств **SetPointTemp** и **TelemetryInterval**, для изменения которых можно установить нужные значения на портале решения.

В следующем примере представлен обработчик требуемого свойства **SetPointTemp** в классе **CoolerDevice**:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Этот метод изменяет температуру для точки телеметрии и передает информацию об изменении в Центр Интернета вещей, устанавливая значение сообщаемого свойства.

Вы можете добавить собственные обработчики для требуемых свойств, используя шаблон из предыдущего примера.

Также следует привязать требуемое свойство к обработчику, как показано в следующем примере из конструктора **CoolerDevice**:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Обратите внимание, что **SetPointTempPropertyName** — это константа, определенная как Config.SetPointTemp.

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Добавление в симулятор поддержки для нового метода

Вы можете изменить симулятор, добавив поддержку нового [метода (прямой метод)][lnk-direct-methods]. Нужно выполнить два основных действия.

- Симулятор должен уведомлять Центр Интернета вещей в предварительно настроенном решении, передавая сведения о методе.
- Симулятор должен содержать код для обработки вызова метода, запускаемого из панели **Сведения об устройстве** в обозревателе решений или с помощью задания.

Удаленный мониторинг предварительно настроенного решения использует *сообщаемые свойства* для передачи в Центр Интернета вещей подробных сведений о поддерживаемых методах. Серверная часть решения сохраняет список всех методов, поддерживаемых для каждого устройства, а также журнал вызовов этих методов. Эти сведения об устройствах и вызовах методов можно посмотреть на портале решения.

Чтобы уведомить Центр Интернета вещей о поддержке метода, устройство добавляет сведения о методе в узел **SupportedMethods** в сообщаемых свойствах:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

Подпись метода имеет следующий формат: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Например, если метод **InitiateFirmwareUpdate** ожидает строковый параметр с именем **FwPackageURI**, сообщить об этом можно с помощью такой подписи метода:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Список поддерживаемых типов параметров см. в описании класса **CommandTypes** в проекте инфраструктуры.

Чтобы удалить метод, установите в сообщаемых свойствах значение `null` для подписи метода.

> [!NOTE]
> Серверная часть решения обновляет сведения о поддерживаемых методах только при получении от устройства сообщений типа *сведения об устройстве*.

В следующем примере кода из класса **SampleDeviceFactory**, реализованного в проекте Common, демонстрируется добавление метода в список поддерживаемых методов **SupportedMethods** в сообщаемых свойствах, отправленных устройством:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Этот фрагмент кода добавляет сведения о методе **InitiateFirmwareUpdate**, в том числе текст, отображаемый на портале решения, и подробные сведения о параметрах метода.

При запуске симулятор отправляет сообщаемые свойства, включая список поддерживаемых методов, в Центр Интернета вещей.

Добавьте в симулятор обработчик для каждого поддерживаемого метода. Существующие обработчики можно увидеть в классе **CoolerDevice** в проекте Simulator.WebJob. В следующем примере представлен обработчик для метода **InitiateFirmwareUpdate**:

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

Имя для метода обработчика должно состоять из строки `On`, за которой следует имя метода. Параметр **methodRequest** содержит все параметры, передаваемые при вызове этого метода из серверной части решения. Возвращаемое значение должно иметь тип **Task&lt;MethodResponse&gt;**. Вспомогательный метод **BuildMethodResponse** служит для создания возвращаемого значения.

В методе обработчика можно выполнить следующее.

- Запустить асинхронную задачу.
- Получить требуемые свойства от *двойника устройства*, хранящегося в Центре Интернета вещей.
- Обновить одно сообщаемое свойство с помощью метода **SetReportedPropertyAsync** из класса **CoolerDevice**.
- Обновить несколько сообщаемых свойств, создав экземпляр **TwinCollection** и вызвав для него метод **Transport.UpdateReportedPropertiesAsync**.

Предыдущий пример обновления микропрограммы выполняет следующие действия.

- Проверяет, что устройство способно принимать запрос на обновление микропрограммы.
- Асинхронно запускает операцию обновления микропрограммы и сбрасывает данные телеметрии по завершении этой операции.
- Немедленно возвращает сообщение "Принято обновление микропрограммы", подтверждая получение запроса устройством.

### <a name="build-and-use-your-own-physical-device"></a>Построение и использование собственного (физического) устройства

[Пакеты SDK для IoT Azure](https://github.com/Azure/azure-iot-sdks) предоставляют библиотеки для подключения различных типов устройств (языков и операционных систем) к решениям IoT.

## <a name="modify-dashboard-limits"></a>Изменение ограничений панели мониторинга

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Количество устройств, отображаемых в раскрывающемся списке панели мониторинга

Количество по умолчанию — 200. Это количество можно изменить в файле [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Количество маркеров, отображаемых в элементе управления карты Bing

Количество по умолчанию — 200. Это количество можно изменить в файле [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Период времени графика телеметрии

Значение по умолчанию — 10 минут. Это значение можно изменить в файле [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Отзыв

У вас есть предложение по настройке, которое не описано в этом документе? Оставьте его на сайте [User Voice](https://feedback.azure.com/forums/321918-azure-iot) или в комментариях к этой статье. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о настройке решений с предварительно заданными параметрами см. в статьях:

* [Руководство. Подключение приложения логики к предварительно настроенному решению для удаленного мониторинга Azure IoT Suite][lnk-logicapp]
* [Использование динамической телеметрии с предварительно настроенным решением для удаленного мониторинга][lnk-dynamic]
* [Метаданные сведений об устройстве в предварительно настроенном решении для удаленного мониторинга][lnk-devinfo]
* [Customize how the connected factory solution displays data from your OPC UA servers][lnk-cf-customize] (Настройка отображения данных серверов OPC UA решением подключенной фабрики)

[lnk-logicapp]: iot-suite-v1-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]: iot-suite-connected-factory-customize.md