<properties
	pageTitle="Начало работы с центром IoT Azure. Платформа Node.js | Microsoft Azure"
	description="Начальное руководство по работе с центром IoT Azure для платформы Node.js. Используя центр IoT Azure, Node.js и пакеты SDK для Интернета вещей Microsoft Azure, создайте собственное решение IoT."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Приступая к работе с центром Azure IoT с использованием Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Введение

Центр IoT Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств Интернета вещей (IoT) и серверной частью решения. Одной из крупнейших задач, стоящих перед проектами IoT, является надежное и безопасное подключение устройств к серверной части решения. Чтобы устранить эту проблему, центр IoT предоставляет следующие возможности:

- глобальный надежный двусторонний обмен сообщениями между облаком и устройством;
- безопасную связь — используются учетные данные безопасности, уникальные для каждого устройства, и контроль доступа;
- простое подключение устройств, так как используются библиотеки устройств для большинства популярных языков и платформ.

В этом учебнике описаны следующие процедуры.

- Создание центра IoT с помощью портала Azure.
- Создание удостоверения устройства в центре IoT.
- Создание имитации устройства, отправляющего данные телеметрии в серверную часть облачного решения.

С помощью этого руководства вы создадите три консольных приложения Node.js:

* **CreateDeviceIdentity.js** — создает удостоверение устройства и соответствующий ключ безопасности для подключения к виртуальному устройству;
* **ReadDeviceToCloudMessages.js** — отображает данные телеметрии, отправленные виртуальным устройством;
* **SimulatedDevice.js** — подключается к центру IoT с созданным ранее удостоверением устройства и отправляет сообщения с частотой один раз в секунду с использованием протокола AMQPS.

> [AZURE.NOTE] Статья [Пакеты SDK для центра IoT][lnk-hub-sdks] содержит сведения о разных пакетах SDK, которые можно использовать для создания приложений, которые будут запущены на устройствах и серверной части решения.

Для работы с этим руководством требуется:

+ Node.js 0.12.x или более поздней версии. <br/> В статье о [подготовке среды разработки][lnk-dev-setup] описывается, как установить Node.js для работы с этим руководством в ОС Windows или Linux.

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Наконец, в колонке "Центр IoT" щелкните **Параметры**, а затем в колонке **Параметры** щелкните **Обмен сообщениями**. Когда откроется колонка **Обмен сообщениями**, запишите значения полей **Имя, совместимое с концентраторами событий** и **Конечная точка, совместимая с концентраторами событий**. Эти значения понадобятся при создании приложения **read-d2c-messages**.

![][6]

Вы создали центр IoT. Теперь у вас есть все необходимое, чтобы выполнить оставшуюся часть инструкций из руководства: имя узла центра IoT, строка подключения к центру IoT, имя и значения конечной точки, совместимые с концентраторами событий.

## Создание удостоверения устройства

В этом разделе вам предстоит написать консольное приложение Node.js, которое создает новое удостоверение устройства в реестре удостоверений в центре IoT. Устройство может подключиться к центру IoT, только если в реестре удостоверений устройств есть соответствующая запись. Более подробную информацию см. в разделе **Реестр удостоверений устройств** [руководства разработчика по центру IoT][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которого выполняется идентификация устройства во время отправки сообщений с устройства в облако для центра IoT.

1. Создайте пустую папку с именем **createdeviceidentity**. В папке **createdeviceidentity** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **createdeviceidentity** выполните следующую команду, чтобы установить пакет **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. В текстовом редакторе создайте файл **CreateDeviceIdentity.js** в папке **createdeviceidentity**.

4. Добавьте следующую инструкцию `require` в начало файла **CreateDeviceIdentity.js**:

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Добавьте следующий код в файл **CreateDeviceIdentity.js**, заменив заполнитель строкой подключения для центра IoT, созданного в предыдущем разделе:

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Добавьте следующий код, чтобы создать новое определение устройства в реестре удостоверений устройств в центре IoT. Этот код создает новое устройство, если идентификатор устройства отсутствует в реестре. В противном случае он возвращает ключ существующего устройства:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
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
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Сохраните и закройте файл **CreateDeviceIdentity.js**.

8. Чтобы запустить приложение **createdeviceidentity**, выполните следующую команду в командной строке в папке createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Запишите **идентификатор устройства** и **ключ устройства**. Эти данные понадобятся вам позже, когда вы будете создавать приложение, которое подключается к центру IoT как устройство.

> [AZURE.NOTE] В реестре удостоверений центра IoT хранятся только идентификаторы устройств для предоставления безопасного доступа к концентратору. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок "Вкл./Выкл.", который позволяет отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков центра IoT][lnk-devguide-identity].

## Получение сообщений с устройства в облако

В этом разделе вы создадите консольное приложение Node.js, которое считывает сообщения, передаваемые с устройства в облако из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве по [обработке сообщений, отправляемых с устройства в облако][lnk-process-d2c-tutorial] показано, как обрабатывать такие сообщения с применением масштабирования. В руководстве по [началу работы с концентраторами событий][lnk-eventhubs-tutorial] приведены дополнительные сведения о том, как обрабатываются сообщения из концентраторов событий. Это руководство применяется к конечным точкам центра IoT, совместимым с концентраторами событий.

