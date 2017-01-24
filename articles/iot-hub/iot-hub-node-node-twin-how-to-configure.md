---
title: "Использование свойств двойников устройств Центра Интернета вещей (Node) | Документация Майкрософт"
description: "Настройка устройств с помощью двойников устройств Центра Интернета вещей Azure. Используйте пакеты SDK для Центра Интернета вещей Azure для Node.js, чтобы реализовать приложение имитации устройства и приложение службы, которое изменяет конфигурацию устройства с помощью двойника устройства."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: 397dffe8ec93ced9196bce8fcc12a058c6876bd4


---
# <a name="use-desired-properties-to-configure-devices-node"></a>Настройка устройств с помощью требуемых свойств (Node)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

По завершении работы с этим руководством у вас будет два консольных приложения Node.js:

* **SimulateDeviceConfiguration.js** — это приложение имитации устройства, которое ожидает обновления требуемой конфигурации и сообщает о состоянии обновления имитации конфигурации.
* **SetDesiredConfigurationAndQuery.js** — это внутреннее приложение Node.js, которое задает требуемую конфигурацию на устройстве и выполняет запрос к процессу обновления конфигурации.

> [!NOTE]
> Статья [IoT Hub SDKs][lnk-hub-sdks] (Пакеты SDK для Центра Интернета вещей) содержит сведения о разных пакетах SDK для Azure IoT, с помощью которых можно создать приложения для устройств и внутренние приложения.
> 
> 

Для работы с этим учебником требуется:

* Node.js версии 0.10.x или более поздней.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

