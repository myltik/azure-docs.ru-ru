## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе выполняются следующие действия:

* создадите консольное приложение Node.js, отвечающее на прямой метод, вызываемый из облака;
* активируете обновление имитации встроенного ПО;
* используете сообщаемые свойства в запросах двойника устройства для определения устройств и времени последнего обновления встроенного ПО.

Шаг 1. Создайте пустую папку с именем **manageddevice**.  В папке **manageddevice** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```

Шаг 2. В командной строке в папке **manageddevice** выполните следующую команду, чтобы установить пакет SDK для устройств **azure-iot-device** и пакет **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

Шаг 3. В текстовом редакторе создайте файл **dmpatterns_fwupdate_device.js** в папке **manageddevice**.

Шаг 4. Добавьте следующие инструкции require в начале файла **dmpatterns_fwupdate_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
Шаг 5. Добавьте переменную **connectionString**, чтобы создать с ее помощью экземпляр **клиента**. Замените заполнитель `{yourdeviceconnectionstring}` строкой подключения, которую вы записали ранее в разделе "Создание удостоверения устройства":
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

Шаг 6. Добавьте следующую функцию, которая будет использоваться для обновления сообщаемых свойств:
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```

Шаг 7. Добавьте следующие функции, которые имитируют скачивание и применение образа встроенного ПО:
   
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

Шаг 8. Добавьте следующую функцию, которая изменяет состояние обновления встроенного ПО с помощью сообщаемых свойств на **Ожидание**. Как правило, устройства информируются о доступном обновлении, а политика, определяемая администратором, активирует скачивание и применения обновления на устройстве. В этой функции применяется логика включения политики. Для простоты пример ожидает в течение четырех секунд, а потом приступает к скачиванию образа встроенного ПО:
   
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

Шаг 9. Добавьте следующую функцию, которая изменяет состояние обновления встроенного ПО с помощью сообщаемых свойств на **Скачивание**. Затем функция имитирует скачивание встроенного ПО и наконец обновляет состояние обновления встроенного ПО на **downloadFailed** или **downloadComplete**:
   
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

Шаг 10. Добавьте следующую функцию, которая изменяет состояние обновления встроенного ПО с помощью сообщаемых свойств на **Применение**. Затем функция имитирует применение встроенного ПО и наконец обновляет состояние обновления встроенного ПО на **applyFailed** или **applyComplete**:
    
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

Шаг 11. Добавьте следующую функцию, которая обрабатывает метод **firmwareUpdate** и инициирует многоэтапное обновление встроенного ПО:
    
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
      var fwPackageUri = request.payload.fwPackageUri;
    
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

Шаг 12. Наконец, добавьте следующий код, который позволяет подключиться к Центру Интернета вещей:
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя](https://msdn.microsoft.com/library/hh675232.aspx) на сайте MSDN.
> 
> 