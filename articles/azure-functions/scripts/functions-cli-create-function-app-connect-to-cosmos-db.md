---
title: "Создание функции Azure, которая подключается к Azure Cosmos DB | Документация Майкрософт"
description: "Пример скрипта Azure CLI для создания функции Azure, которая подключается к Azure Cosmos DB."
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: dbe02fbc71f04c67ab03a84845bf8666333ce621
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Создание функции Azure, которая подключается к Azure Cosmos DB

Этот пример сценария создает приложение-функцию Azure и подключается к Azure Cosmos DB.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

Этот пример создает приложение-функцию Azure, а также добавляет конечную точку и ключ доступа Cosmos DB в параметры приложения.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Создание функции Azure, которая подключается к Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, использовав следующую команду.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Вход в Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создайте группу ресурсов с расположением. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Создайте учетную запись хранения. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Создайте приложение-функцию. |
| [az documentdb create](https://docs.microsoft.com/cli/azure/documentdb#create) | Создайте базу данных DocumentDB. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Очистка |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).