Если вы выполнили указания руководства [Приступая к работе с двойниками устройств (предварительная версия)][lnk-twin-tutorial], то у вас уже есть Центр Интернета вещей и удостоверение устройства **myDeviceId**. Вы можете перейти к разделу [Создание приложения имитации устройства][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>Создание приложения имитации устройства
В этом разделе вы создаете консольное приложение Node.js, которое подключается к центру как **myDeviceId**, ожидает обновления требуемой конфигурации и сообщает об обновлениях в ходе обновления имитации конфигурации.

1. Создайте пустую папку с именем **simulatedeviceconfiguration**. В папке **simulatedeviceconfiguration** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **simulatedeviceconfiguration** выполните следующую команду, чтобы установить пакеты **azure-iot-device** и **azure-iot-device-mqtt**.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. В текстовом редакторе создайте файл **SimulateDeviceConfiguration.js** в папке **simulatedeviceconfiguration**.
4. Добавьте следующий код в файл **SimulateDeviceConfiguration.js** и замените заполнитель **{device connection string}** строкой подключения устройства, скопированной при создании удостоверения устройства **myDeviceId**.
   
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
   
    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. После инициализации объекта **Client** предыдущий код извлекает двойник устройства для **myDeviceId** и подключает обработчик для обновления требуемых свойств. Обработчик проверяет, отправлен ли запрос на изменение конфигурации. Для этого он сравнивает идентификаторы конфигураций, а затем вызывает метод, который запускает изменение конфигурации.
   
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
   
    Метод **initConfigChange** обновляет сообщаемые свойства в объекте локального двойника устройства с помощью запроса на обновление конфигурации и устанавливает для состояния значение **Ожидание**, а затем обновляет двойник устройства в службе. После успешного обновления двойника устройства он имитирует длительный процесс, который завершается выполнением **completeConfigChange**. Этот метод обновляет сообщаемые свойства локального двойника устройства, установив для состояния значение **Success** и удалив объект **pendingConfig**. Затем он обновляет двойник устройства в службе.
   
    Обратите внимание, что для сокращения нагрузки на полосу пропускания при обновлении сообщаемых свойств указываются только свойства, которые нужно изменить (**patch** в коде выше), а не заменяется весь документ.
   
   > [!NOTE]
   > В этом руководстве не имитируется поведение при одновременном обновлении конфигураций. В некоторых случаях обновление конфигурации может пройти в соответствии с изменениями целевой конфигурации, в других может потребоваться поместить изменения в очередь, а в остальных они могут быть отклонены с ошибкой. Следует учитывать желаемое поведение для конкретной конфигурации и добавить соответствующую логику перед ее изменением.
   > 
   > 
6. Запустите приложение устройства:
   
        node SimulateDeviceConfiguration.js
   
    Отобразится сообщение `retrieved device twin`. Не отключайте приложение.

## <a name="create-the-service-app"></a>Создание приложения службы
В этом разделе вы создадите консольное приложение Node.js, которое обновляет *требуемые свойства* двойника устройства, связанного с **myDeviceId**, с использованием нового объекта конфигурации телеметрии. Затем оно выполняет запрос к двойникам устройств, которые хранятся в Центре Интернета вещей, и показывает разницу между требуемой и сообщаемой конфигурацией устройства.

1. Создайте пустую папку с именем **setdesiredandqueryapp**. В папке **setdesiredandqueryapp** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **setdesiredandqueryapp** выполните следующую команду, чтобы установить пакет **azure-iothub**.
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. В текстовом редакторе создайте файл **SetDesiredAndQuery.js** в папке **addtagsandqueryapp**.
4. Добавьте следующий код в файл **SetDesiredAndQuery.js** и замените заполнитель **{iot hub connection string}** на строку подключения Центра Интернета вещей, скопированную при создании центра:
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{iot hub connection string}';
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

    Объект **Registry** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства из службы. После инициализации объекта **Registry** предыдущий код извлекает двойник устройства для **myDeviceId** и обновляет его требуемые свойства, используя новый объект конфигурации телеметрии. После этого он вызывает функцию **queryTwins** каждые 10 секунд.

    > [!IMPORTANT]
    > Это приложение выполняет запрос к Центру Интернета вещей каждые 10 секунд в качестве примера. Используйте запросы для создания представляемых пользователям отчетов на многих устройствах, а не для обнаружения изменений. Если вашему решению требуется отправка уведомлений о событиях устройства в реальном времени, используйте [сообщения, отправляемые с устройства в облако][lnk-d2c].
    > 
    >.

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
   
    Предыдущий код выполняет запрос к двойникам устройств, которые хранятся в Центре Интернета вещей, и выводит требуемые и сообщаемые конфигурации телеметрии. Чтобы узнать, как создавать эффективные отчеты на всех устройствах, см. статью [Справочник по языку запросов для двойников и заданий][lnk-query].
2. Запустив файл **SimulateDeviceConfiguration.js**, запустите приложение с помощью следующей команды:
   
        node SetDesiredAndQuery.js 5m
   
    Состояние сообщаемой конфигурации изменится с **Success** на **Pending** и снова на **Success**, а значение активной частоты отправки изменится с 24 часов на пять минут.
   
   > [!IMPORTANT]
   > Результаты запроса выводятся после операции сообщения устройства с задержкой (не более минуты). Таким образом обеспечивается работа инфраструктуры запросов в очень широком масштабе. Для получения согласованных представлений одного двойника устройства используйте метод **getDeviceTwin** в классе **Registry**.
   > 
   > 

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы установили требуемую конфигурацию в качестве *требуемых свойств* из внутреннего приложения и написали код приложения для имитации устройства, которое обнаруживает изменения и имитирует многоэтапный процесс обновления, о состоянии которого сообщается двойнику устройства в качестве *сообщаемых свойств*.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (учебник [Приступая к работе с Центром Интернета вещей Azure (Node)][lnk-iothub-getstarted]);
* запланировать или выполнить операции на больших наборах устройств (см. учебник [Планирование и трансляция заданий][lnk-schedule-jobs];
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (учебник [Use direct methods][lnk-methods-tutorial] (Использование прямых методов)).

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app



<!--HONumber=Dec16_HO1-->


