---
title: Сообщения облака на устройство с Центра Интернета вещей Azure | Документации Майкрософт
description: Как отправлять сообщения из облака на устройство из Центра Интернета вещей Azure, используя пакеты SDK Интернета вещей Azure для Node.js. Для получения сообщений, отправляемых из облака на устройство, следует изменить приложение имитации устройства, а для отправки таких сообщений следует изменить внутреннее приложение.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 526fdce85df388fec04dd5df35183200c1478570
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Отправка сообщений из облака на устройства с помощью Центра Интернета вещей (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Введение
Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. В руководстве [Приступая к работе с Центром Интернета вещей Azure (Node)] показано, как создать Центр Интернета вещей, подготовить в нем удостоверение устройства и написать код приложения для имитации устройства, которое отправляет сообщения из устройства в облако.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Это руководство является логическим продолжением статьи [Приступая к работе с Центром Интернета вещей Azure (Node)]. В нем показано следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.
* Получение на устройстве сообщений, передаваемых из облака на устройство.
* Запрос из серверной части решения подтверждения доставки (*отзыва*) для сообщений, отправленных на устройство из Центра Интернета вещей.

Дополнительные сведения о сообщениях, отправляемых из облака на устройство, см. в [руководстве разработчика для Центра Интернета вещей][IoT Hub developer guide - C2D].

По завершении работы с этим руководством запустите два консольных приложения Node.js:

* **SimulatedDevice**, измененную версию приложения, созданного в руководстве [Приступая к работе с Центром Интернета вещей Azure (Node)]. Это приложение подключается к центру IoT и получает сообщения с облака на устройство.
* **SendCloudToDeviceMessage**, которое отправляет сообщение из облака в приложение для имитации устройства с помощью Центра Интернета вещей, а затем получает подтверждение о его доставке.

> [!NOTE]
> В Центре Интернета вещей реализована поддержка для пакетов SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые указания по связыванию устройства с кодом из этого руководства, а также по подключению к Центру Интернета вещей Azure см. в [центре разработчиков для Интернета вещей Azure].
> 
> 

Для работы с этим учебником требуется:

* Node.js 4.0.x или более поздней версии.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства
В этом разделе вы измените приложение для имитации устройства, созданное в разделе [Приступая к работе с Центром Интернета вещей Azure (Node)], для получения от Центра Интернета вещей сообщений из облака на устройство.

1. В текстовом редакторе откройте файл SimulatedDevice.js.
2. Измените функцию **connectCallback** для обработки сообщений, отправленных из Центра Интернета вещей. В этом примере устройство всегда вызывает функцию **complete** для уведомления Центра Интернета вещей о том, что сообщение обработано. Новая версия функции **connectCallback** выглядит как следующий фрагмент кода.
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
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
   
   > [!NOTE]
   > Если в качестве транспорта вместо MQTT или AMQP используется HTTPS, то экземпляр **DeviceClient** редко проверяет наличие сообщений от Центра Интернета вещей (реже чем каждые 25 минут). Дополнительные сведения о различиях между поддержкой MQTT, AMQP и HTTPS, а также регулировании Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство
В этом разделе создается консольное приложение Node.js, которое отправляет сообщения, передаваемые из облака на устройство, в приложение имитации устройства. Необходим идентификатор устройства, добавленного при изучении руководства [Приступая к работе с Центром Интернета вещей Azure (Node)]. Кроме того, нужна строка подключения для вашего экземпляра Центра Интернета вещей, которую можно найти на [портале Azure].

1. Создайте пустую папку **sendcloudtodevicemessage**. В папке **sendcloudtodevicemessage** создайте файл package.json, введя в командной строке следующую команду. Примите значения по умолчанию:
   
    ```shell
    npm init
    ```
2. В командной строке в папке **sendcloudtodevicemessage** выполните следующую команду, чтобы установить пакет **azure-iothub**.
   
    ```shell
    npm install azure-iothub --save
    ```
3. В текстовом редакторе создайте файл **SendCloudToDeviceMessage.js** в папке **sendcloudtodevicemessage**.
4. Добавьте следующие операторы `require` в начало файла **SendCloudToDeviceMessage.js** .
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Добавьте в файл **SendCloudToDeviceMessage.js** следующий код. Замените заполнитель строки {iot hub connection string} значением строки для Центра Интернета вещей, созданного при изучении руководства [Приступая к работе с Центром Интернета вещей Azure (Node)]. Замените заполнитель {device id} значением идентификатора устройства, добавленного при изучении руководства [Приступая к работе с Центром Интернета вещей Azure (Node)]:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Добавьте следующую функцию для вывода результатов операции в консоль.
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Добавьте следующую функцию для вывода сообщений о доставке в консоль.
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Добавьте приведенный ниже код для отправки сообщения на устройство и обработки сообщения о доставке, получаемого после подтверждения устройством получения сообщения из облака на устройство.
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```
9. Сохраните и закройте файл **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы начать отправку данных телеметрии в Центр Интернета вещей и прослушивание сообщений из облака на устройства:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Запуск приложения виртуального устройства][img-simulated-device]
2. В командной строке в папке **sendcloudtodevicemessage** выполните следующую команду, чтобы отправить сообщение из облака на устройство и ожидать подтверждения доставки:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Запуск приложения для отправки команды из облака на устройство][img-send-command]
   
   > [!NOTE]
   > Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Transient Fault Handling](Обработка временного сбоя).
   > 
   > 

## <a name="next-steps"></a>Дополнительная информация
В этом учебнике вы научились отправлять и получать сообщения с облака на устройство. 

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по Azure IoT Suite].

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Приступая к работе с Центром Интернета вещей Azure (Node)]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[руководстве разработчика для Центра Интернета вещей]: iot-hub-devguide.md
[центре разработчиков для Интернета вещей Azure]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[портале Azure]: https://portal.azure.com
[документации по Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
