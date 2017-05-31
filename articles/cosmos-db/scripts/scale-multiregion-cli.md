---
title: "Скрипт Azure CLI. Репликация в нескольких регионах для базы данных Azure Cosmos DB | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Репликация в нескольких регионах для базы данных Azure Cosmos DB"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: sample
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cde5ff007dc7b26db33e195dd6cb528675ecd391
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---

# <a name="replicate-an-azure-cosmos-db-database-account-in-multiple-regions-and-configure-failover-priorities-using-the-azure-cli"></a>Репликация учетной записи базы данных Azure Cosmos DB в нескольких регионах и настройка приоритетов отработки отказа с помощью Azure CLI

Этот пример реплицирует любую учетную запись базы данных Azure Cosmos DB в нескольких регионах и настраивает приоритеты отработки отказа с помощью Azure CLI.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-replicate-multiple-regions/scale-cosmosdb-replicate-multiple-regions.sh?highlight=21-31 "Масштабирование базы данных Azure Cosmos DB в нескольких регионах")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | Обновляет учетную запись базы данных Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов CLI для базы данных Azure Cosmos DB см. в [документации по интерфейсу командной строки базы данных Azure Cosmos DB](../cli-samples.md).

