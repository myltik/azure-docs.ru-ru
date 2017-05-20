---
title: "Создание функции Azure, которая подключается к Azure DocumentDB | Документация Майкрософт"
description: "Пример сценария Azure CLI для создания функции Azure, которая подключается к Azure DocumentDB."
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 46e0fe827b7b34010d14a31ff377d4854ec62f6e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Создание функции Azure, которая подключается к Azure Cosmos DB

Этот пример сценария создает приложение-функцию Azure и подключается к Azure Cosmos DB.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

Создание примера приложения

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Создание функции Azure, которая подключается к Azure Cosmos DB")]

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





