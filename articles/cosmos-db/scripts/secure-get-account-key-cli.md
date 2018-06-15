---
title: Получение ключей учетной записи для базы данных Azure Cosmos DB с помощью скрипта Azure CLI | Документация Майкрософт
description: Пример скрипта Azure CLI. Получение ключей учетной записи для базы данных Azure Cosmos DB
services: cosmos-db
documentationcenter: cosmosdb
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: sngun
ms.openlocfilehash: accd4209276425dc7c7f24e921d14d903fc03f2d
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794910"
---
# <a name="get-account-keys-for-azure-cosmos-db-using-the-azure-cli"></a>Получение ключей учетной записи для базы данных Azure Cosmos DB с помощью Azure CLI

Этот пример получает ключи учетной записи для любой учетной записи базы данных Azure Cosmos DB.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-get-account-key/secure-cosmosdb-get-account-key.sh?highlight=22-25 "Get Azure Cosmos DB account keys")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az cosmosdb update](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_update) | Обновляет учетную запись базы данных Azure Cosmos DB. |
| [az cosmosdb list-keys](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_list_keys) | Создает логический сервер, на котором размещена база данных SQL. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов CLI для базы данных Azure Cosmos DB см. в [документации по интерфейсу командной строки базы данных Azure Cosmos DB](../cli-samples.md).
