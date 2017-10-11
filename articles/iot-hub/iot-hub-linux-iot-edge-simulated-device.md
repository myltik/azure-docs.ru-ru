---
title: "Имитация устройства с помощью Edge Интернета вещей Azure (Linux) | Документация Майкрософт"
description: "Сведения об использовании Edge Интернета вещей Azure в Linux для создания имитации устройства, отправляющего данные телеметрии через шлюз Интернета вещей в Центр Интернета вещей."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: andbuc
ms.openlocfilehash: 5349960373ae6815862c5f79a69dd6d5d9d624ab
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Отправка сообщений с устройства в облако с помощью имитации устройства (Linux) с использованием Edge Интернета вещей Azure

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>Запуск примера

Скрипт **build.sh** создает выходные данные в папке **build** локальной копии репозитория **iot-edge**. Выходные данные содержат четыре модуля IoT Edge, которые используются в данном примере.

Сценарий сборки помещает:

* **liblogger.so** в папку **build/modules/logger**;
* **libiothub.so** в папку **build/modules/iothub**;
* **lib\_identity\_map.so** в папку **build/modules/identitymap**;
* **libsimulated\_device.so** в папку **build/modules/simulated\_device**.

Используйте эти пути для настройки значений **module path**, как указано в приведенном ниже файле параметров JSON.

Процесс simulated\_device\_cloud\_upload\_sample принимает путь к JSON-файлу конфигурации в качестве аргумента командной строки. Следующий пример файла JSON предоставляется в репозитории SDK в расположении **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_sample\_lin.json**. Этот файл конфигурации работает так, как если бы вы не модифицировали сценарий сборки, чтобы разместить модули IoT Edge или образцы исполняемых файлов в местах, отличных от настроек по умолчанию.

> [!NOTE]
> Пути к модулям зависят от каталога, в котором находится исполняемый файл simulated\_device\_cloud\_upload\_sample, а не от каталога, где находится исполняемый файл. Образец файла конфигурации JSON по умолчанию используется для записи в deviceCloudUploadGatewaylog.log в вашей текущей рабочей папке.

В текстовом редакторе откройте файл **samples/simulated\_device\_cloud\_upload\_sample/src/simulated\_device\_cloud\_upload\_lin.json** в локальной копии репозитория **iot-edge**. Этот файл настраивает модули Edge Интернета вещей в примере шлюза:

* Модуль **IoTHub** подключается к центру IoT. Его необходимо настроить для отправки данных в центр IoT. В частности, укажите в качестве значения **IoTHubName** имя своего Центра Интернета вещей, а в качестве значения **IoTHubSuffix** — **azure-devices.net**. Для параметра **Transport** задайте одно из следующих значений: **HTTP**, **AMQP** или **MQTT**. В настоящее время только **HTTP** использует одно TCP-подключение для всех сообщений с устройства. Если задать значение **AMQP** или **MQTT**, то шлюз будет поддерживать отдельное TCP-подключение к Центру Интернета вещей для каждого устройства.
* Модуль **mapping** сопоставляет MAC-адреса имитаций устройств с идентификаторами устройств Центра Интернета вещей. Убедитесь в том, что значения **deviceId** совпадают с идентификаторами двух устройств, добавленных в Центр Интернета вещей, а значения **deviceKey** содержат ключи этих двух устройств.
* Модули **BLE1** и **BLE2** — это имитации устройств. Обратите внимание, что MAC-адреса совпадают с адресами в модуле **mapping**.
* Модуль **Logger** регистрирует активность вашего шлюза в файл.
* В значениях **module path** в примере предполагается, что образец запускается из папки **build** в локальной копии репозитория **iot-edge**.
* Массив **links** в нижней части JSON-файла подключает модули **BLE1** и **BLE2** к модулю **mapping**, а модуль **mapping** — к модулю **IoTHub**. Это также гарантирует, что все сообщения будут зарегистрированы модулем **Logger** .

```json
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

Сохраните все изменения, внесенные в файл конфигурации.

Запуск примера

1. Перейдите к папке **iot-edge/build** в оболочке.
2. Выполните следующую команду:
   
    ```sh
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. Для мониторинга сообщений, получаемых Центром Интернета вещей из шлюза, можно использовать такие средства, как [обозреватель устройств][lnk-device-explorer] или [iothub-explorer][lnk-iothub-explorer]. Например, используя средство iothub-explorer, вы можете отслеживать сообщения, отправляемые с устройства в облако с помощью следующей команды:

    ```sh
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы подробнее изучить возможности Azure IoT Edge и поэкспериментировать с примерами кода, см. следующие руководства и ресурсы для разработчиков.

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
