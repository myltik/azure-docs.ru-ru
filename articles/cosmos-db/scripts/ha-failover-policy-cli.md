---
title: "Создание политики отработки отказа с помощью скрипта Azure CLI для обеспечения высокой доступности | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание политики отработки отказа для обеспечения высокой доступности"
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
ms.openlocfilehash: 96083d66cc1a2ef179f9313c1b3ed04162c1c048
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017

---

<a id="create-a-failover-policy-for-high-availability-using-the-azure-cli" class="xliff"></a>

# Создание политики отработки отказа для обеспечения высокой доступности с помощью Azure CLI

Данный пример скрипта CLI создает учетную запись базы данных Azure Cosmos DB, а затем настраивает ее для обеспечения высокой доступности.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

<a id="sample-script" class="xliff"></a>

## Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/high-availability-cosmosdb-configure-failover/high-availability-cosmosdb-configure-failover.sh?highlight=23-27 "Создание политики отработки отказа для базы данных Azure Cosmos DB")]

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
| [az cosmosdb create](/cli/azure/sql/server#create) | Создает учетную запись базы данных Azure Cosmos DB. |
| [az cosmosdb update](/cli/azure/cosmosdb#update) | Обновляет учетную запись базы данных Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов CLI для базы данных Azure Cosmos DB см. в [документации по интерфейсу командной строки базы данных Azure Cosmos DB](../cli-samples.md).

