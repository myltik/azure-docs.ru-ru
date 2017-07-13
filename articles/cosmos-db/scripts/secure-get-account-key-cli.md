---
title: "Получение ключей учетной записи для базы данных Azure Cosmos DB с помощью скрипта Azure CLI | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Получение ключей учетной записи для базы данных Azure Cosmos DB"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: ef3b147e0249e41c65fa88002cbb883d001bd382
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017

---

<a id="get-account-keys-for-azure-cosmos-db-using-the-azure-cli" class="xliff"></a>

# Получение ключей учетной записи для базы данных Azure Cosmos DB с помощью Azure CLI

Этот пример получает ключи учетной записи для любой учетной записи базы данных Azure Cosmos DB.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

<a id="sample-script" class="xliff"></a>

## Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-get-account-key/secure-cosmosdb-get-account-key.sh?highlight=22-25 "Получение ключей учетной записи для базы данных Azure Cosmos DB")]

<a id="clean-up-deployment" class="xliff"></a>

## Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli-interactive
az group delete --name myResourceGroup
```

<a id="script-explanation" class="xliff"></a>

## Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | Обновляет учетную запись базы данных Azure Cosmos DB. |
| [az cosmosdb list-keys](/cli/azure/sql/server#create) | Создает логический сервер, на котором размещена база данных SQL. |
| [az group delete](/cli/azure/resource#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов CLI для базы данных Azure Cosmos DB см. в [документации по интерфейсу командной строки базы данных Azure Cosmos DB](../cli-samples.md).

