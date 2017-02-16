---
title: "Обновление встроенного ПО | Документация Майкрософт"
description: "В этом руководстве описана процедура обновления встроенного ПО"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 632b9b38808e033b1fee2676b353f2649c4a282c


---
# <a name="tutorial-how-to-do-a-firmware-update"></a>Учебник. Обновление встроенного ПО
## <a name="introduction"></a>Введение
В статье [Приступая к работе с функцией управления устройствами центра IoT Azure с использованием C# (предварительная версия)][lnk-dm-getstarted] вы узнали, как использовать [двойник устройства][lnk-devtwin] и [прямые методы][lnk-c2dmethod] для удаленной перезагрузки устройства. В этом руководстве используются те же примитивы Центра Интернета вещей, а также содержатся рекомендации по комплексному обновлению имитации встроенного ПО.  Этот шаблон используется в реализации обновления встроенного ПО для примера устройства Intel Edison.

В этом учебнике описаны следующие процедуры.

* Создание консольного приложения, вызывающего прямой метод firmwareUpdate в приложении для имитации устройства с помощью Центра Интернета вещей.
* Создание приложения для имитации устройства, реализующей прямой метод firmwareUpdate, который проходит через многоэтапный процесс: ожидание скачивания образа встроенного ПО, скачивание образа встроенного ПО и, наконец, применение образа встроенного ПО.  В ходе выполнения каждого этапа устройство использует сообщаемые свойства для продолжения процесса.

По завершении работы с этим руководством у вас будет два консольных приложения Node.js:

**dmpatterns_fwupdate_service.js**, которое вызывает прямой метод в приложении для имитации устройства, выводит ответ и периодически (каждые 500 миллисекунд) отображает обновленные сообщаемые свойства.

**dmpatterns_fwupdate_device.js**, которое подключается к Центру Интернета вещей с использованием удостоверения устройства, созданного ранее, получает прямой метод firmwareUpdate, запускает процесс с несколькими состояниями для имитации обновления встроенного ПО, состоящий из нескольких этапов (ожидание скачивания образа, скачивание нового образа и применение образа).

Для работы с этим учебником требуется:

* Node.js версии 0.12.x или более поздней. <br/>  В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Node.js для работы с этим учебником в ОС Windows или Linux.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

Следуйте указаниям статьи [Get started with device management](iot-hub-node-node-device-management-get-started.md) (Приступая к работе с управлением устройствами), чтобы создать Центр Интернета вещей и получить строку подключения.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы сделаете следующее:

* создадите консольное приложение Node.js, отвечающее на прямой метод, вызываемый из облака;
* активируете обновление имитации встроенного ПО;
* используете сообщаемые свойства в запросах двойника устройства для определения устройств и времени последнего обновления встроенного ПО.

1. Создайте пустую папку с именем **manageddevice**.  В папке **manageddevice** создайте файл package.json, используя следующую команду в командной строке.  Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **manageddevice** выполните следующую команду, чтобы установить пакет SDK для устройства **azure-iot-device** и пакет **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. В текстовом редакторе создайте файл **dmpatterns_fwupdate_device.js** в папке **manageddevice**.
4. Добавьте следующие инструкции require в начале файла **dmpatterns_fwupdate_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Добавьте переменную **connectionString** , чтобы создать с ее помощью клиент устройства.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Добавьте следующую функцию, которая будет использоваться для обновления сообщаемых свойств:
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
7. Добавьте следующие функции, которые имитируют скачивание и применение образа встроенного ПО:
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. Добавьте следующую функцию, которая изменяет состояние обновления встроенного ПО с помощью сообщаемых свойств на "Ожидание скачивания".  Как правило, устройства информируются о доступном обновлении, а политика, определяемая администратором, активирует скачивание и применения обновления на устройстве.  Именно на этом этапе применяется логика включения политики.  Для простоты мы ждем 4 секунды и переходим к скачиванию образа встроенного ПО. 
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. Добавьте следующую функцию, которая изменяет состояние обновления встроенного ПО с помощью сообщаемых свойств на "Скачивание образа встроенного ПО".  Затем имитируется скачивание встроенного ПО и, наконец, изменяется состояние обновления встроенного ПО, чтобы сообщить об успешном скачивании или сбое.
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. Добавьте следующую функцию, которая изменяет состояние обновления встроенного ПО с помощью сообщаемых свойств на "Применение образа встроенного ПО".  Затем имитируется применение образа встроенного ПО и, наконец, изменяется состояние обновления встроенного ПО, чтобы сообщить об успешном применении или сбое.
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. Добавьте следующую функцию, которая обрабатывает метод firmwareUpdate и инициирует многоэтапное обновление встроенного ПО.
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. Наконец, добавьте следующий код, который позволяет подключиться к Центру Интернета вещей в качестве устройства. 
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.
> 
> 

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Активация удаленного обновления встроенного ПО на устройстве с помощью прямого метода
В этом разделе вы создадите консольное приложение Node.js, которое инициирует удаленное обновление встроенного ПО на устройстве с помощью прямого метода и использует запросы двойников устройств для периодического получения сведений о состоянии обновления активного встроенного ПО на этом устройстве.

1. Создайте пустую папку с именем **triggerfwupdateondevice**.  В папке **triggerfwupdateondevice** создайте файл package.json, используя следующую команду в командной строке.  Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **triggerfwupdateondevice** выполните следующую команду, чтобы установить пакет SDK для устройства **azure-iothub** и пакет **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-hub --save
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

## <a name="next-steps"></a>Дальнейшие действия
В этом учебники вы использовали прямой метод для активации удаленного обновления встроенного ПО на устройстве и периодически использовали сообщаемые свойства для отслеживания хода обновления встроенного ПО.  

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в учебнике [Планирование и трансляция заданий][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Nov16_HO5-->


