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
ms.date: 05/04/2017
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 8ac82da36b2edb71fcd0599dac12a3ed18e33b6f
ms.lasthandoff: 03/24/2017


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

```azurecli
azure --version
```

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). Интерфейс командной строки Azure нужно переключить в режим Azure Resource Manager:
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Настройка учетной записи и подписки Azure

1. Выполните вход, введя в командной строке следующую команду:

   ```azurecli
    azure login
   ```

   Пройдите аутентификацию, используя предлагаемый браузер и код.
1. Если у вас есть несколько подписок Azure, то при подключении к Azure вы получаете доступ ко всем подпискам Azure, связанным с вашими учетными данными. Вы можете просмотреть подписки Azure, а также узнать, какая из них используется по умолчанию, выполнив команду:

   ```azurecli
    azure account list
   ```

   Чтобы задать контекст подписки, в котором будут выполняться остальные команды, используйте следующую команду:

   ```azurecli
    azure account set <subscription name>
   ```

1. Создайте группу ресурсов с именем **exampleResourceGroup**, если она не создана:

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> Дополнительные сведения об управлении ресурсами и группами ресурсов Azure с помощью Azure CLI см. в [этой статье][lnk-CLI-arm].

## <a name="create-an-iot-hub"></a>Создание центра IoT

Ниже перечислены необходимые параметры:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resource-group**. Имя группы ресурсов. Может содержать 1–64 знака (буквы, цифры, подчеркивания и дефисы). Регистр знаков не учитывается.
* **name**. Имя создаваемого Центра Интернета вещей. Может содержать 3–50 знаков (буквы, цифры, подчеркивания и дефисы). Регистр знаков не учитывается.
* **location**. Расположение (регион или центр обработки данных Azure) для подготовки Центра Интернета вещей.
* **sku-name**. Имя SKU. Возможные значения: [F1, S1, S2, S3]. Наиболее актуальный и полный список доступен на странице цен а Центр Интернета вещей.
* **units**. Количество подготовленных единиц. Диапазон: F1 [1]; S1, S2 [1–200]; S3 [1–10]. Единицы Центра Интернета вещей зависят от общего количества сообщений и количества устройств, к которым требуется подключаться.

Чтобы просмотреть все параметры для создания, можно воспользоваться командой справки в командной строке:

```azurecli
azure iothub create -h
```

Быстрый пример. Чтобы создать Центр Интернета вещей **exampleIoTHubName** в группе ресурсов **exampleResourceGroup**, выполните следующую команду.

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Эта команда интерфейса командной строки Azure создает стандартный центр Интернета вещей S1, за который взимается плата. Чтобы удалить Центр Интернета вещей **exampleIoTHubName**, выполните следующую команду:
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующей статье:

* [Пакеты SDK для Центра Интернета вещей][lnk-sdks]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Создание Центра Интернета вещей через портал Azure][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 

