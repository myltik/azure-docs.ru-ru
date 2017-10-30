---
title: "Имитация устройства с помощью Edge Интернета вещей Azure (Windows) | Документация Майкрософт"
description: "Сведения об использовании Edge Интернета вещей Azure в Windows для создания имитации устройства, отправляющего данные телеметрии через шлюз Интернета вещей Azure в Центр Интернета вещей."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.openlocfilehash: 0aa1836ee1445894022b95fefc2338ef53698240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Отправка сообщений с устройства в облако с помощью имитации устройства (Windows) с использованием Edge Интернета вещей Azure

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Запуск примера

Скрипт **build.cmd** создает выходные данные в папке **build** локальной копии репозитория **iot-edge**. Выходные данные содержат четыре модуля IoT Edge, которые используются в данном примере.

Сценарий сборки создает следующие файлы:

* **logger.dll** в папку **build\\modules\\logger\\Debug**;
* **iothub.dll** в папку **build\\modules\\iothub\\Debug**;
* **identity\_map.dll** в папку **build\\modules\\identitymap\\Debug**;
* **simulated\_device.dll** в папку **build\\modules\\simulated\_device\\Debug**.

Использовать эти пути для значений **пути модуля**, как показано в файле параметров JSON simulated\_device\_cloud\_upload\_win.

Процесс simulated\_device\_cloud\_upload sample принимает путь к JSON-файлу конфигурации в качестве аргумента командной строки. Следующий пример файла JSON предоставляется в репозитории SDK в расположении **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_win.json**. Этот файл конфигурации работает так, как если бы вы не модифицировали сценарий сборки, чтобы разместить модули IoT Edge или образцы исполняемых файлов в местах, отличных от настроек по умолчанию.

> [!NOTE]
> Пути к модулям зависят от каталога, в котором находится файл simulated\_device\_cloud\_upload\_sample.exe. Образец файла конфигурации JSON по умолчанию используется для записи в deviceCloudUploadGatewaylog.log в вашей текущей рабочей папке.

В текстовом редакторе откройте файл **samples\\simulated\_device\_cloud\_upload\\src\\simulated\_device\_cloud\_upload\_win.json** в локальной копии репозитория **iot-edge**. Этот файл настраивает модули Edge Интернета вещей в примере шлюза:

* Модуль **IoTHub** подключается к центру IoT. Его необходимо настроить для отправки данных в центр IoT. В частности, укажите в качестве значения **IoTHubName** имя своего Центра Интернета вещей, а в качестве значения **IoTHubSuffix** — **azure-devices.net**. Для параметра **Transport** задайте одно из следующих значений: **HTTP**, **AMQP** или **MQTT**. В настоящее время только **HTTP** использует одно TCP-подключение для всех сообщений с устройства. Если задать значение **AMQP** или **MQTT**, то шлюз будет поддерживать отдельное TCP-подключение к Центру Интернета вещей для каждого устройства.
* Модуль **mapping** сопоставляет MAC-адреса имитаций устройств с идентификаторами устройств Центра Интернета вещей. Задайте значения **deviceId** для идентификаторов двух устройств, добавленных в Центр Интернета вещей. Задайте значения **deviceKey** для ключей двух устройств.
* Модули **BLE1** и **BLE2** — это имитации устройств. Обратите внимание, что MAC-адреса модулей совпадают с адресами в модуле **mapping**.
* Модуль **Logger** регистрирует активность вашего шлюза в файл.
* Значения **путей модуля** в следующем примере зависят от каталога, в котором находится файл simulated\_device\_cloud\_upload\_sample.exe.
* Массив **links** в нижней части JSON-файла подключает модули **BLE1** и **BLE2** к модулю **mapping**, а модуль **mapping** — к модулю **IoTHub**. Это также гарантирует, что все сообщения будут зарегистрированы модулем **Logger** .

```json
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

Сохраните все изменения, внесенные в файл конфигурации.

Запуск примера

1. В командной строке перейдите в папку **build** в локальной копии репозитория **iot-edge**.
2. Выполните следующую команду:
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. Для мониторинга сообщений, получаемых Центром Интернета вещей из шлюза, можно использовать такие средства, как [обозреватель устройств][lnk-device-explorer] или [iothub-explorer][lnk-iothub-explorer]. Например, используя средство iothub-explorer, вы можете отслеживать сообщения, отправляемые с устройства в облако с помощью следующей команды:

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы подробнее изучить возможности IoT Edge и поэкспериментировать с примерами кода, см. следующие руководства и ресурсы для разработчиков.

* [Отправка сообщений с устройства в облако с помощью физического устройства (Linux) с использованием Edge Интернета вещей Azure][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge] (Edge Интернета вещей Azure).

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]
* [Комплексная защита в Интернете вещей][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