> [AZURE.NOTE] Совместимая с концентраторами событий конечная точка для чтения сообщений, отправляемых с устройства в облако, всегда использует протокол AMQPS.

1. Создайте пустую папку с именем **readdevicetocloudmessages**. В папке **readdevicetocloudmessages** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **readdevicetocloudmessages** выполните следующую команду, чтобы установить пакеты **amqp10** и **bluebird**:

    ```
    npm install amqp10 bluebird --save
    ```

3. В текстовом редакторе создайте файл **ReadDeviceToCloudMessages.js** в папке **readdevicetocloudmessages**.

4. Добавьте следующие инструкции `require` в начало файла **ReadDeviceToCloudMessages.js**:

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Добавьте следующие объявления переменных, заменив заполнители ранее записанными значениями. Для заполнителя **{your event hub-compatible namespace}** будет использоваться значение поля **Event Hub-compatible endpoint** (Конечная точка, совместимая с концентраторами событий) на портале в формате **namespace.servicebus.windows.net** (без префикса **sb://*).

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Предполагается, что вы создали центр IoT на уровне F1 (Бесплатный). Бесплатный центр IoT содержит два раздела с именами 0 и 1. Если центр IoT создан с использованием одной из ценовых категорий, измените код, чтобы создать **MessageReceiver** для каждого раздела.

6. Добавьте следующее определение фильтра. Это приложение использует фильтр во время создания приемника, чтобы приемник считывал только сообщения, отправленные в центр IoT после запуска получателя. Это удобно в тестовой среде, где можно увидеть текущий набор сообщений, но в рабочей среде код должен обрабатывать все сообщения. Дополнительные сведения см. в руководстве [Как обрабатывать сообщения, отправляемые с устройства в облако, с помощью центра IoT][lnk-process-d2c-tutorial].

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. Добавьте следующий код, чтобы создать адрес получения и AMQP-клиент:

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. Добавьте следующие две функции для вывода результатов на консоль:

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. Добавьте следующую функцию, которая выступает в качестве приемника для выбранного раздела, с помощью фильтра:

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. Добавьте следующий код, чтобы подключиться к конечной точке, совместимой с концентраторами событий, и запустить приемники:

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. Сохраните и закройте файл **ReadDeviceToCloudMessages.js**.

## Создание приложения виртуального устройства

В этом разделе вам предстоит создать консольное приложение Node.js, которое имитирует устройство, отправляющее сообщения с устройства в облако в центр IoT.

1. Создайте пустую папку с именем **simulateddevice**. В папке **simulateddevice** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы установить пакет **azure-iot-device-amqp**:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. В текстовом редакторе создайте файл **SimulatedDevice.js** в папке **simulateddevice**.

4. Добавьте следующие инструкции `require` в начало файла **SimulatedDevice.js**:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Добавьте переменную **connectionString**, чтобы создать с ее помощью клиент устройства. Замените значения **{youriothubname}** именем центра IoT, а **{yourdeviceid}** и **{yourdevicekey}** — значениями для устройства, созданными в разделе *Создание удостоверения устройства*:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Добавьте следующую функцию для вывода данных приложения:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Создайте обратный вызов и используйте функцию **setInterval**, чтобы отправлять новое сообщение в центр IoT каждую секунду:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. Откройте подключение к центру IoT и начните отправку сообщений.

    ```
    client.open(connectCallback);
    ```

9. Сохраните и закройте файл **SimulatedDevice.js**.

> [AZURE.NOTE] Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].


## Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке в папке **readdevicetocloudmessages** выполните следующую команду, чтобы начать наблюдение за центром IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы начать отправку данных телеметрии в центр IoT:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. На плитке **Использование** на [портале Azure][lnk-portal] отображается количество сообщений, отправленных в центр:

    ![][43]

## Дальнейшие действия

В этом руководстве описана настройка нового центра IoT на портале и создание удостоверения устройства в реестре удостоверений центра. Это удостоверение устройства позволяет приложению виртуального устройства отправлять в центр сообщения, передаваемые из устройства в облако. Также описано создание приложения, которое отображает сообщения, полученные центром. Дальнейшее описание функций и сценариев центра IoT см. в следующих руководствах:

- [Отправка сообщений с облака на устройство в центре IoT][lnk-c2d-tutorial] — описывается, как отправлять сообщения на устройства и обрабатывать подтверждения о доставке от центра IoT.
- [Обработка сообщений, отправляемых с устройств в облако][lnk-process-d2c-tutorial] — описывается, как надежно обрабатывать данные телеметрии и интерактивные сообщения, поступающие от устройств.
- [Передача файлов с устройств][lnk-upload-tutorial] — описывается модель использования сообщений, отправляемых из облака на устройства, для упрощения передачи файлов с устройств.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->