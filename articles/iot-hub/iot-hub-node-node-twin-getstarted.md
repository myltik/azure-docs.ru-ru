<properties
    pageTitle="Приступая к работе с двойниками | Microsoft Azure"
    description="В этом руководстве описано, как использовать двойники"
    services="iot-hub"
    documentationCenter="node"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>


# <a name="tutorial:-get-started-with-device-twins-(preview)"></a>Приступая к работе с двойниками устройств (предварительная версия)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

По завершении работы с этим руководством у вас будет два консольных приложения Node.js:

* **AddTagsAndQuery.js** — это приложение Node.js, предназначенное для запуска из серверной части, которое добавляет теги и выполняет запросы к двойникам устройств.
* **TwinSimulatedDevice.js** — это приложение Node.js, которое имитирует устройство, подключающееся к Центру Интернета вещей с использованием удостоверения устройства, созданного ранее, и сообщает о его условии подключения.

> [AZURE.NOTE] Статья [IoT Hub SDKs][lnk-hub-sdks] (Пакеты SDK Центра Интернета вещей) содержит сведения о разных пакетах SDK, с помощью которых можно создать приложения устройств и серверной части.

Для работы с этим учебником требуется:

+ Node.js версии 0.10.x или более поздней.

+ Активная учетная запись Azure. (Если ее нет, можно создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. в статье о [бесплатной пробной версии Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе вы создадите консольное приложение Node.js, которое добавляет метаданные расположения к двойнику, связанному с **myDeviceId**. Затем оно выполняет запрос к двойникам, хранящимся в концентраторе, и выбирает устройства, которые находятся в США, а затем выбирает устройства, сообщившие о подключении по сети мобильной связи.

1. Создайте пустую папку с именем **addtagsandqueryapp**. В папке **addtagsandqueryapp** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **addtagsandqueryapp** выполните следующую команду, чтобы установить пакет **azure-iothub**:

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. В текстовом редакторе создайте файл **AddTagsAndQuery.js** в папке **addtagsandqueryapp**.

4. Добавьте следующий код в файл **AddTagsAndQuery.js** и замените заполнитель **{строка подключения службы}** на строку подключения, скопированную при создании центра:

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    Объект **Registry** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства из службы. Предыдущий код сначала инициализирует объект **Registry**, затем извлекает двойник для **myDeviceId** и, наконец, обновляет его теги, используя сведения о требуемом расположении.

    После обновления тегов он вызывает функцию **queryTwins**.

7. Добавьте следующий код в конце файла **AddTagsAndQuery.js** для реализации функции **queryTwins**:

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    Предыдущий код выполняет два запроса: первый выбирает только двойников устройства, расположенных на фабрике **Redmond43**, а второй уточняет условия первого запроса и выбирает устройства, подключенные по сети мобильной связи.

    Обратите внимание, что при создании объекта **query** предыдущий код указывает максимальное количество возвращаемых документов. Объект **query** содержит логическое свойство **hasMoreResults**, которое можно использовать для вызова методов **nextAsTwin** несколько раз, чтобы получить все результаты. Метод **next** доступен для результатов, которые не являются двойниками устройств, например результаты статистических запросов.

8. Запустите приложение, выполнив следующую команду:

        node AddTagsAndQuery.js

    В результатах запроса на все устройства, расположенные на фабрике **Redmond43**, отобразится одно устройство, а для запроса на ограничение результатов устройствами, использующими сеть мобильной связи, не отобразится ни одного устройства.

    ![][1]

В следующем разделе вы создадите приложение устройства, которое сообщает сведения о подключении и изменяет результат запроса в предыдущем разделе.

## <a name="create-the-device-app"></a>Создание приложения устройства

В этом разделе вы создадите консольное приложение Node.js, которое подключается к центру как **myDeviceId** и обновляет сообщаемые свойства двойника, добавив в них сведения о подключении по сети мобильной связи.

> [AZURE.NOTE] На данный момент доступ к двойникам устройств можно получить только на устройствах, подключающихся к Центру Интернета вещей по протоколу MQTT. Указания по преобразованию существующего приложения устройства для использования MQTT см. в статье [Поддержка MQTT][lnk-devguide-mqtt].

1. Создайте пустую папку с именем **reportconnectivity**. В папке **reportconnectivity** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **reportconnectivity** выполните следующую команду, чтобы установить пакет SDK для устройства **azure-iot-device** и пакет **azure-iot-device-mqtt**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. В текстовом редакторе создайте файл **ReportConnectivity.js** в папке **reportconnectivity**.

4. Добавьте следующий код в файл **ReportConnectivity.js** и замените заполнитель **{строка подключения устройства}** на строку подключения, скопированную при создании удостоверения устройства **myDeviceId**:

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. После инициализации объекта **Client** предыдущий код извлекает двойник для **myDeviceId** и обновляет его сообщаемое свойство, используя сведения о подключении.

5. Запуск приложения устройства

        node ReportConnectivity.js

    Отобразится сообщение `twin state reported`.

6. Теперь, когда устройство сообщило сведения о подключении, оно должно появиться в обоих запросах. Вернитесь к папке **addtagsandqueryapp** и выполните запросы снова:

        node AddTagsAndQuery.js

    На этот раз **myDeviceId** должен появиться в результатах обоих запросов.

    ![][3]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве описана настройка нового центра IoT на портале и создание удостоверения устройства в реестре удостоверений центра. Вы добавили метаданные устройства в качестве тегов из приложения серверной части и написали код для приложения имитации, чтобы сообщить сведения о подключении устройства в двойнике устройства. Вы также узнали, как запрашивать эти сведения, используя язык запросов на основе SQL Центра Интернета вещей.

Ознакомьтесь со следующими материалами, чтобы узнать как:

- отправить данные телеметрии с устройств (руководство [Начало работы с Центром Интернета вещей][lnk-iothub-getstarted]);
- настроить устройства с помощью требуемых свойств двойника (руководство [Настройка устройств с помощью требуемых устройств][lnk-twin-how-to-configure]);
- управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (руководство [Использование прямых методов][lnk-methods-tutorial]).

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md


<!--HONumber=Oct16_HO2-->


