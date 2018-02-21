---
title: "Использование двойников устройств Центра Интернета вещей (.NET или Node) | Документация Майкрософт"
description: "Настройка устройств с помощью двойников устройств Центра Интернета вещей Azure. Используйте пакет SDK для устройств Azure IoT для Node.js, чтобы реализовать приложение имитации устройства, и пакет SDK для служб Azure IoT для .NET, чтобы реализовать приложение-службу, которое изменит конфигурацию устройства с помощью двойника устройства."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.openlocfilehash: 8f9626fc47e7d32cb104b960bea9b7de9efa6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Настройка устройств с помощью требуемых свойств
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

По завершении работы с этим руководством у вас будет два консольных приложения:

* **SimulateDeviceConfiguration.js** — это приложение имитации устройства, которое ожидает обновления требуемой конфигурации и сообщает о состоянии обновления имитации конфигурации.
* **SetDesiredConfigurationAndQuery** — это внутреннее приложение .NET, которое задает требуемую конфигурацию на устройстве и выполняет запрос к процессу обновления конфигурации.

> [!NOTE]
> Статья [IoT Hub SDKs][lnk-hub-sdks] (Пакеты SDK для Центра Интернета вещей) содержит сведения о разных пакетах SDK для Azure IoT, с помощью которых можно создать приложения для устройств и внутренние приложения.
> 
> 

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Node.js 4.0.x или более поздней версии.
* Активная учетная запись Azure. Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

Если вы выполнили указания руководства [Начало работы с двойниками устройств][lnk-twin-tutorial], то у вас уже есть Центр Интернета вещей и удостоверение устройства **myDeviceId**. В этом случае можно перейти к разделу [Создание приложения имитации устройства][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Создание приложения имитации устройства
В этом разделе вы создаете консольное приложение Node.js, которое подключается к центру как **myDeviceId**, ожидает обновления требуемой конфигурации и сообщает об обновлениях в ходе обновления имитации конфигурации.

1. Создайте пустую папку с именем **simulatedeviceconfiguration**. В папке **simulatedeviceconfiguration** создайте файл package.json, используя следующую команду в командной строке. Примите все значения по умолчанию.
   
    ```
    npm init
    ```
1. В командной строке в папке **simulatedeviceconfiguration** выполните следующую команду, чтобы установить пакеты **azure-iot-device** и **azure-iot-device-mqtt**.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. В текстовом редакторе создайте файл **SimulateDeviceConfiguration.js** в папке **simulatedeviceconfiguration**.
1. Добавьте следующий код в файл **SimulateDeviceConfiguration.js** и замените заполнитель **{device connection string}** строкой подключения устройства, скопированной при создании удостоверения устройства **myDeviceId**.
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. Этот код инициализирует объект **Client**, извлекает двойник устройства для **myDeviceId**, а затем подключает обработчик для обновления *требуемых свойств*. Обработчик проверяет, отправлен ли запрос на изменение конфигурации. Для этого он сравнивает идентификаторы конфигураций, а затем вызывает метод, который запускает изменение конфигурации.
   
    Обратите внимание, что для простоты в этом коде используется жестко заданное значение по умолчанию для выполнения начальной настройки. Настоящее приложение, вероятно, скачало бы эту конфигурацию из локального хранилища.
   
   > [!IMPORTANT]
   > События изменения требуемых свойств всегда инициируются единожды при подключении устройства. Прежде чем что-либо делать, обязательно убедитесь, что требуемые свойства действительно изменились.
   > 
   > 
1. Добавьте следующие методы перед осуществлением вызова `client.open()`:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    Метод **initConfigChange** обновляет сообщаемые свойства в объекте локального двойника устройства с помощью запроса на обновление конфигурации и устанавливает для состояния значение **Pending** (Ожидание), а затем обновляет двойник устройства в службе. После успешного обновления двойника устройства он имитирует длительный процесс, который завершается выполнением **completeConfigChange**. Этот метод обновляет сообщаемые локальные свойства, установив для состояния значение **Success** и удалив объект **pendingConfig**. Затем он обновляет двойник устройства в службе.
   
    Обратите внимание, что для сокращения нагрузки на полосу пропускания при обновлении сообщаемых свойств указываются только свойства, которые нужно изменить (**patch** в коде выше), а не заменяется весь документ.
   
   > [!NOTE]
   > В этом руководстве не имитируется поведение при одновременном обновлении конфигураций. В некоторых случаях обновление конфигурации может пройти в соответствии с изменениями целевой конфигурации, в других может потребоваться поместить изменения в очередь, а в остальных они могут быть отклонены с ошибкой. Следует учитывать желаемое поведение для конкретной конфигурации и добавить соответствующую логику перед ее изменением.
   > 
   > 
1. Запустите приложение устройства:
   
        node SimulateDeviceConfiguration.js
   
    Отобразится сообщение `retrieved device twin`. Не отключайте приложение.

## <a name="create-the-service-app"></a>Создание приложения службы
В этом разделе вы создадите консольное приложение .NET, которое обновляет *требуемые свойства* двойника устройства, связанного с **myDeviceId**, с использованием нового объекта конфигурации телеметрии. Затем оно выполняет запрос к двойникам устройств, которые хранятся в Центре Интернета вещей, и показывает разницу между требуемой и сообщаемой конфигурацией устройства.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **SetDesiredConfigurationAndQuery**.
   
    ![Новый проект классического приложения Windows на языке Visual C#][img-createapp]
1. В обозревателе решений щелкните правой кнопкой мыши проект **SetDesiredConfigurationAndQuery** и выберите **Управление пакетами Nuget...**
1. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure][lnk-nuget-service-sdk] и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Окно "Диспетчер пакетов NuGet"][img-servicenuget]
1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения к Центру Интернета вещей, созданному в предыдущем разделе.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Добавьте следующий метод в класс **Program** .
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    Объект **Registry** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства из службы. Этот код инициализирует объект **Registry**, извлекает двойник устройства для **myDeviceId**, а затем обновляет его требуемые свойства, используя новый объект конфигурации телеметрии.
    Затем он каждые 10 секунд запрашивает данные двойников устройств, которые хранятся в Центре Интернета вещей, и выводит требуемые и сообщаемые конфигурации телеметрии. Чтобы узнать, как создавать эффективные отчеты на всех устройствах, см. статью [Справочник по языку запросов для двойников и заданий][lnk-query].
   
   > [!IMPORTANT]
   > Это приложение выполняет запрос к Центру Интернета вещей каждые 10 секунд в качестве примера. Используйте запросы для создания представляемых пользователям отчетов на многих устройствах, а не для обнаружения изменений. Если вашему решению требуется отправка уведомлений о событиях устройства в реальном времени, используйте [уведомления двойников][lnk-twin-notifications].
   > 
   > 
