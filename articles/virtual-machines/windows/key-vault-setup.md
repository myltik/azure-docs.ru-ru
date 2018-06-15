---
title: Настройка Key Vault для виртуальных машин Windows в Azure Resource Manager | Документация Майкрософт
description: Узнайте, как настроить хранилище ключей для использования с виртуальной машиной Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: f8f094bfb0f304123cbdf719bec22185431aca5a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918393"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Настройка хранилища ключей для виртуальных машин в Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

В стеке Azure Resource Manager секреты или сертификаты моделируются как ресурсы, которые предоставляются поставщиком ресурсов хранилища ключей. Чтобы больше узнать о хранилище ключей, ознакомьтесь с разделом [Что такое хранилище ключей Azure?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Чтобы хранилище ключей можно было использовать для виртуальных машин Azure Resource Manager, свойству **EnabledForDeployment** хранилища ключей должно быть присвоено значение true. Это можно сделать на различных клиентах.
> 2. Хранилище ключей должно быть создано в тех же подписке и расположении, что и виртуальная машина.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Использование PowerShell для настройки хранилища ключей
Сведения о создании хранилища ключей с помощью PowerShell см. в разделе [Приступая к работе с хранилищем ключей Azure](../../key-vault/key-vault-get-started.md#vault).

Для новых хранилищ ключей можно использовать этот командлет PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Для существующих хранилищ ключей можно использовать этот командлет PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Использование интерфейса командной строки для настройки хранилища ключей
Сведения об использовании интерфейса командной строки (CLI) для создания хранилища ключей см. в статье [Управление хранилищем ключей с помощью CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Чтобы использовать интерфейс командной строки, необходимо сначала создать хранилище ключей, а затем назначить политику развертывания. Это можно сделать с помощью следующей команды:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Использование шаблонов для настройки хранилища ключей
При использовании шаблона свойству `enabledForDeployment` ресурса хранилища ключей нужно присвоить значение `true`.

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

Сведения о других параметрах, которые можно настроить при создании хранилища ключей с помощью шаблонов, см. в статье [Создание хранилища ключей](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
