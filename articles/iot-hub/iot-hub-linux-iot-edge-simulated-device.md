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
ms.date: 05/18/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: a20b563561676bfebce89393fe28b86747b8ebc5
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Отправка сообщений с устройства в облако с помощью имитации устройства (Linux) с использованием Edge Интернета вещей Azure
[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера
Перед началом работы необходимо выполнить следующие действия:

* [Настройте среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Linux.
* [Создайте Центр Интернета вещей][lnk-create-hub] в своей подписке Azure (для выполнения указаний данного пошагового руководства необходимо имя центра). Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* Добавьте в центр IoT два устройства и запишите их идентификаторы и ключи устройств. Для добавления устройств в Центр Интернета вещей, созданный на предыдущем шаге, и получения их ключей, можно использовать такие средства, как [обозреватель устройств][lnk-device-explorer] или [iothub-explorer][lnk-iothub-explorer].

Сборка примера

1. Откройте оболочку.
2. Перейдите в корневую папку в локальной копии репозитория **iot-edge**.
3. Запустите сценарий **tools/build.sh** . В этом сценарии используется служебная программа **cmake**, которая создает папку **build** в корневой папке локальной копии репозитория **iot-edge** и генерирует файл makefile. Затем скрипт создает решение, пропуская модульные и сквозные тесты. Добавьте параметр **--run-unittests**, если требуется создавать и выполнять модульные тесты. Добавьте параметр **--run-e2e-tests**, если требуется создавать и выполнять сквозные тесты. 

> [!NOTE]
> При каждом запуске сценарий **build.sh** удаляет и заново создает папку **build** в корневой папке локальной копии репозитория **iot-edge**.
> 
> 

Запуск примера

В текстовом редакторе откройте файл **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** в локальной копии репозитория **iot-edge**. Этот файл настраивает модули Edge Интернета вещей в примере шлюза:

* Модуль **IoTHub** подключается к центру IoT. Его необходимо настроить для отправки данных в центр IoT. В частности, укажите в качестве значения **IoTHubName** имя своего Центра Интернета вещей, а в качестве значения **IoTHubSuffix** — **azure-devices.net**. Для параметра **Transport** задайте одно из следующих значений: HTTP, AMQP или MQTT. Обратите внимание, что в настоящее время только HTTP использует одно TCP-подключение для всех сообщений с устройства. Если задать значение AMQP или MQTT, то шлюз будет поддерживать отдельное TCP-подключение к Центру Интернета вещей для каждого устройства.
* Модуль **mapping** сопоставляет MAC-адреса имитаций устройств с идентификаторами устройств Центра Интернета вещей. Убедитесь в том, что значения **deviceId** совпадают с идентификаторами двух устройств, добавленных в Центр Интернета вещей, а значения **deviceKey** содержат ключи этих двух устройств.
* Модули **BLE1** и **BLE2** — это имитации устройств. Обратите внимание на то, что их MAC-адреса совпадают с адресами в модуле **mapping** .
* Модуль **Logger** регистрирует активность вашего шлюза в файл.
* Представленные ниже значения **module path** предполагают, что пример запускается из корневой папки локальной копии репозитория **iot-edge**.
* Массив **links** в нижней части JSON-файла подключает модули **BLE1** и **BLE2** к модулю **mapping**, а модуль **mapping** — к модулю **IoTHub**. Это также гарантирует, что все сообщения будут зарегистрированы модулем **Logger** .

```
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
   
    ```
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. Для мониторинга сообщений, получаемых Центром Интернета вещей из шлюза Edge Интернета вещей, можно использовать такие инструменты, как [Device Explorer][lnk-device-explorer] или [iothub-explorer][lnk-iothub-explorer].

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите подробнее изучить возможности Edge Интернета вещей Azure и поэкспериментировать с примерами кода, см. следующие руководства и ресурсы для разработчиков.

* [Отправка сообщений с устройства в облако с помощью физического устройства (Linux) с использованием Edge Интернета вещей Azure][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge] (Edge Интернета вещей Azure).

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]
* [Комплексная защита в Интернете вещей][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-iot-edge]: https://github.com/Azure/iot-edge/

[lnk-physical-device]: iot-hub-iot-edge-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md

