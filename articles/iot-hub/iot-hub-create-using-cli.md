---
title: Создание центра Интернета вещей с помощью Azure CLI (az.py) | Документация Майкрософт
description: Как создать Центр Интернета вещей Azure, используя кроссплатформенный Azure CLI 2.0 (az.py).
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 9f97775a5a49077a340efb0e3de14b7064db5fe4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632770"
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Создание Центра Интернета вещей с помощью Azure CLI 2.0

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Введение

Вы можете использовать Azure CLI 2.0 (az.py), чтобы создать Центр Интернета вещей Azure программным способом и управлять им. В этой статье показано, как создать Центр Интернета вещей с помощью Azure CLI 2.0 (az.py).

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

* [Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) — это интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
* Azure CLI 2.0 (az.py) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager, как описано в этой статье.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* [Azure CLI 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Выполнение входа и установка учетной записи Azure

Войдите в учетную запись Azure и выберите подписку.

1. В командной строке запустите [команду для входа][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Следуйте инструкциям, чтобы выполнить аутентификацию с использованием кода и войти в учетную запись Azure через веб-браузер.

2. Если у вас есть несколько подписок Azure, то при выполнении входа в Azure вы получаете доступ ко всем учетным записям Azure, связанным с вашими учетными данными. Используйте следующую [команду для вывода учетных записей Azure][lnk-az-account-command], доступных для использования:
    
    ```azurecli
    az account list 
    ```

    Используйте следующую команду, чтобы выбрать подписку, которая будет использоваться для выполнения команд для создания Центра Интернета вещей. Вы можете использовать имя подписки или идентификатор из выходных данных предыдущей команды:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>Создание центра Интернета вещей

Создайте группу ресурсов и добавьте Центр Интернета вещей с помощью Azure CLI.

1. Создайте Центр Интернета вещей в группе ресурсов. Используйте существующую группу ресурсов либо выполните следующую [команду для создания группы ресурсов][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > В предыдущем примере создается группа ресурсов в расположении западной части США. Список доступных расположений можно просмотреть, выполнив команду `az account list-locations -o table`.
    >
    >

2. Выполните в своей группе ресурсов следующую [команду для создания Центра Интернета вещей][lnk-az-iot-command], указав глобально уникальное имя своего Центра Интернета вещей.
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> Предыдущая команда создает Центр Интернета вещей в оплачиваемой ценовой категории S1. Дополнительные сведения см. на странице с [ценами на Центр Интернета вещей Azure][lnk-iot-pricing].
>

## <a name="remove-an-iot-hub"></a>Удаление Центра Интернета вещей

Вы можете использовать Azure CLI для [удаления отдельного ресурса][lnk-az-resource-command], например Центра Интернета вещей, или группы ресурсов и всех ее ресурсов, включая любые Центры Интернета вещей.

Чтобы удалить Центр Интернета вещей, выполните следующую команду:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Чтобы удалить группу ресурсов и все ее ресурсы, выполните следующую команду:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Управление Центром Интернета вещей с помощью портала Azure][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
