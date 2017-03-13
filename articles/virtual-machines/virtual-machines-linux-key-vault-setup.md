---
title: "Настройка Azure Key Vault для виртуальных машин Linux | Документация Майкрософт"
description: "Как настроить Key Vault для использования с виртуальной машиной Azure Resource Manager c помощью интерфейса командной строки 2.0."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 54c3bd47eaedee0d2269ee49143b18dc6b702e1b
ms.openlocfilehash: 16994b3dfe4945b1b023fa23aafd8541c74ae2e1
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Как настроить Key Vault для виртуальных машин с помощью Azure CLI 2.0

В стеке Azure Resource Manager секреты и сертификаты представляют собой ресурсы, которые предоставляются Key Vault. Чтобы больше узнать о хранилище ключей Azure, ознакомьтесь с разделом [Что такое хранилище ключей Azure?](../key-vault/key-vault-whatis.md) Чтобы Key Vault можно было использовать для виртуальных машин Azure Resource Manager, свойству *EnabledForDeployment* в Key Vault должно быть присвоено значение true. В этой статье показано, как настроить Key Vault для использования с виртуальными машинами Azure с помощью Azure CLI 2.0. Эти действия можно также выполнить с помощью [Azure CLI 1.0](virtual-machines-linux-key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Чтобы выполнить эти действия, нужно установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/#login).

## <a name="create-a-key-vault"></a>создать хранилище ключей;
Создайте Key Vault и назначьте политику развертывания с помощью команды [az keyvault create](/cli/azure/keyvault#create). В следующем примере создается Key Vault `myKeyVault` в группе ресурсов `myResourceGroup`.

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Обновление Key Vault для использования с виртуальными машинами
Установите политику развертывания в существующем Key Vault с помощью команды [az keyvault update](/cli/azure/keyvault#update). В следующем примере обновляется Key Vault `myKeyVault` в группе ресурсов `myResourceGroup`.

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Использование шаблонов для настройки хранилища ключей
При использовании шаблона свойству `enabledForDeployment` ресурса Key Vault нужно присвоить значение `true`, как показано ниже.

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других параметрах, которые можно настроить при создании Key Vault с помощью шаблонов, см. в разделе [Create a Key Vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/) (Создание Key Vault).

