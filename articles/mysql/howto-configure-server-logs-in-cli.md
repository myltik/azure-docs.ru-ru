---
title: "Получение доступа к журналам сервера в базе данных Azure для MySQL с помощью Azure CLI | Документация Майкрософт"
description: "В этой статье описывается, как получить доступ к журналам сервера в базе данных Azure для MySQL с помощью служебной программы командной строки Azure CLI."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 6ee2c2c6e6cff824d3167ea600a1ddc778ad011b
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Настройка журналов сервера и получение к ним доступа с помощью Azure CLI
Скачать журналы сервера из базы данных Azure для MySQL можно с помощью портала Azure или Azure CLI, служебной программы командной строки Azure.

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-cli.md);
- [Azure CLI 2.0](/cli/azure/install-azure-cli) (или используйте Azure Cloud Shell в браузере).

## <a name="configure-logging-for-azure-database-for-mysql"></a>Настройка ведения журнала для базы данных Azure для MySQL
Можно настроить на сервере доступ к журналу медленных запросов MySQL.
1. Включите ведение журнала, установив для параметра **slow\_query\_log** значение ON.
2. Настройте другие параметры, такие как **long\_query\_time** и **log\_slow\_admin\_statements**.

Ознакомьтесь с разделом [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md), чтобы узнать, как задать значение этих параметров с помощью Azure CLI.

Например, приведенная ниже команда интерфейса командной строки включает журнал медленных запросов, задает длительность запроса в 10 секунд и отключает ведение журнала медленных инструкций администрирования. Наконец, она выводит параметры конфигурации, чтобы вы могли их просмотреть.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Получение списка журналов для сервера базы данных Azure для MySQL
Чтобы получить список доступных файлов журналов для сервера, выполните команду [az mysql server-logs list](/cli/azure/mysql/server-logs#list).

Вы можете получить список файлов журналов для сервера **myserver4demo.mysql.database.azure.com** в группе ресурсов **myresourcegroup** и направить его в текстовый файл **log\_files\_list.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Скачивание журналов с сервера
Команда [az mysql server-logs download](/cli/azure/mysql/server-logs#download) позволяет скачать отдельные файлы журналов для сервера. 

Этот пример скачивает в локальную среду определенный файл журнала для сервера **myserver4demo.mysql.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше о [журналах сервера в базе данных Azure для MySQL](concepts-server-logs.md).
