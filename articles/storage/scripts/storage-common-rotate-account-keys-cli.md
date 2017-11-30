---
title: "Пример скрипта Azure CLI для смены ключей доступа учетных записей хранения | Документы Майкрософт"
description: "Сведения о создании учетной записи хранения Azure и последующих операциях извлечения и смены ее ключей доступа."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: marsma
ms.openlocfilehash: 743c7b01e82721e855b4c33bf2d36714ca8c6468
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Создание учетной записи хранения и смена ее ключей доступа

Этот скрипт создает учетную запись хранения Azure, отображает ключи доступа новой учетной записи хранения, а затем обновляет (меняет) ключи.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните команду ниже, чтобы удалить группу ресурсов, учетную запись хранения и все связанные ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды для создания учетной записи хранения и извлечения и смены ее ключей доступа. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](/cli/azure/storage/account#create) | Создает учетную запись хранения Azure в указанной группе ресурсов. |
| [az storage account keys list](/cli/azure/storage/account/keys#list) | Отображает ключи доступа для указанной учетной записи хранения. |
| [az storage account keys renew](/cli/azure/storage/account/keys#renew) | Повторно создает первичный или вторичный ключи доступа учетной записи хранения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure/overview).

Дополнительные примеры скриптов CLI хранилища см. в статье [Примеры скриптов Azure CLI для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-cli.md).
