---
title: "Приступая к работе с Центром Интернета вещей Azure (Node) | Документация Майкрософт"
description: "Узнайте, как отправлять сообщения из устройства на облако в Центр Интернета вещей Azure с помощью пакетов SDK Интернета вещей для Node.js. Создайте виртуальное устройство и приложения службы для регистрации устройства, отправки сообщений и чтения сообщений из Центра Интернета вещей."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b360d5a08abed7d65d8c39f7a957412656f7825
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>Подключение виртуального устройства к Центру Интернета вещей с помощью Node

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

С помощью этого руководства вы создадите три консольных приложения Node.js:

* **CreateDeviceIdentity.js**. Создает удостоверение устройства и соответствующий ключ безопасности для подключения имитирующего устройство приложения.
* **ReadDeviceToCloudMessages.js**. Отображает данные телеметрии, отправленные приложением, которое имитирует устройство.
* **SimulatedDevice.js** — подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и отправляет сообщения телеметрии с частотой один раз в секунду с использованием протокола MQTT.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.

Для работы с этим учебником требуется:

* Node.js 4.0.x или более поздней версии.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Теперь центр IoT создан и у вас есть соответствующие имя узла и строка подключения, необходимые для завершения работы с этим руководством.

## <a name="create-a-device-identity"></a>Создание удостоверения устройства

В этом разделе вам предстоит написать консольное приложение Node.js, которое создает удостоверение устройства в реестре удостоверений в центре IoT. Устройство может подключиться к Центру Интернета вещей, только если в реестре удостоверений есть соответствующая запись. Дополнительные сведения см. в разделе, посвященном **реестру удостоверений**, в [руководстве разработчика для Центра Интернета вещей Azure][lnk-devguide-identity]. Запустите это приложение, чтобы создать уникальный идентификатор устройства и ключ, используемые устройством для собственной идентификации при отправке сообщений из устройства в облако.

1. Создайте пустую папку с именем `createdeviceidentity`. В папке `createdeviceidentity` создайте файл package.json, указав приведенную ниже команду в командной строке. Примите значения по умолчанию:

    ```cmd/sh
    npm init
    ```

2. В командной строке в папке `createdeviceidentity` выполните следующую команду, чтобы установить пакет SDK службы `azure-iothub`:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. В текстовом редакторе создайте файл **CreateDeviceIdentity.js** в папке `createdeviceidentity`.

4. Добавьте следующую инструкцию `require` в начало файла **CreateDeviceIdentity.js** :

    ```nodejs
    'use strict';

    var iothub = require('azure-iothub');
    ```

5. Добавьте приведенный ниже код в файл **CreateDeviceIdentity.js**. Замените значение заполнителя строкой подключения к Центру Интернета вещей, созданному в предыдущем разделе.

    ```nodejs
    var connectionString = '{iothub connection string}';

    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Добавьте следующий код, чтобы создать определение устройства в реестре удостоверений в Центре Интернета вещей. Этот код создает устройство, если идентификатор устройства отсутствует в реестре удостоверений. В противном случае он возвращает ключ существующего устройства:

    ```nodejs
    var device = {
      deviceId: 'myFirstNodeDevice'
    }
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

7. Сохраните и закройте файл **CreateDeviceIdentity.js** .

8. Чтобы запустить приложение `createdeviceidentity`, выполните следующую команду в командной строке в папке `createdeviceidentity`:

    ```cmd/sh
    node CreateDeviceIdentity.js 
    ```

9. Запишите значения полей **Идентификатор устройства** и **Device key** (Ключ устройства). Эти значения понадобятся вам позже, когда вы будете создавать приложение, которое подключается к центру IoT как устройство.

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только идентификаторы устройств, необходимые для безопасного доступа к Центру Интернета вещей. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок включения или выключения, который позволяет вам отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве разработчика для Центра Интернета вещей][lnk-devguide-identity].

<a id="D2C_node"></a>
## <a name="receive-device-to-cloud-messages"></a>Получение сообщений с устройства в облако

В этом разделе вы научитесь создавать консольное приложение Node.js, которое считывает сообщения, передаваемые с устройства в облако из Центра Интернета вещей. Центр Интернета вещей предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве по [обработке сообщений, передаваемых с устройства в облако][lnk-process-d2c-tutorial], показано, как обрабатывать такие сообщения в больших количествах. В руководстве по [началу работы с концентраторами событий][lnk-eventhubs-tutorial] приведены дополнительные сведения, касающиеся конечных точек Центра Интернета вещей, совместимых с концентраторами событий.

> [!NOTE]
> Совместимая с концентраторами событий конечная точка для чтения сообщений, отправляемых с устройства в облако, всегда использует протокол AMQP.

