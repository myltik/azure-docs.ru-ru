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
ms.date: 03/29/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 0ea8483e19ddec447642a33c24c9ecdd9937ea11
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Отправка сообщений с устройства в облако с помощью имитации устройства (Windows) с использованием Edge Интернета вещей Azure
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера
Перед началом работы необходимо выполнить следующие действия:

* [Настроить среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Windows.
* [Создайте Центр Интернета вещей][lnk-create-hub] в подписке Azure (для выполнения указаний данного пошагового руководства необходимо имя центра). Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* Добавьте в центр IoT два устройства и запишите их идентификаторы и ключи устройств. Для добавления устройств в Центр Интернета вещей, созданный на предыдущем шаге, и получения их ключей, можно использовать такие средства, как [обозреватель устройств][lnk-device-explorer] или [iothub-explorer][lnk-iothub-explorer].

Сборка примера

1. Откройте командную строку разработчика для **VS 2015** или **VS 2017**.
2. Перейдите в корневую папку в локальной копии репозитория **iot-edge**.
3. Запустите сценарий **tools\\build.cmd**. Этот сценарий создает файл решения Visual Studio выполняет сборку решения. Решение Visual Studio находится в папке **build** локальной копии репозитория **iot-edge**. В сценарии можно задать дополнительные параметры для выполнения сборки и модульного или сквозного тестирования. Это параметры **--run-unittests** и **--run-e2e-tests** соответственно.

Запуск примера

В текстовом редакторе откройте файл **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** в локальной копии репозитория **iot-edge**. Этот файл настраивает модули Edge Интернета вещей в примере шлюза:

* Модуль **IoTHub** подключается к центру IoT. Его необходимо настроить для отправки данных в центр IoT. В частности, укажите в качестве значения **IoTHubName** имя своего Центра Интернета вещей, а в качестве значения **IoTHubSuffix** — **azure-devices.net**. Для параметра **Transport** задайте одно из следующих значений: HTTP, AMQP или MQTT. В настоящее время только HTTP использует одно TCP-подключение для всех сообщений с устройства. Если задать значение AMQP или MQTT, то шлюз будет поддерживать отдельное TCP-подключение к Центру Интернета вещей для каждого устройства.
* Модуль **mapping** сопоставляет MAC-адреса имитаций устройств с идентификаторами устройств Центра Интернета вещей. Убедитесь в том, что значения **deviceId** совпадают с идентификаторами двух устройств, добавленных в Центр Интернета вещей, а значения **deviceKey** содержат ключи этих двух устройств.
* Модули **BLE1** и **BLE2** — это имитации устройств. Обратите внимание, что MAC-адреса модулей совпадают с адресами в модуле **mapping**.
* Модуль **Logger** регистрирует активность вашего шлюза в файл.
* Указанные в следующем примере значения **module path** предполагают, что вы клонировали репозиторий Edge Интернета вещей в корневую папку диска **C:**. Если вы загрузили его в другое место, измените соответственно значения **module path** .
* Массив **links** в нижней части JSON-файла подключает модули **BLE1** и **BLE2** к модулю **mapping**, а модуль **mapping** — к модулю **IoTHub**. Это также гарантирует, что все сообщения будут зарегистрированы модулем **Logger** .

```
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
            "macAddress": "01:01:01:01:01:01"
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
            "macAddress": "02:02:02:02:02:02"
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

1. В командной строке перейдите в корневую папку в локальной копии репозитория **iot-edge**.
2. Выполните следующую команду:
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. Для мониторинга сообщений, получаемых Центром Интернета вещей из шлюза, можно использовать такие средства, как [обозреватель устройств][lnk-device-explorer] или [iothub-explorer][lnk-iothub-explorer].

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите подробнее изучить возможности Edge Интернета вещей и поэкспериментировать с примерами кода, см. следующие руководства и ресурсы для разработчиков.

* [Отправка сообщений с устройства в облако с помощью физического устройства (Linux) с использованием Edge Интернета вещей Azure][lnk-physical-device]
* [Azure IoT Edge][lnk-gateway-sdk] (Edge Интернета вещей Azure).

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]
* [Все аспекты безопасности решения IoT][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-gateway-sdk]: https://github.com/Azure/iot-edge/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md

