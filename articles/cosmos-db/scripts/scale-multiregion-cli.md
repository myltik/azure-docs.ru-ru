---
title: Скрипт Azure CLI. Репликация в нескольких регионах для базы данных Azure Cosmos DB | Документация Майкрософт
description: Пример скрипта Azure CLI. Репликация в нескольких регионах для базы данных Azure Cosmos DB
services: cosmos-db
documentationcenter: cosmosdb
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.assetid: ''
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: sngun
ms.openlocfilehash: ce50a8d0e42a7799ea85d8fec92f4c9e27cd6bf8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="replicate-an-azure-cosmos-db-database-account-in-multiple-regions-and-configure-failover-priorities-using-the-azure-cli"></a>Репликация учетной записи базы данных Azure Cosmos DB в нескольких регионах и настройка приоритетов отработки отказа с помощью Azure CLI

Этот пример реплицирует любую учетную запись базы данных Azure Cosmos DB в нескольких регионах и настраивает приоритеты отработки отказа с помощью Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-replicate-multiple-regions/scale-cosmosdb-replicate-multiple-regions.sh?highlight=21-31 "Scale Azure Cosmos DB into multiple regions")]

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
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов CLI для базы данных Azure Cosmos DB см. в [документации по интерфейсу командной строки базы данных Azure Cosmos DB](../cli-samples.md).
