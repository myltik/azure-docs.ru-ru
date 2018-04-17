---
title: Подготовка устройства к удаленному мониторингу с помощью Node.js в Azure | Документация Майкрософт
description: Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения, написанного на Node.js.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: df89150867a3c95116ba8ca8cd684af4b32a36de
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

В этом руководстве показано, как подключить физическое устройство к предварительно настроенному решению для удаленного мониторинга. В этом руководстве используется Node.js, поэтому оно подходит для сред с минимальными ограничениями ресурсов.

## <a name="create-a-nodejs-solution"></a>Создание решения Node.js

Убедитесь, что на компьютере разработки установлен компонент [Node.js](https://nodejs.org/) версии 4.0.0 или более поздней. Можно запустить `node --version` в командной строке, чтобы проверить версию.

1. Создайте папку с именем `remotemonitoring` на компьютере для разработки. Перейдите к этой папке в среде командной строки.

1. Чтобы скачать и установить пакеты, необходимые для выполнения примера приложения, выполните следующие команды:

    ```cmd/sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. В папке `remotemonitoring` создайте файл с именем **remote_monitoring.js**. Откройте этот файл в текстовом редакторе.

1. Откройте файл **remote-monitoring.js** и добавьте следующие инструкции `require`:

    ```nodejs
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. После операторов `require` добавьте указанные ниже объявления переменных. Замените значение заполнителя `{device connection string}` ранее записанным значением для своего устройства, подготовленного в решении для удаленного мониторинга:

    ```nodejs
    var connectionString = '{device connection string}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Чтобы определить базовые данные телеметрии, добавьте следующие переменные:

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Чтобы определить некоторые значения свойств, добавьте следующие переменные:

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Добавьте следующую переменную, чтобы определить, какие передаваемые свойства должны отправляться в решение. Эти свойства содержат метаданные для описания методов и данных телеметрии, используемых устройством:

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Добавьте следующую вспомогательную функцию для вывода результатов операции:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Добавьте следующую вспомогательную функцию для создания случайных значений телеметрии:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Добавьте приведенную ниже общую функцию для обработки вызовов прямого метода из решения. Функция отвечает за отображение сведений о вызванном прямом методе. В этом примере ее использование не приводит к каким-либо изменениям устройства. Решение использует прямые методы для выполнения действий на устройствах:

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (err) console.error('Error sending method response :\n' + err.toString());
        else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
      });
    }
    ```

1. Добавьте приведенную ниже функцию для обработки вызовов прямого метода **FirmwareUpdate** из решения. Функция отвечает за проверку параметров, переданных в полезные данные прямого метода, и асинхронный запуск моделирования для обновления встроенного ПО:

    ```node.js
    function onFirmwareUpdate(request, response) {
      // Get the requested firmware version from the JSON request body
      var firmwareVersion = request.payload.Firmware;
      var firmwareUri = request.payload.FirmwareUri;
      
      // Ensure we got a firmware values
      if (!firmwareVersion || !firmwareUri) {
        response.send(400, 'Missing firmware value', function(err) {
          if (err) console.error('Error sending method response :\n' + err.toString());
          else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
        });
      } else {
        // Respond the cloud app for the device method
        response.send(200, 'Firmware update started.', function(err) {
          if (err) console.error('Error sending method response :\n' + err.toString());
          else {
            console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

            // Run the simulated firmware update flow
            runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
          }
        });
      }
    }
    ```

1. Добавьте следующую функцию для моделирования длительного потока обновления встроенного ПО с передачей в решение сведений о ходе выполнения:

    ```node.js
    // Simulated firmwareUpdate flow
    function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
      console.log('Simulating firmware update flow...');
      console.log('> Firmware version passed: ' + firmwareVersion);
      console.log('> Firmware URI passed: ' + firmwareUri);
      async.waterfall([
        function (callback) {
          console.log("Image downloading from " + firmwareUri);
          var patch = {
            FirmwareUpdateStatus: 'Downloading image..'
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(10000, callback);
        },
        function (callback) {
          console.log("Downloaded, applying firmware " + firmwareVersion);
          deviceOnline = false;
          var patch = {
            FirmwareUpdateStatus: 'Applying firmware..',
            Online: false
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(8000, callback);
        },
        function (callback) {
          console.log("Rebooting");
          var patch = {
            FirmwareUpdateStatus: 'Rebooting..'
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(10000, callback);
        },
        function (callback) {
          console.log("Firmware updated to " + firmwareVersion);
          deviceOnline = true;
          var patch = {
            FirmwareUpdateStatus: 'Firmware updated',
            Online: true,
            Firmware: firmwareVersion
          };
          reportUpdateThroughTwin(patch, callback);
          callback(null);
        }
      ], function(err) {
        if (err) {
          console.error('Error in simulated firmware update flow: ' + err.message);
        } else {
          console.log("Completed simulated firmware update flow");
        }
      });

      // Helper function to update the twin reported properties.
      function reportUpdateThroughTwin(patch, callback) {
        console.log("Sending...");
        console.log(JSON.stringify(patch, null, 2));
        client.getTwin(function(err, twin) {
          if (!err) {
            twin.properties.reported.update(patch, function(err) {
              if (err) callback(err);
            });      
          } else {
            if (err) callback(err);
          }
        });
      }

      function sleep(milliseconds, callback) {
        console.log("Simulate a delay (milleseconds): " + milliseconds);
        setTimeout(function () {
          callback(null);
        }, milliseconds);
      }
    }
    ```

1. Добавьте приведенный ниже код для отправки данных телеметрии в решение. Клиентское приложение добавляет свойства в сообщение, чтобы определить его схему:

    ```node.js
    function sendTelemetry(data, schema) {
      if (deviceOnline) {
        var d = new Date();
        var payload = JSON.stringify(data);
        var message = new Message(payload);
        message.properties.add('$$CreationTimeUtc', d.toISOString());
        message.properties.add('$$MessageSchema', schema);
        message.properties.add('$$ContentType', 'JSON');

        console.log('Sending device message data:\n' + payload);
        client.sendEvent(message, printErrorFor('send event'));
      } else {
        console.log('Offline, not sending telemetry');
      }
    }
    ```

1. Добавьте следующий код для создания экземпляра клиента:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Добавьте следующий код, который:

    * открывает подключение;
    * настраивает обработчик для требуемых свойств;
    * отправляет сообщаемые свойства;
    * регистрирует обработчики для прямых методов; В примере используется отдельный обработчик для прямого метода обновления встроенного ПО.
    * начинает отправку данных телеметрии.

    ```nodejs
    client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
    });
    ```

1. Сохраните изменения в файле **remote_monitoring.js**.

1. Чтобы запустить пример приложения, выполните следующую команду в командной строке:

    ```cmd/sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
