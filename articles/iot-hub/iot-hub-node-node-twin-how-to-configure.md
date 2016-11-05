---
title: Использование свойств двойников | Microsoft Docs
description: В этом руководстве описано, как использовать свойства двойников
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda

---
# <a name="tutorial:-use-desired-properties-to-configure-devices-(preview)"></a>Руководство. Настройка устройств с помощью требуемых свойств (предварительная версия)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

По завершении работы с этим руководством у вас будет два консольных приложения Node.js:

* **SimulateDeviceConfiguration.js** — это приложение имитации устройства, которое ожидает обновления требуемой конфигурации и сообщает о состоянии обновления имитации конфигурации.
* **SetDesiredConfigurationAndQuery.js** — это приложение Node.js, предназначенное для запуска из серверной части, которое задает требуемую конфигурацию на устройстве и выполняет запрос к процессу обновления конфигурации.

> [!NOTE]
> Статья [IoT Hub SDKs][lnk-hub-sdks] (Пакеты SDK Центра Интернета вещей) содержит сведения о разных пакетах SDK, с помощью которых можно создать приложения устройств и серверной части.
> 
> 

Для работы с этим учебником требуется:

* Node.js версии 0.10.x или более поздней.
* Активная учетная запись Azure. (Если ее нет, можно создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. в статье о [бесплатной пробной версии Azure][lnk-free-trial].

Если вы выполнили указания руководства [Приступая к работе с двойниками устройств][lnk-twin-tutorial], у вас уже есть концентратор с поддержкой управления устройствами и удостоверение устройства **myDeviceId**. Вы можете перейти к разделу [Создание приложения имитации устройства][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Создание приложения имитации устройства
В этом разделе вы создаете консольное приложение Node.js, которое подключается к центру как **myDeviceId**, ожидает обновления требуемой конфигурации и сообщает об обновлениях в ходе обновления имитации конфигурации.

1. Создайте пустую папку с именем **simulatedeviceconfiguration**. В папке **simulatedeviceconfiguration** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **simulatedeviceconfiguration** выполните следующую команду, чтобы установить пакет **azure-iot-device** и **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```
3. В текстовом редакторе создайте файл **SimulateDeviceConfiguration.js** в папке **simulatedeviceconfiguration**.
4. Добавьте следующий код в файл **SimulateDeviceConfiguration.js** и замените заполнитель **{строка подключения устройства}** на строку подключения, скопированную при создании удостоверения устройства **myDeviceId**:
   
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
   
    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. После инициализации объекта **Client** предыдущий код извлекает двойник для **myDeviceId** и подключает обработчик для обновления требуемых свойств. Обработчик проверяет, отправлен ли запрос на изменение конфигурации. Для этого он сравнивает идентификаторы конфигураций, а затем вызывает метод, который запускает изменение конфигурации.
   
    Обратите внимание, что для простоты приведенный выше код использует жестко заданное значение по умолчанию для выполнения начальной настройки. Настоящее приложение, вероятно, скачало бы эту конфигурацию из локального хранилища.
   
   > [!IMPORTANT]
   > События изменения требуемых свойств всегда создаются единожды при подключении устройства. Прежде чем что-либо делать, обязательно убедитесь, что требуемые свойства действительно изменились.
   > 
   > 
5. Добавьте следующие методы перед осуществлением вызова `client.open()`:
   
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
   
    Метод **initConfigChange** обновляет сообщаемые свойства в объекте локального двойника с помощью запроса на обновление конфигурации и устанавливает для состояния значение **Pending**, а затем обновляет двойник устройства в службе. После успешного обновления двойника он имитирует длительный процесс, который завершается выполнением **completeConfigChange**. Этот метод обновляет сообщаемые свойства локального двойника, установив для состояния значение **Success** и удалив объект **pendingConfig**. Затем он обновляет двойник в службе.
   
    Обратите внимание, что для сокращения нагрузки на полосу пропускания при обновлении сообщаемых свойств указываются только свойства, которые нужно изменить (**patch** в коде выше), а не заменяется весь документ.
   
   > [!NOTE]
   > В этом руководстве не имитируется поведение при одновременном обновлении конфигураций. В некоторых случаях обновление конфигурации может пройти в соответствии с изменениями целевой конфигурации, в других может потребоваться поместить изменения в очередь, а в остальных они могут быть отклонены с ошибкой. Следует учитывать желаемое поведение для конкретной конфигурации и добавить соответствующую логику перед ее изменением.
   > 
   > 
6. Запустите приложение устройства:
   
        node SimulateDeviceConfiguration.js
   
    Отобразится сообщение `retrieved device twin`. Не отключайте приложение.

## <a name="create-the-service-app"></a>Создание приложения службы
В этом разделе вы создадите консольное приложение Node.js, которое обновляет *требуемые свойства* двойника, связанного с **myDeviceId**, с использованием нового объекта конфигурации телеметрии. Затем оно выполняет запрос к двойникам устройств, которые хранятся в концентраторе, и показывает разницу между требуемой и сообщаемой конфигурацией устройства.

1. Создайте пустую папку с именем **setdesiredandqueryapp**. В папке **setdesiredandqueryapp** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **setdesiredandqueryapp** выполните следующую команду, чтобы установить пакет **azure-iothub**:
   
    ```
    npm install azure-iothub@dtpreview node-uuid --save
    ```
3. В текстовом редакторе создайте файл **SetDesiredAndQuery.js** в папке **addtagsandqueryapp**.
4. Добавьте следующий код в файл **SetDesiredAndQuery.js** и замените заполнитель **{строка подключения службы}** на строку подключения, скопированную при создании центра:
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{service connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    Объект **Registry** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства из службы. После инициализации объекта **Registry** предыдущий код извлекает двойник для **myDeviceId** и обновляет его требуемые свойства, используя новый объект конфигурации телеметрии. После этого он вызывает функцию **queryTwins** каждые 10 секунд.

    > [AZURE.IMPORTANT] Это приложение выполняет запрос к Центру Интернета вещей каждые 10 секунд в качестве примера. Используйте запросы для создания представляемых пользователям отчетов на многих устройствах, а не для обнаружения изменений. Если вашему решению требуется отправка уведомлений о событиях устройства в реальном времени, используйте [сообщения, отправляемые с устройства в облако][lnk-d2c].

1. Добавьте следующий код перед осуществлением вызова `registry.getDeviceTwin()` для реализации функции **queryTwins**:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    Предыдущий код выполняет запрос к двойникам, которые хранятся в концентраторе, и выводит требуемые и сообщаемые конфигурации телеметрии. Чтобы узнать, как создавать эффективные отчеты на всех устройствах, см. статью [Язык запросов Центра Интернета вещей][lnk-query].
2. Запустив файл **SimulateDeviceConfiguration.js**, запустите приложение с помощью следующей команды:
   
        node SetDesiredAndQuery.js 5m
   
    Состояние сообщаемой конфигурации изменится с **Success** на **Pending** и снова на **Success**, а значение активной частоты отправки изменится с 24 часов на пять минут.
   
   > [!IMPORTANT]
   > Результаты запроса выводятся после операции сообщения устройства с задержкой (не более минуты). Таким образом обеспечивается работа инфраструктуры запросов в очень широком масштабе. Для получения согласованных представлений одного двойника используйте метод **getDeviceTwin** в классе **Registry**.
   > 
   > 

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы установили требуемую конфигурацию в качестве *требуемых свойств* из приложения серверной части и написали код приложения имитации устройства, которое обнаруживает изменения и имитирует многоэтапный процесс обновления, о состоянии которого сообщается двойнику в качестве *требуемых свойств*.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (руководство [Начало работы с Центром Интернета вещей][lnk-iothub-getstarted]);
* запланировать или выполнить операции на больших наборах устройств (см. руководство [Use jobs to schedule and broadcast device operations][lnk-schedule-jobs] (Планирование и трансляция операций устройства с помощью заданий));
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (руководство [Использование прямых методов][lnk-methods-tutorial]).

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app



<!--HONumber=Oct16_HO2-->


