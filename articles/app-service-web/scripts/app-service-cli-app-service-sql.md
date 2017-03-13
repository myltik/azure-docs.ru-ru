---
title: "Пример скрипта Azure CLI. Подключение веб-приложения к базе данных SQL | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Подключение веб-приложения к базе данных SQL"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97ab792a0ad7420e97dbab378736b9815356e8a4
ms.openlocfilehash: 72c91ef9c59fb42e7a2a233e258622417b2f84ac
ms.lasthandoff: 02/27/2017

---

# <a name="connect-a-web-app-to-a-sql-database"></a>Подключение веб-приложения к базе данных SQL

В этой статье вы узнаете, как создать базу данных SQL и веб-приложение Azure. Затем вы свяжете базу данных SQL с веб-приложением, используя параметры приложения.

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "База данных SQL")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера скрипта можно удалить группу ресурсов, приложение службы приложений и все связанные ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, веб-приложения, базы данных SQL и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. Это как ферма сервера для веб-приложения Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Создает веб-приложение Azure в плане службы приложений. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Создает сервер базы данных SQL.  |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Создает базу данных с помощью сервера базы данных SQL. |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Создает или обновляет параметр приложения для веб-приложения Azure. Параметры приложения представляются в качестве переменных среды для приложения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