1. Создайте пустую папку с именем `readdevicetocloudmessages`. В папке `readdevicetocloudmessages` создайте файл package.json, указав приведенную ниже команду в командной строке. Примите значения по умолчанию:

    ```cmd/sh
    npm init
    ```

2. В командной строке в папке `readdevicetocloudmessages` выполните следующую команду, чтобы установить пакет **azure-event-hubs**:

    ```cmd/sh
    npm install azure-event-hubs --save
    ```

3. В текстовом редакторе создайте файл **ReadDeviceToCloudMessages.js** в папке `readdevicetocloudmessages`.

4. Добавьте следующие инструкции `require` в начало файла **ReadDeviceToCloudMessages.js** :

    ```nodejs
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Добавьте следующее объявление переменной и замените заполнитель строкой подключения к своему экземпляру Центра Интернета вещей:

    ```nodejs
    var connectionString = '{iothub connection string}';
    ```

6. Добавьте следующие две функции для вывода результатов на консоль:

    ```nodejs
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Добавьте следующий код, чтобы создать **EventHubClient**, открыть подключение к Центру Интернета вещей и создать получатель для каждой секции. Это приложение использует фильтр во время создания приемника, чтобы приемник считывал только сообщения, отправленные в центр IoT после запуска получателя. Этот фильтр удобно использовать в тестовой среде для просмотра только текущего набора сообщений. В рабочей среде убедитесь, что ваш код обрабатывает все сообщения. Дополнительные сведения см. в руководстве [по обработке сообщений Центра Интернета вещей, отправляемых с устройства в облако][lnk-process-d2c-tutorial].

    ```nodejs
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Сохраните и закройте файл **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе вам предстоит создать консольное приложение Node.js, которое имитирует устройство, отправляющее сообщения с устройства в облако в центр IoT.

1. Создайте пустую папку с именем `simulateddevice`. В папке `simulateddevice` создайте файл package.json, указав приведенную ниже команду в командной строке. Примите значения по умолчанию:

    ```cmd/sh
    npm init
    ```

2. В командной строке в папке `simulateddevice` выполните следующую команду, чтобы установить пакет SDK для устройств **azure-iot-device** и пакет **azure-iot-device-mqtt**.

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. В текстовом редакторе создайте файл **SimulatedDevice.js** в папке `simulateddevice`.

4. Добавьте следующие инструкции `require` в начало файла **SimulatedDevice.js** :

    ```nodejs
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Добавьте переменную `connectionString`, чтобы создать с ее помощью экземпляр **клиента**. Замените `{youriothostname}` именем Центра Интернета вещей, созданного в разделе о *создании центра Интернета вещей*. Замените `{yourdevicekey}` ключом устройства, созданным в разделе *Создание удостоверения устройства*.

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';

    var client = clientFromConnectionString(connectionString);
    ```

6. Добавьте следующую функцию для вывода данных приложения:

    ```nodejs
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Создайте обратный вызов и используйте функцию **setInterval**, чтобы отправлять сообщение в Центр Интернета вещей каждую секунду:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. Откройте подключение к Центру Интернета вещей и начните отправку сообщений.

    ```nodejs
    client.open(connectCallback);
    ```

9. Сохраните и закройте файл **SimulatedDevice.js** .

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке в папке `readdevicetocloudmessages` выполните следующую команду, чтобы начать мониторинг Центра Интернета вещей:

    ```cmd/sh
    node ReadDeviceToCloudMessages.js 
    ```

    ![Приложение службы Центра Интернета вещей на платформе Node.js для мониторинга сообщений, отправляемых с устройства в облако][7]

2. В командной строке в папке `simulateddevice` выполните следующую команду, чтобы начать отправку данных телеметрии в Центр Интернета вещей:

    ```cmd/sh
    node SimulatedDevice.js
    ```

    ![Приложение устройства Центра Интернета вещей на платформе Node.js для отправки сообщений с устройства в облако][8]

3. На плитке **Использование** на [портале Azure][lnk-portal] отображается количество сообщений, отправленных в Центр Интернета вещей.

    ![Плитка "Использование" на портале Azure, отображающая количество сообщений, отправленных в Центр Интернета вещей][43]

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Это удостоверение позволяет приложению виртуального устройства отправлять в Центр Интернета вещей сообщения, передаваемые из устройства в облако. Кроме того, мы создали приложение, которое отображает сообщения, полученные Центром Интернета вещей.

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [Подключение устройства к Azure IoT][lnk-connect-device]
* [How to get started with device management (Node)][lnk-device-management] (Начало работы с управлением устройствами (Node))
* [Развертывание ИИ на пограничных устройствах с использованием Azure IoT Edge][lnk-iot-edge]

Сведения о том, как расширить решение для Интернета вещей и обрабатывать сообщения, отправляемые с устройства в облако в большом количестве, см. [здесь][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
