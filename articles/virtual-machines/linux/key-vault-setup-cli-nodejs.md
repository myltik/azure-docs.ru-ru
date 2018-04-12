---
title: Настройка Key Vault для виртуальных машин Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: Как настроить Key Vault для использования с виртуальной машиной Azure Resource Manager c помощью Azure CLI 1.0.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: a9225429e878415334b0c8a66777902395606d63
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>Настройка Key Vault для виртуальных машин в Azure Resource Manager с помощью Azure CLI 1.0
В стеке Azure Resource Manager секретные данные или сертификаты моделируются как ресурсы, которые предоставляются поставщиком ресурсов Key Vault. Чтобы больше узнать о хранилище ключей Azure, ознакомьтесь с разделом [Что такое хранилище ключей Azure?](../../key-vault/key-vault-whatis.md) Чтобы хранилище ключей можно было использовать для виртуальных машин Azure Resource Manager, свойству *EnabledForDeployment* хранилища ключей должно быть присвоено значение true. Это можно сделать на различных клиентах. В этой статье показано, как настроить Key Vault для использования с виртуальными машинами Azure.

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="use-cli-10-to-set-up-key-vault"></a>Настройка Key Vault с помощью интерфейса командной строки версии 1.0
Сведения об использовании интерфейса командной строки (CLI) для создания хранилища ключей см. в статье [Управление хранилищем ключей с помощью CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Для интерфейса командной строки версии 1.0 необходимо создать хранилище ключей перед назначением политики развертывания. Затем можно назначить политику, используя следующую команду:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Использование шаблонов для настройки хранилища ключей
При использовании шаблона свойству `enabledForDeployment` ресурса хранилища ключей нужно задать значение `true`.

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
