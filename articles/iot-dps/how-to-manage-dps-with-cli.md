---
title: Как управлять службами подготовки устройств с помощью Azure CLI 2.0 и расширения Интернета вещей | Документация Майкрософт
description: Узнайте, как управлять службами подготовки устройств с помощью Azure CLI 2.0 и расширения Интернета вещей
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 174f8447b17d1fa580472cbb45d0a72f41c793c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628323"
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Как управлять службами подготовки устройств с помощью Azure CLI 2.0 и расширения Интернета вещей

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Интерфейс Azure CLI 2.0 доступен для операционных систем Windows, Linux и MacOS. Azure CLI 2.0 позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки.

Расширение Интернета вещей расширяет функции Azure CLI 2.0 (например, функция управления устройствами) и добавляет возможности IoT Edge.

В этом руководстве вы выполните действия для настройки Azure CLI 2.0 и расширения Интернета вещей. Затем вы узнаете, как выполнять команды CLI для выполнения основных операций службы подготовки устройств. 

## <a name="installation"></a>Установка 

### <a name="step-1---install-python"></a>Шаг 1. Установка Python

Необходимы [Python 2.7x или Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-azure-cli-20"></a>Шаг 2. Установка Azure CLI 2.0

Выполните [инструкции по установке](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), чтобы настроить Azure CLI 2.0 в своей среде. Вам понадобится как минимум Azure CLI 2.0 версии 2.0.24 или более поздней. Для проверки используйте `az –version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. Простой способ установки в Windows — скачать и установить [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Шаг 3. Установка расширения Интернета вещей

В [файле сведений расширения Интернета вещей](https://github.com/Azure/azure-iot-cli-extension) описывается несколько способов установки расширения. Проще всего запустить `az extension add --name azure-cli-iot-ext`. После установки можно использовать `az extension list`, чтобы проверить установленные расширения, или `az extension show --name azure-cli-iot-ext` для просмотра сведений о расширении Интернета вещей. Чтобы удалить расширение, можно использовать `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Операции службы подготовки устройств (базовые)
В примере показано, как войти в учетную запись Azure, создать группу ресурсов Azure (контейнер, содержащий связанные ресурсы для решения Azure), создать Центр Интернета вещей, службу подготовки устройств, список имеющихся служб подготовки устройств и связанный Центр Интернета вещей с командами CLI. 

Перед началом работы выполните описанные выше шаги по установке. Если у вас нет учетной записи Azure, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Войдите в учетную запись Azure
  
    az login

![вход][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Создайте группу ресурсов IoTHubBlogDemo в регионе eastus

    az group create -l eastus -n IoTHubBlogDemo

![Создать группу ресурсов][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Создайте две службы подготовки устройств

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Создайте службу политики диагностики][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Создайте список всех имеющихся служб подготовки устройств в этой группе ресурсов

    az iot dps list --resource-group IoTHubBlogDemo

![Список служб политики диагностики][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Создайте Центр Интернета вещей blogDemoHub в созданной группе ресурсов.

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Создание Центра Интернета вещей][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Свяжите один имеющийся Центр Интернета вещей со службой подготовки устройств

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Связывание Центра Интернета вещей][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Регистрация устройства
> * запуск устройства;
> * проверка регистрации устройства.

Перейдите к следующему руководству, чтобы узнать, как подготовить несколько устройств через концентраторы с балансировкой нагрузки. 

> [!div class="nextstepaction"]
> [Подготовка устройств в Центрах Интернета вещей с балансировкой нагрузки](./tutorial-provision-multiple-hubs.md)
