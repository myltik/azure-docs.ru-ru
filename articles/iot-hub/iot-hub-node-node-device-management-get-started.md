---
title: "Начало работы с управлением устройствами | Документация Майкрософт"
description: "В этом учебнике показано, как начать работу с функцией управления устройствами в Центре Интернета вещей Azure"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 829164eaa856d824ed1f37c43799dabb8f0a0868


---
# <a name="tutorial-get-started-with-device-management"></a>Учебник. Приступая к работе с управлением устройствами
## <a name="introduction"></a>Введение
Облачные приложения Интернета вещей могут использовать в Центре Интернета вещей примитивы, а именно "двойники" устройств и прямые методы, чтобы удаленно запускать и отслеживать на устройствах действия по управлению ими.  В этой статье содержатся рекомендации и код, которые можно использовать для взаимодействия облачного приложения Интернета вещей и устройства с целью запуска и отслеживания процесса удаленной перезагрузки устройства с помощью Центра Интернета вещей.

Чтобы удаленно запустить и отслеживать на устройствах действия по управлению этими устройствами из облачного внутреннего приложения, используйте примитивы Центра Интернета вещей Azure, такие как [двойники устройств][lnk-devtwin] и [прямые методы][lnk-c2dmethod]. В этом учебнике показано, как серверное приложение и устройство взаимодействуют для запуска и отслеживания процесса удаленной перезагрузки устройства из Центра Интернета вещей.

Прямой метод используется для запуска действий по управлению устройствами (таких как перезагрузка, сброс к параметрам по умолчанию и обновление встроенного ПО) из внутреннего приложения в облаке. Устройство отвечает за следующие действия:

* Обработка запроса метода, отправленного из Центра Интернета вещей.
* Инициализация соответствующего действия на определенном устройстве.
* Предоставление сведений об обновлении состояния в Центр Интернета вещей посредством сообщаемых свойств.

Вы можете воспользоваться серверным приложением в облаке, чтобы выполнить запросы двойника устройства для получения сведений о ходе выполнения ваших действий по управлению устройствами.

В этом учебнике описаны следующие процедуры.

* Создание экземпляра Центра Интернета вещей на портале Azure и удостоверения устройства в экземпляре Центра Интернета вещей.
* Создание приложения для имитации устройства с прямым методом, который позволяет выполнить перезагрузку через вызов из облака.
* Создание консольного приложения, вызывающего прямой метод перезагрузки в приложении для имитации устройства с помощью Центра Интернета вещей.

По завершении работы с этим руководством у вас будет два консольных приложения Node.js:

**dmpatterns_getstarted_device.js**, которое подключается к вашему центру Интернета вещей с ранее созданным идентификатором устройства, получает прямой метод перезагрузки, имитирует физическую перезагрузку и сообщает о времени последней перезагрузки.

**dmpatterns_getstarted_service.js**, которое вызывает прямой метод в приложении для имитации устройства, выводит ответ и отображает обновленные сообщаемые свойства.

Для работы с этим учебником требуется:

* Node.js версии 0.12.x или более поздней. <br/>  В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Node.js для работы с этим учебником в ОС Windows или Linux.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы сделаете следующее:

* создадите консольное приложение Node.js, отвечающее на прямой метод, вызываемый из облака;
* запустите перезагрузку имитации устройства;
* используете сообщаемые свойства в запросах двойника устройства для определения устройств и времени последней перезагрузки.

1. Создайте пустую папку с именем **manageddevice**.  В папке **manageddevice** создайте файл package.json, используя следующую команду в командной строке.  Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **manageddevice** выполните следующую команду, чтобы установить пакет SDK для устройства **azure-iot-device** и пакет **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. В текстовом редакторе создайте файл **dmpatterns_getstarted_device.js** в папке **manageddevice**.
4. Добавьте следующие инструкции require в начале файла **dmpatterns_getstarted_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Добавьте переменную **connectionString** , чтобы создать с ее помощью клиент устройства.  Замените connection string строкой подключения своего устройства.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Добавьте следующую функцию, чтобы реализовать прямой метод на устройстве:
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Откройте подключение к Центру Интернета вещей и запустите прослушиватель прямого метода:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Сохраните и закройте файл **dmpatterns_getstarted_device.js**.
   
   [AZURE.NOTE] Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Активация удаленной перезагрузки на устройстве с помощью прямого метода
