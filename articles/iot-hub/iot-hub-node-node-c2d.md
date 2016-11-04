<properties
    pageTitle="Отправка сообщений из облака на устройства с помощью центра IoT | Microsoft Azure"
    description="Следуйте инструкциям этого руководства, чтобы научиться отправлять сообщения из облака на устройства в центре IoT Azure с помощью Java."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>


# <a name="tutorial-how-to-send-cloudtodevice-messages-with-iot-hub-and-nodejs"></a>Учебник. Как отправлять сообщения из облака на устройства с помощью центра IoT и Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Введение

Центр Azure IoT — полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью приложения. В учебнике [Приступая к работе с центром IoT] показано, как создать центр IoT, подготовить в нем удостоверение устройства и написать код виртуального устройства, которое отправляет сообщения с устройства в облако.

Это руководство является логическим продолжением статьи [Приступая к работе с центром Azure IoT с использованием Java]. В нем показано следующее:

- Отправка сообщения, передаваемого из облака на устройство, из облачной серверной части приложения на одно устройство через центр IoT.
- Получение на устройстве сообщений, передаваемых из облака на устройство.
- Запрос из облачной серверной части приложения подтверждения доставки (*отзыва*) для сообщений, отправленных на устройство из центра IoT.

Дополнительные сведения о сообщениях, отправляемых с облака на устройство, можно найти в [руководстве разработчика по Центру Интернета вещей][Руководстве разработчика по центру IoT].

По завершении этого учебника запускаются два консольных приложения Node.js:

* **SimulatedDevice**, измененную версию приложения, созданного в руководстве [Начало работы с Центром Интернета вещей ]. Это приложение подключается к центру IoT и получает сообщения с облака на устройство.
* **SendCloudToDeviceMessage**, которое отправляет сообщение из облака на имитацию устройства с помощью центра IoT, а затем получает подтверждение о его доставке.

> [AZURE.NOTE] Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые указания по связыванию устройства с кодом из этого руководства, а также по подключению к Центру Интернета вещей Azure см. в [центре разработчиков для Интернета вещей Azure].

Для работы с этим учебником требуется:

+ Node.js версии 0.10.x или более поздней.

+ Активная учетная запись Azure. (Если ее нет, можно создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. в статье о [бесплатной пробной версии Azure][lnk-free-trial].

## <a name="receive-messages-on-the-simulated-device"></a>Получение сообщений на виртуальном устройстве

В этом разделе вы измените приложение имитации устройства, созданное в разделе [Приступая к работе с центром IoT] , для получения сообщений из облака на устройство от центра IoT.

1. В текстовом редакторе откройте файл SimulatedDevice.js.

2. Измените функцию **connectCallback** для обработки сообщений, отправленных из центра IoT. В этом примере устройство всегда вызывает функцию **complete** для уведомления центра IoT о том, что сообщение обработано. Новая версия функции **connectCallback** выглядит следующим образом.

    ```
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
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Если в качестве транспорта вместо AMQP или MQTT используется HTTP/1, то экземпляр **DeviceClient** редко проверяет наличие сообщений от Центра Интернета вещей (реже чем каждые 25 минут). Дополнительную информацию о различиях между поддержкой AMQP, MQTT и HTTP/1, а также регулировании Центра Интернета вещей см. в [Руководстве разработчика по Центру Интернета вещей][Руководстве разработчика по центру IoT].

## <a name="send-a-cloudtodevice-message"></a>Отправка сообщения из облака на устройство

В этом разделе создается консольное приложение Node.js, которое отправляет сообщения, передаваемые из облака на устройство, в приложение имитации устройства. Необходим идентификатор устройства, добавленного при изучении учебника [Начало работы с центром IoT] . Кроме того, нужна строка подключения для центра IoT, которую можно найти на [портале Azure].

1. Создайте пустую папку **sendcloudtodevicemessage**. В папке **sendcloudtodevicemessage** создайте файл package.json, введя в командной строке следующую команду. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **sendcloudtodevicemessage** выполните следующую команду, чтобы установить пакет **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. В текстовом редакторе создайте файл **SendCloudToDeviceMessage.js** в папке **sendcloudtodevicemessage**.

4. Добавьте следующие операторы `require` в начало файла **SendCloudToDeviceMessage.js** .

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Добавьте в файл **SendCloudToDeviceMessage.js** следующий код. Замените заполнитель строки подключения значением строки подключения для Центра Интернета вещей, созданного при изучении учебника [Приступая к работе с центром Azure IoT с использованием Node.js] . Замените заполнитель целевого устройства значением идентификатора устройства, добавленного при изучении учебника [Приступая к работе с центром Azure IoT с использованием Node.js] :

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Добавьте следующую функцию для вывода результатов операции в консоль.

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Добавьте следующую функцию для вывода сообщений о доставке в консоль.

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Добавьте приведенный ниже код для отправки сообщения на устройство и обработки сообщения о доставке, получаемого после подтверждения устройством получения сообщения из облака на устройство.

    ```
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

7. Сохраните и закройте файл **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы начать отправку данных телеметрии в центр IoT и прослушивание сообщений из облака на устройство.

    ```
    node SimulatedDevice.js 
    ```

    ![Запуск приложения виртуального устройства][img-simulated-device]

2. В командной строке в папке **sendcloudtodevicemessage** выполните следующую команду, чтобы отправить сообщение из облака на устройство и ожидать подтверждения доставки.

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Запуск приложения для отправки команды c2d][img-send-command]

    > [AZURE.NOTE] Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя].

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство. 

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по Azure IoT Suite].

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в статье [Руководство разработчика по центру Azure IoT (IoT — Интернет вещей)].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Приступая к работе с центром IoT]: iot-hub-node-node-getstarted.md
[Руководство разработчика по Центру Интернета вещей. Передача сообщений из облака на устройство]: iot-hub-devguide-messaging.md
[Руководстве разработчика по центру IoT]: iot-hub-devguide.md
[Центре разработчика для IoT в Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Обработка временного сбоя]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[портал Azure]: https://portal.azure.com
[Набор Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/


<!---HONumber=Oct16_HO2-->


