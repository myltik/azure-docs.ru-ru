---
title: "Пример скрипта Azure CLI для смены ключей доступа учетных записей хранения | Документы Майкрософт"
description: "Сведения о создании учетной записи хранения Azure и последующих операциях извлечения и смены ее ключей доступа."
services: storage
documentationcenter: na
author: mmacy
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1b59144e0426b50c2ac49acbd7914d975ff037ec
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Создание учетной записи хранения и смена ее ключей доступа

Этот скрипт создает учетную запись хранения Azure, отображает ключи доступа новой учетной записи хранения, а затем обновляет (меняет) ключи.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, учетную запись хранения и все связанные ресурсы.

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

Дополнительные примеры скриптов CLI хранилища можно найти в статье [Azure CLI samples for Azure Blob storage](../blobs/storage-samples-blobs-cli.md) (Примеры скриптов Azure CLI для хранилища BLOB-объектов Azure).

