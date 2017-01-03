---
title: "Использование прямых методов | Документация Майкрософт"
description: "В этом руководстве описано использование прямых методов"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: fd80a245f2cc2415bec487f98360980534df8730


---
# <a name="tutorial-use-direct-methods"></a>Руководство. Использование прямых методов
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

По завершении работы с этим руководством у вас будет два консольных приложения Node.js:

* **CallMethodOnDevice.js**, которое вызывает метод в приложении для имитации устройства и выводит ответ;
* **SimulatedDevice.js**, которое подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и отвечает на метод, вызванный облаком.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.
> 
> 

Для работы с этим учебником требуется:

* Node.js версии 0.10.x или более поздней.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы создадите консольное приложение Node.js, отвечающее на метод, вызванный из облака.

1. Создайте пустую папку с именем **simulateddevice**. В папке **simulateddevice** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы установить пакет SDK для устройства **azure-iot-device** и пакет **azure-iot-device-amqp**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. В текстовом редакторе создайте файл **SimulatedDevice.js** в папке **simulateddevice**.
4. Добавьте следующие инструкции `require` в начало файла **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Добавьте переменную **connectionString** , чтобы создать с ее помощью клиент устройства. Замените **{строка подключения устройства}** на строку подключения, созданную в разделе *Создание удостоверения устройства*:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Добавьте следующую функцию, чтобы реализовать метод на устройстве:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Откройте подключение к Центру Интернета вещей и начните инициализацию прослушивателя метода:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Сохраните и закройте файл **SimulatedDevice.js** .

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, повторную попытку подключения), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.
> 
> 

## <a name="call-a-method-on-a-device"></a>Вызов метода на устройстве
В этом разделе вы создадите консольное приложение Node.js, которое вызывает метод в приложении для имитации устройства и выводит ответ.

1. Создайте пустую папку с именем **callmethodondevice**. В папке **callmethodondevice** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **callmethodondevice** выполните следующую команду, чтобы установить пакет **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. В текстовом редакторе создайте файл **CallMethodOnDevice.js** в папке **callmethodondevice**.
4. Добавьте следующие инструкции `require` в начало файла **CallMethodOnDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. Добавьте следующее объявление переменной и замените заполнитель строкой подключения для центра IoT:
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. Создайте клиент, чтобы открыть подключение к Центру Интернета вещей.
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. Добавьте следующую функцию, чтобы вызвать метод устройства и вывести ответ устройства в консоль:
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. Сохраните и закройте файл **CallMethodOnDevice.js**.

## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы начать прослушивать вызовы из Центра Интернета вещей:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. В командной строке в папке **callmethodondevice** выполните следующую команду, чтобы начать мониторинг Центра Интернета вещей:
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. Устройство отреагирует на метод и выведет сообщение, а приложение, вызвавшее метод, выведет ответ устройства:
   
    ![][9]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы использовали удостоверение устройства, с помощью которого приложение имитации устройства может отвечать на методы, вызванные из облака. Вы также создали приложение, вызывающее методы на устройстве и выводящее ответ устройства. 

Чтобы продолжить знакомство с центром IoT и изучить другие сценарии IoT, см. следующие ресурсы:

* [Начало работы с Центром Интернета вещей]
* [Schedule jobs on multiple devices][lnk-devguide-jobs] (Планирование заданий на нескольких устройствах)

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в учебнике [Планирование и трансляция заданий][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Начало работы с Центром Интернета вещей]: iot-hub-node-node-getstarted.md



<!--HONumber=Nov16_HO5-->