В этом разделе создается консольное приложение Node.js, которое инициирует удаленную перезагрузку на устройстве с помощью прямого метода и использует запросы двойника устройства для поиска значения времени последней перезагрузки этого устройства.

1. Создайте пустую папку с именем **triggerrebootondevice**.  В папке **triggerrebootondevice** создайте файл package.json, используя следующую команду в командной строке.  Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **triggerrebootondevice** выполните следующую команду, чтобы установить пакет SDK для устройства **azure-iothub** и пакет **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iothub --save
    ```
3. В текстовом редакторе создайте файл **dmpatterns_getstarted_service.js** в папке **triggerrebootondevice**.
4. Добавьте следующие инструкции require в начале файла **dmpatterns_getstarted_service.js**:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Добавьте следующие объявления переменных и замените значения заполнителей:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Добавьте следующую функцию, чтобы вызвать метод устройства для перезагрузки целевого устройства:
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```
7. Добавьте следующую функцию, чтобы запросить устройство и получить сведения о времени последней перезагрузки:
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
8. Добавьте следующий код для вызова функций, которые активируют прямой метод перезагрузки и запросят сведения о времени последней перезагрузки:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Сохраните и закройте файл **dmpatterns_getstarted_service.js**.

## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке **manageddevice** выполните следующую команду, чтобы начать прослушивание прямого метода перезагрузки.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. В командной строке в папке **triggerrebootondevice** выполните следующую команду, чтобы активировать удаленную перезагрузку и выполнить запрос к двойнику устройства для поиска значения времени последней перезагрузки.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. В консоли отобразится ответ устройства на прямой метод.

## <a name="customize-and-extend-the-device-management-actions"></a>Настройка и расширенные возможности действий по управлению устройствами
Решения Интернета вещей позволяют расширить определенный набор шаблонов управления устройствами или добавить пользовательские шаблоны. Для этого используются примитивы — двойники устройств и прямые методы. Примеры других действий по управлению устройствами: сброс к параметрам по умолчанию, обновление встроенного ПО, обновление программного обеспечения, управление питанием, управление сетями и подключениями и шифрование данных.

## <a name="device-maintenance-windows"></a>Периоды обслуживания устройств
Как правило устройства настраиваются на выполнение действий по очереди, что позволяет свести к минимуму прерывания в работе и время простоя.  Периоды обслуживания устройств — это распространенный шаблон для определения времени, когда выполняется обновление конфигурации устройства. Ваши серверные решения могут использовать необходимые свойства двойника устройства для определения и активации на устройстве политики, которая задает период обслуживания. Когда устройство получает политику периода обслуживания, оно может использовать сообщаемые свойства двойника устройства для предоставления сведений о состоянии политики. Затем серверное приложение может использовать запросы двойника устройства для подтверждения соответствия устройств и каждой политики.

## <a name="next-steps"></a>Дальнейшие действия
В данном учебнике мы использовали прямой метод для активации удаленной перезагрузки устройства, использовали сообщаемые свойства для передачи с устройства сведений о последней перезагрузке, а также выполняли запрос к двойнику устройства для поиска значения времени последней перезагрузки устройства из облака.

Чтобы продолжить знакомство с Центром Интернета вещей и шаблонами управления устройствами, такими как удаленное обновление встроенного ПО, см. следующие материалы:

[Учебник. Обновление встроенного ПО][lnk-fwupdate]

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в учебнике [Планирование и трансляция заданий][lnk-tutorial-jobs].

Чтобы продолжить знакомство с Центром Интернета вещей, см. статью [Приступая к работе с пакетом SDK для шлюза Центра Интернета вещей Azure (Linux)][lnk-gateway-SDK].

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Nov16_HO5-->


