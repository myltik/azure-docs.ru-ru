---
title: Обновление встроенного ПО устройства c помощью Центра Интернета вещей Azure (Node) | Документация Майкрософт
description: Запуск обновления встроенного ПО устройства с помощью функции управления устройствами в Центре Интернета вещей. Используйте пакеты SDK для Центра Интернета вещей Azure для Node.js, чтобы реализовать приложение имитации устройства и приложение службы, которое активирует обновление встроенного ПО.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e8ef556793534c9eb8300ee262a4b134960f862a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Используйте управление устройствами, чтобы запустить обновление встроенного ПО устройства (Node/Node).
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

В руководстве по [началу управления устройством][lnk-dm-getstarted] вы узнали, как использовать [двойник устройства][lnk-devtwin] и [прямые методы][lnk-c2dmethod] для удаленной перезагрузки устройства. В этом руководстве используются те же примитивы Центра Интернета вещей, а также содержатся рекомендации по комплексному обновлению имитации встроенного ПО.  Этот шаблон используется в реализации обновления встроенного ПО для примера устройства Intel Edison.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

В этом учебнике описаны следующие процедуры.

* Создание консольного приложения Node.js, вызывающего прямой метод firmwareUpdate в приложении имитации устройства с помощью Центра Интернета вещей.
* Создайте приложение виртуального устройства, которое реализует прямой метод **firmwareUpdate**. Этот метод запускает многоэтапный процесс, который сначала ожидает скачивания образа встроенного ПО, а затем скачивает его и применяет. В ходе выполнения каждого этапа обновления устройство использует сообщаемые свойства для продолжения процесса.

По завершении работы с этим руководством у вас будет два консольных приложения Node.js:

**dmpatterns_fwupdate_service.js**, которое вызывает прямой метод в приложении для имитации устройства, выводит ответ и периодически (каждые 500 миллисекунд) отображает обновленные сообщаемые свойства.

**dmpatterns_fwupdate_device.js**, которое подключается к Центру Интернета вещей с использованием удостоверения устройства, созданного ранее, получает прямой метод firmwareUpdate, запускает процесс с несколькими состояниями для имитации обновления встроенного ПО, состоящий из нескольких этапов (ожидание скачивания образа, скачивание нового образа и применение образа).

Для работы с этим учебником требуется:

* Node.js 4.0.x или более поздней версии. <br/>  В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Node.js для работы с этим учебником в ОС Windows или Linux.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

Следуйте указаниям статьи [Руководство по началу работы с управлением устройствами](iot-hub-node-node-device-management-get-started.md), чтобы создать Центр Интернета вещей и получить строку подключения Центра Интернета вещей.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Активация удаленного обновления встроенного ПО на устройстве с помощью прямого метода
В этом разделе вы создадите консольное приложение Node.js, которое инициирует удаленное обновление встроенного ПО на устройстве. Приложение использует прямой метод для запуска обновления, а также применяет запросы двойников устройства для периодического получения состояния обновления активного встроенного ПО.

1. Создайте пустую папку с именем **triggerfwupdateondevice**.  В папке **triggerfwupdateondevice** создайте файл package.json, используя следующую команду в командной строке.  Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **triggerfwupdateondevice** выполните следующую команду, чтобы установить пакет **azure-iot-hub**.
   
    ```
    npm install azure-iothub --save
    ```
3. В текстовом редакторе создайте файл **dmpatterns_etstarted_service.js** в папке **triggerfwupdateondevice**.
4. Добавьте следующие инструкции require в начале файла **dmpatterns_getstarted_service.js**:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Добавьте следующие объявления переменных и замените значения заполнителей:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Добавьте следующую функцию, чтобы найти и отобразить значение сообщаемого свойства firmwareUpdate.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Добавьте следующую функцию, чтобы вызвать метод firmwareUpdate для перезагрузки целевого устройства:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Наконец, добавьте следующую функцию в код, чтобы запустить последовательность обновления встроенного ПО и периодическое отображение сообщаемых свойств:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Сохраните и закройте файл **dmpatterns_fwupdate_service.js**.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке **manageddevice** выполните следующую команду, чтобы начать прослушивание прямого метода перезагрузки.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. В командной строке в папке **triggerfwupdateondevice** выполните следующую команду, чтобы активировать удаленную перезагрузку и выполнить запрос к двойнику устройства для поиска значения времени последней перезагрузки.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. В консоли отобразится ответ устройства на прямой метод.

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы использовали прямой метод для активации удаленного обновления встроенного ПО на устройстве. Также вы использовали переданные свойства для отслеживания хода обновления встроенного ПО.

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в учебнике [Планирование и трансляция заданий][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
