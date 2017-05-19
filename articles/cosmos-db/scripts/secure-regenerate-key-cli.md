---
title: "Повторное создание ключа учетной записи для базы данных Azure Cosmos DB с помощью скрипта Azure CLI | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Повторное создание ключа учетной записи для базы данных Azure Cosmos DB"
services: cosmosdb
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ffdf78a42d5ff6ec2500167ecf1f31f17db3b830
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="regenerate-an-azure-cosmos-db-account-key-using-the-azure-cli"></a>Повторное создание ключа учетной записи для базы данных Azure Cosmos DB с помощью Azure CLI

Этот пример повторно создает любой вид ключа учетной записи базы данных Azure Cosmos DB с помощью Azure CLI. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-regenerate-keys/secure-cosmosdb-regenerate-keys.sh?highlight=27-31 "Повторное создание ключа учетной записи для базы данных Azure Cosmos DB")]

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
| [az cosmosdb create](/cli/azure/cosmosdb/name#create) | Обновляет учетную запись базы данных Azure Cosmos DB. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb/regenerate-key) | Повторно создает ключи учетной записи базы данных Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов CLI для базы данных Azure Cosmos DB см. в [документации по интерфейсу командной строки базы данных Azure Cosmos DB](../cli-samples.md).

