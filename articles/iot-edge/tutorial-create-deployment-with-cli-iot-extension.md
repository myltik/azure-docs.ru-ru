---
title: "Развертывание модулей на устройства IoT Edge с помощью расширения Интернета вещей для Azure CLI 2.0 | Документация Майкрософт"
description: "Развертывание модулей на устройства IoT Edge с помощью расширения IoT для Azure CLI 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Развертывание модулей на устройства IoT Edge с помощью расширения IoT для Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Интерфейс Azure CLI 2.0 доступен для операционных систем Windows, Linux и MacOS.

Azure CLI 2.0 позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей поставляет в Azure CLI 2.0 компоненты для управления устройствами и всеми возможностями IoT Edge.

В этом руководстве вы выполните действия для настройки Azure CLI 2.0 и расширения Интернета вещей. Затем вы узнаете, как развертывать модули на устройствах IoT Edge с помощью доступных команд интерфейса командной строки.

## <a name="installation"></a>Установка 

### <a name="step-1---install-python"></a>Шаг 1. Установка Python

Необходимы [Python 2.7x или Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-azure-cli-20"></a>Шаг 2. Установка Azure CLI 2.0

Выполните [инструкции по установке](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), чтобы настроить Azure CLI 2.0 в своей среде. Вам понадобится как минимум Azure CLI 2.0 версии 2.0.24 или более поздней. Для проверки используйте `az –version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. Простой способ установки в Windows — скачать и установить [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Шаг 3. Установка расширения Интернета вещей

В [файле сведений расширения Интернета вещей](https://github.com/Azure/azure-iot-cli-extension) описывается несколько способов установки расширения. Проще всего запустить `az extension add --name azure-cli-iot-ext`. После установки можно использовать `az extension list`, чтобы проверить установленные расширения, или `az extension show --name azure-cli-iot-ext` для просмотра сведений о расширении Интернета вещей. Чтобы удалить расширение, можно использовать `az extension remove --name azure-cli-iot-ext`.


## <a name="deploy-modules-to-an-iot-edge-device"></a>Развертывание модулей на устройстве IoT Edge
В этом руководстве вы узнаете, как создать развертывание IoT Edge. В примере показано, как войти в учетную запись Azure, создать группу ресурсов Azure (контейнер, содержащий связанные ресурсы для решения Azure), Центр Интернета вещей, три удостоверения устройства IoT Edge, настроить теги, а затем создать развертывание IoT Edge, предназначенное для этих устройств. Перед началом работы выполните описанные выше шаги по установке. Если у вас нет учетной записи Azure, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-login-to-the-azure-account"></a>1. Войдите в учетную запись Azure.
  
    az login

![вход][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Создайте группу ресурсов IoTHubBlogDemo в регионе eastus

    az group create -l eastus -n IoTHubBlogDemo

![Создать группу ресурсов][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3. Создайте Центр Интернета вещей blogDemoHub в созданной группе ресурсов.

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Создание Центра Интернета вещей][3]


### <a name="4-create-an-iot-edge-device"></a>4. Создайте устройство IoT Edge.

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![Создание устройства IoT Edge][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5. Примените конфигурацию к устройству IoT Edge.

Сохраните шаблон развертывания JSON локально в качестве TXT-файла. Вам необходимо будет указать путь к файлу при запуске команды apply-configuration.

Ниже приведен пример шаблона развертывания JSON, который содержит один модуль tempSensor.

```json
{
  "moduleContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "tempSensor": {
      "properties.desired": {}
    }
  }
}
```

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![Применение конфигурации][5]

### <a name="6-list-modules"></a>6. Выведите список модулей.
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![Вывод списка модулей][6]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Чтобы продолжить изучение функций Azure IoT Edge, перейдите к статье об использовании устройства IoT Edge в качестве шлюза. 

> [!div class="nextstepaction"]
> [Create an IoT Edge gateway device](how-to-create-transparent-gateway.md) (Создание устройства шлюза IoT Edge для обработки данных с других устройств Интернета вещей (предварительная версия))

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

