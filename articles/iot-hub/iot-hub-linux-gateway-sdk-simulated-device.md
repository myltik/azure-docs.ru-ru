---
title: "Имитация устройства с помощью пакета SDK для шлюза Azure IoT (Linux) | Документация Майкрософт"
description: "Сведения об использовании пакета SDK для шлюза Azure IoT в Linux для создания имитации устройства, отправляющего данные телеметрии через шлюз в Центр Интернета вещей."
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
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e2e814559282de3e5409e3215d824e1309debe5a


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Воспользуйтесь пакетом SDK для шлюза Azure IoT для отправка сообщений с устройства в облако с помощью имитации устройства (Linux).
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера
Перед началом работы необходимо выполнить следующие действия:

* [Настройте среду разработки][lnk-setupdevbox] для работы с пакетом SDK для Linux.
* [Создайте Центр Интернета вещей][lnk-create-hub] в своей подписке Azure (для выполнения указаний данного пошагового руководства необходимо имя центра). Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* Добавьте в центр IoT два устройства и запишите их идентификаторы и ключи устройств. Чтобы добавить устройства в Центр Интернета вещей, созданный на предыдущем шаге, и получить их ключи, можно использовать такие инструменты, как [обозреватель устройств или iothub-explorer][lnk-explorer-tools].

Сборка примера

1. Откройте оболочку.
2. Перейдите в корневую папку в локальной копии репозитория **azure-iot-gateway-sdk** .
3. Запустите скрипт **tools/build.sh--skip unittests**. В этом сценарии используется служебная программа **cmake**, которая создает папку **build** в корневой папке локальной копии репозитория **azure-iot-gateway-sdk** и генерирует файл makefile. Затем скрипт создает решение, пропуская модульные тесты. Удалите параметр **--skip-unittests**, если требуется создавать и выполнять модульные тесты. 

> [!NOTE]
> При каждом запуске сценарий **build.sh** удаляет и заново создает папку **build** в корневой папке локальной копии репозитория **azure-iot-gateway-sdk**.
> 
> 

Запуск примера

В текстовом редакторе откройте файл **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** в локальной копии репозитория **azure-iot-gateway-sdk**. Этот файл настраивает модули в примере шлюза:

* Модуль **IoTHub** подключается к центру IoT. Его необходимо настроить для отправки данных в центр IoT. В частности, укажите в качестве значения **IoTHubName** имя своего Центра Интернета вещей, а в качестве значения **IoTHubSuffix** — **azure-devices.net**. Для параметра **Transport** задайте одно из следующих значений: HTTP, AMQP или MQTT. Обратите внимание, что в настоящее время только HTTP использует одно TCP-подключение для всех сообщений с устройства. Если задать значение AMQP или MQTT, то шлюз будет поддерживать отдельное TCP-подключение к Центру Интернета вещей для каждого устройства.
* Модуль **mapping** сопоставляет MAC-адреса имитаций устройств с идентификаторами устройств Центра Интернета вещей. Убедитесь в том, что значения **deviceId** совпадают с идентификаторами двух устройств, добавленных в Центр Интернета вещей, а значения **deviceKey** содержат ключи этих двух устройств.
* Модули **BLE1** и **BLE2** — это имитации устройств. Обратите внимание на то, что их MAC-адреса совпадают с адресами в модуле **mapping** .
* Модуль **Logger** регистрирует активность вашего шлюза в файл.
* Представленные ниже значения **module path** предполагают, что пример запускается из корневой папки локальной копии репозитория **azure-iot-gateway-sdk**.
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

1. Перейдите к папке **azure-iot-gateway-sdk/build** в оболочке.
2. Выполните следующую команду:
   
    ```
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. Для мониторинга сообщений, получаемых Центром Интернета вещей из шлюза, можно использовать такие инструменты, как [обозреватель устройств или iothub-explorer][lnk-explorer-tools].

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите подробнее изучить возможности пакета SDK для шлюза Интернета вещей и поэкспериментировать с примерами кода, то см. следующие учебники и ресурсы для разработчиков:

* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через реальное устройство с ОС Linux][lnk-physical-device]
* [Пакет SDK для шлюза Azure IoT][lnk-gateway-sdk]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]
* [Комплексная защита в Интернете вещей][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md



<!--HONumber=Dec16_HO2-->


