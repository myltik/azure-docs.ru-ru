---
title: "Пример скрипта Azure CLI. Подключение веб-приложения к DocumentDB | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Подключение веб-приложения к DocumentDB"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 80b6d446fd9a0ac6434c8415b97ff0f07eb0da12
ms.lasthandoff: 03/11/2017

---

# <a name="connect-a-web-app-to-a-documentdb"></a>Подключение веб-приложения DocumentDB

Из этой статьи вы узнаете, как создать Azure DocumentDB и веб-приложение Azure. Затем вы свяжете DocumentDB с веб-приложением, используя параметры приложения.

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure DocumentDB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, веб-приложений, DocumentDB и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. Это как ферма сервера для веб-приложения Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Создает веб-приложение Azure в плане службы приложений. |
| [az documentdb create](https://docs.microsoft.com/en-us/cli/azure/documentdb#create) | Создает DocumentDB. Здесь будут храниться данные. |
| [az documentdb list-keys](https://docs.microsoft.com/en-us/cli/azure/documentdb#list-keys) | Список ключей доступа для указанной учетной записи базы данных Azure DocumentDB. |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Создает или обновляет параметр приложения для веб-приложения Azure. Параметры приложения представляются в качестве переменных среды для приложения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