1. Наконец, добавьте следующие строки в метод **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. В обозревателе решений выберите **Задать автозагружаемые проекты...** и убедитесь, что для параметра **Действие** проекта **SetDesiredConfigurationAndQuery** задано значение **Запустить**. Выполните сборку решения.
1. Во время выполнения **SimulateDeviceConfiguration.js** запустите приложение .NET из Visual Studio, нажав клавишу **F5**. Состояние сообщаемой конфигурации изменится с **Успешно** на **В ожидании** и снова на **Успешно**, а значение активной частоты отправки изменится с 24 часов на 5 минут.

 ![Устройство успешно настроено][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Результаты запроса выводятся после операции сообщения устройства с задержкой (не более минуты). Таким образом обеспечивается работа инфраструктуры запросов в очень широком масштабе. Для получения согласованных представлений одного двойника устройства используйте метод **getDeviceTwin** в классе **Registry**.
   > 
   > 

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы установили требуемую конфигурацию в качестве *требуемых свойств* из серверной части решения и написали код приложения для устройства, которое обнаруживает изменения и имитирует многоэтапный процесс обновления, о состоянии которого сообщается в качестве сообщаемых свойств.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (учебник [Приступая к работе с Центром Интернета вещей Azure (Node)][lnk-iothub-getstarted]);
* запланировать или выполнить операции на больших наборах устройств (см. учебник [Планирование и трансляция заданий][lnk-schedule-jobs];
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (учебник [Use direct methods][lnk-methods-tutorial] (Использование прямых методов)).

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app
