## Создание удостоверения устройства

В этом разделе вам предстоит написать консольное приложение Node.js, которое создает новое удостоверение устройства в реестре удостоверений в центре IoT. Устройство может подключиться к центру IoT, только если в реестре удостоверений устройств есть соответствующая запись. Более подробную информацию см. в разделе **Реестр удостоверений устройств** [руководства разработчика по центру IoT][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которого выполняется идентификация устройства во время отправки сообщений с устройства в облако для центра IoT.

1. Создайте новую пустую папку с именем **createdeviceidentity**. В папке **createdeviceidentity** создайте новый файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **createdeviceidentity** выполните следующую команду, чтобы установить пакет **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. В текстовом редакторе создайте новый файл **CreateDeviceIdentity.js** в папке **createdeviceidentity**.

4. Добавьте следующую инструкцию `require` в начало файла **CreateDeviceIdentity.js**:

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Добавьте следующий код в файл **CreateDeviceIdentity.js**, заменив заполнитель строкой подключения для центра IoT, созданного в предыдущем разделе.

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

8. Чтобы запустить приложение **createdeviceidentity**, выполните следующую команду в командной строке в папке createdeviceidentity.

    ```
    node CreateDeviceIdentity.js 
    ```

9. Запишите **идентификатор устройства** и **ключ устройства**. Эти данные понадобятся вам позже, когда вы будете создавать приложение, которое подключается к центру IoT как устройство.

> [AZURE.NOTE] В реестре удостоверений центра IoT хранятся только идентификаторы устройств для предоставления безопасного доступа к концентратору. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок "Вкл./Выкл.", который позволяет отключить доступ для отдельного устройства. Если в вашем приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков центра IoT][lnk-devguide-identity].

## Получение сообщений с устройства в облако

В этом разделе вы создадите консольное приложение Node.js, которое считывает сообщения, передаваемые с устройства в облако из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве [Как обрабатывать сообщения, отправляемые с устройства в облако][lnk-processd2c-tutorial] показано, как обрабатывать сообщения, отправляемые с устройства в облако, с применением масштабирования. В руководстве [Приступая к работе с концентраторами событий][lnk-eventhubs-tutorial] приводятся дополнительные сведения о том, как обрабатываются сообщения из концентраторов событий. Это руководство применяется к конечным точкам центра IoT, совместимым с концентраторами событий.

1. Создайте новую пустую папку с именем **readdevicetocloudmessages**. В папке **readdevicetocloudmessages** создайте новый файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **readdevicetocloudmessages** выполните следующую команду, чтобы установить пакеты **amqp10** и **bluebird**:

    ```
    npm install amqp10 bluebird --save
    ```

3. В текстовом редакторе создайте новый файл **ReadDeviceToCloudMessages.js** в папке **readdevicetocloudmessages**.

4. Добавьте следующие инструкции `require` в начало файла **ReadDeviceToCloudMessages.js**:

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Добавьте следующие объявления переменных, заменив заполнители ранее записанными значениями. Значение заполнителя **{your event hub-compatible namespace}** поступает из **конечной точки, совместимой с концентраторами событий** и принимает форму **xxxxnamespace.servicebus.windows.net**.

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Предполагается, что вы создали центр IoT на уровне F1 (Бесплатный). Бесплатный центр IoT содержит два раздела с именами 0 и 1. Если вы создали центр IoT с помощью одной из ценовых категорий, измените код, чтобы создать **MessageReceiver** для каждого раздела.

6. Добавьте следующее определение фильтра. Это приложение использует фильтр во время создания приемника, чтобы приемник считывал только сообщения, отправленные в центр IoT после запуска получателя. Это удобно в тестовой среде, где можно увидеть текущий набор сообщений, но в рабочей среде код должен обрабатывать все сообщения. Дополнительные сведения см. в руководстве [Как обрабатывать сообщения, отправляемые с устройства в облако, с помощью центра IoT][lnk-processd2c-tutorial].

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

<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0128_2016-->