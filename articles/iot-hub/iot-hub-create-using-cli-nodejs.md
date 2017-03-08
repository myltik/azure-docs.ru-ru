---
title: "Создание Центра Интернета вещей с помощью Azure CLI (azure.js) | Документация Майкрософт"
description: "Как создать Центр Интернета вещей Azure, используя кроссплатформенный Azure CLI (azure.js)."
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 5ea7095e12b6194556d3cd0baa43ccfed1e087ee
ms.openlocfilehash: 1be7dc038f1bc864e7c6461b616449d75ecda439
ms.lasthandoff: 02/27/2017


---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Создание Центра Интернета вещей с помощью Azure CLI
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Введение
Вы можете использовать Azure CLI (azure.js), чтобы создать Центр Интернета вещей Azure программным способом и управлять им. В этой статье рассказывается, как создать Центр Интернета вещей с помощью Azure CLI.

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

* Azure CLI (azure.js) — это интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager, как описано в этой статье.
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Интерфейс командной строки Azure 0.10.4][lnk-CLI-install] или более поздней версии. Если установлен Azure CLI, можно проверить его текущую версию в командной строке, выполнив следующую команду:
  ```
    azure --version
  ```

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). Интерфейс командной строки Azure нужно переключить в режим Azure Resource Manager:
> 
> ```
> azure config mode arm
> ```
> 
> 

## <a name="set-your-azure-account-and-subscription"></a>Настройка учетной записи и подписки Azure
1. Выполните вход, введя в командной строке следующую команду:
   
   ```
    azure login
   ```
   Пройдите аутентификацию, используя предлагаемый браузер и код.
2. Если у вас есть несколько подписок Azure, то при подключении к Azure вы получаете доступ ко всем подпискам Azure, связанным с вашими учетными данными. Вы можете просмотреть подписки Azure, а также узнать, какая из них используется по умолчанию, выполнив команду:
   
   ```
    azure account list 
   ```

   Чтобы задать контекст подписки, в котором будут выполняться остальные команды, используйте следующую команду:

   ```
    azure account set <subscription name>
   ```

3. Создайте группу ресурсов с именем **exampleResourceGroup**, если она не создана:
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> Дополнительные сведения об управлении ресурсами и группами ресурсов Azure с помощью Azure CLI см. в [этой статье][lnk-CLI-arm]. 
> 
> 

## <a name="create-an-iot-hub"></a>Создание центра IoT
Ниже перечислены необходимые параметры:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Чтобы просмотреть все параметры для создания, можно воспользоваться командой справки в командной строке:

```
    azure iothub create -h 
```
Краткий пример:

 Чтобы создать Центр Интернета вещей **exampleIoTHubName** в группе ресурсов **exampleResourceGroup**, выполните следующую команду:

```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Эта команда интерфейса командной строки Azure создает стандартный центр Интернета вещей S1, за который взимается плата. Чтобы удалить Центр Интернета вещей **exampleIoTHubName**, выполните следующую команду:
> 
> ```
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```
> 
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о разработке для центра IoT см. в следующих руководствах.

* [Пакеты SDK для Центра Интернета вещей][lnk-sdks]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Создание Центра Интернета вещей через портал Azure][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 

