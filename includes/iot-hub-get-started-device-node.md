## Создание приложения виртуального устройства

В этом разделе вам предстоит создать консольное приложение Node.js, которое имитирует устройство, отправляющее сообщения с устройства в облако в центр IoT.

1. Создайте пустую папку с именем **simulateddevice**. В папке **simulateddevice** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы установить пакет **azure-iot-device-amqp**:

    ```
    npm install azure-iot-device-amqp --save
    ```

3. В текстовом редакторе создайте файл **SimulatedDevice.js** в папке **simulateddevice**.

4. Добавьте следующие инструкции `require` в начало файла **SimulatedDevice.js**:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Добавьте переменную **connectionString**, чтобы создать с ее помощью клиент устройства. Замените значения **{youriothubname}** именем центра IoT, а **{yourdeviceid}** и **{yourdevicekey}** — значениями для устройства, сгенерированными в разделе *Создание удостоверения устройства*:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Добавьте следующую функцию для вывода данных приложения:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ': message sent');
      };
    }
    ```

7. Используйте функцию **setInterval**, чтобы отправлять новое сообщение в центр IoT каждую секунду:

    ```
    setInterval(function(){
      var windSpeed = 10 + (Math.random() * 4);
      var data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed });
      var message = new Message(data);
      console.log("Sending message: " + message.getData());
      client.sendEvent(message, printResultFor('send'));
    }, 1000);
    ```

8. Сохраните и закройте файл **SimulatedDevice.js**.

> [AZURE.NOTE] Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/ru-RU/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0128_2016-->