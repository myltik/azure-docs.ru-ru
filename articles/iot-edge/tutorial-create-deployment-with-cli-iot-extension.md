---
title: Развертывание модулей на устройства IoT Edge с помощью расширения Интернета вещей для Azure CLI 2.0 | Документация Майкрософт
description: Развертывание модулей на устройства IoT Edge с помощью расширения IoT для Azure CLI 2.0
services: iot-edge
keywords: ''
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 03/02/2018
ms.topic: article
ms.service: iot-edge
ms.custom: ''
ms.reviewer: kgremban
ms.openlocfilehash: 7bc0d0706385f2f3e101d06be3a2837341c331b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Развертывание модулей на устройства IoT Edge с помощью расширения IoT для Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Интерфейс Azure CLI 2.0 доступен для операционных систем Windows, Linux и MacOS.

Azure CLI 2.0 позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей поставляет в Azure CLI 2.0 компоненты для управления устройствами и всеми возможностями IoT Edge.

С помощью этого руководства вы настроите Azure CLI 2.0 и расширение Интернета вещей. Затем вы узнаете, как развертывать модули на устройствах IoT Edge с помощью доступных команд интерфейса командной строки.

## <a name="prerequisites"></a>предварительным требованиям

* Учетная запись Azure. Если ее нет, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?v=17.39a). 

* [Python 2.7x или Python 3.x](https://www.python.org/downloads/).

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) в среде. Вам понадобится как минимум Azure CLI 2.0 версии 2.0.24 или более поздней. Для проверки используйте `az –-version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. Простой способ установки в Windows — скачать и установить [MSI](https://aka.ms/InstallAzureCliWindows).

* [Расширение Интернета вещей для Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension):
   1. Запустите `az extension add --name azure-cli-iot-ext`. 
   2. После установки с помощью командлета `az extension list` можно проверить установленные расширения, а с помощью командлета `az extension show --name azure-cli-iot-ext` — просмотреть сведения о расширении Интернета вещей.
   3. Чтобы удалить расширение, используйте `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Создайте устройство IoT Edge.
В этой статье приведены инструкции по созданию развертывания IoT Edge. В примере показано, как войти в учетную запись Azure, создать группу ресурсов Azure (контейнер, содержащий связанные ресурсы решения Azure), Центр Интернета вещей, три удостоверения устройства IoT Edge, настроить теги, а затем создать развертывание IoT Edge, предназначенное для этих устройств. 

Войдите в свою учетную запись Azure. После ввода следующей команды появится запрос на вход через браузер с использованием одноразового кода: 

   ```cli
   az login
   ```

Создайте группу ресурсов **IoTHubCLI** в регионе "Восточная часть США": 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Создать группу ресурсов][2]

Создайте Центр Интернета вещей **CLIDemoHub** в новой группе ресурсов:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >В подписке может быть только один бесплатный Центр Интернета вещей. Чтобы создать бесплатный Центр с помощью команды CLI, замените значение SKU на `--sku F1`. Если в подписке уже имеется бесплатный Центр, при попытке создать второй появится сообщение об ошибке. 

Создайте устройство IoT Edge:

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![Создание устройства IoT Edge][4]

## <a name="configure-the-iot-edge-device"></a>Настройка устройства IoT Edge

Создайте шаблон развертывания JSON и сохраните его локально как TXT-файл. Вам необходимо будет указать путь к файлу при запуске команды apply-configuration.

Шаблоны развертывания JSON всегда должны содержать два системных модуля: edgeAgent и edgeHub. С помощью этого файла на устройство IoT Edge можно развернуть дополнительные модули, а не только эти два. Чтобы настроить устройство IoT Edge с одним модулем tempSensor, используйте следующий пример:

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
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

Примените конфигурацию к устройству IoT Edge:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Просмотрите модули на устройстве IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Вывод списка модулей][6]

## <a name="next-steps"></a>Дополнительная информация

* [Создание устройства IoT Edge, которое работает как прозрачный шлюз — предварительная версия](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

