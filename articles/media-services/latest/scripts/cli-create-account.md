---
title: Пример скрипта Azure CLI. Создание учетной записи Служб мультимедиа Azure | Документация Майкрософт
description: Создание учетной записи Служб мультимедиа Azure с помощью скрипта Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 783dc0fd37f98a12d9240ad4b3ee72aa98212eff
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Пример скрипта CLI. Создание учетной записи Служб мультимедиа Azure

В скрипте Azure CLI в этой статье показано, как создать учетную запись Служб мультимедиа Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.20 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующую команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Создание учетной записи хранения. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Создание учетной записи Служб мультимедиа. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Создание субъекта-службы с паролем и настройка его доступа к учетной записи Служб мультимедиа Azure. 
| [az group delete](/cli/azure/group#az_group_delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |


## <a name="next-steps"></a>Дополнительная информация

См. [другие примеры Azure CLI](../cli-samples.md).
