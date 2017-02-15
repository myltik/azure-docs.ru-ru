---
title: "Настройка хранилища ключей для виртуальных машин в Azure Resource Manager | Документация Майкрософт"
description: "Узнайте, как настроить хранилище ключей для использования с виртуальной машиной Azure Resource Manager."
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
ms.date: 05/31/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: bc60d5659c80ad1245d7a34b4cd81c0ae73a5ba9


---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Настройка хранилища ключей для виртуальных машин в Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

В стеке Azure Resource Manager секреты или сертификаты моделируются как ресурсы, которые предоставляются поставщиком ресурсов хранилища ключей. Чтобы больше узнать о хранилище ключей Azure, ознакомьтесь с разделом [Что такое хранилище ключей Azure?](../key-vault/key-vault-whatis.md)

Чтобы хранилище ключей можно было использовать для виртуальных машин Azure Resource Manager, свойству *EnabledForDeployment* хранилища ключей должно быть задано значение true. Это можно сделать на различных клиентах.

## <a name="use-cli-to-set-up-key-vault"></a>Использование интерфейса командной строки для настройки хранилища ключей
Сведения об использовании интерфейса командной строки (CLI) для создания хранилища ключей см. в статье [Управление хранилищем ключей с помощью CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Чтобы использовать интерфейс командной строки, необходимо сначала создать хранилище ключей, а затем назначить политику развертывания. Это можно сделать с помощью следующей команды:

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




<!--HONumber=Nov16_HO3-->


