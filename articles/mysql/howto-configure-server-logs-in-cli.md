---
title: Доступ к журналам сервера в службе "База данных Azure для MySQL" с помощью Azure CLI
description: В этой статье объясняется, как получить доступ к журналам сервера в службе "База данных Azure для MySQL" с помощью служебной программы командной строки Azure CLI.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 85c7840c0e919e77e807e6114c4d0c65601ff334
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265832"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Настройка журналов сервера и получение к ним доступа с помощью Azure CLI
Скачать журналы сервера из службы "База данных Azure для MySQL" можно с помощью портала Azure или Azure CLI, служебной программы командной строки Azure.

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).
- [Azure CLI 2.0](/cli/azure/install-azure-cli) или Azure Cloud Shell в браузере.

## <a name="configure-logging-for-azure-database-for-mysql"></a>Настройка ведения журнала для базы данных Azure для MySQL
Можно настроить на сервере доступ к журналу медленных запросов MySQL, выполнив следующие действия:
1. Включите ведение журнала, установив для параметра **slow\_query\_log** значение ON.
2. Настройте другие параметры, такие как **long\_query\_time** и **log\_slow\_admin\_statements**.

Ознакомьтесь со статьей [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md), чтобы узнать, как задать значение этих параметров с помощью Azure CLI. 

Например, приведенная ниже команда интерфейса командной строки включает журнал медленных запросов, задает длительность запроса в 10 секунд и отключает ведение журнала медленных инструкций администрирования. Наконец, она выводит параметры конфигурации, чтобы вы могли их просмотреть.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Получение списка журналов для сервера базы данных Azure для MySQL
Чтобы получить список доступных файлов журналов для сервера, выполните команду [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list).

Вы можете вывести список файлов журнала для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup**. Затем направьте список файлов журнала в текстовый файл с именем **log\_files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Скачивание журналов с сервера
Команда [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) позволяет скачать отдельные файлы журналов для сервера. 

В следующем примере в локальную среду скачивается определенный файл журнала для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Дополнительная информация
- Ознакомьтесь со сведениями о [журналах сервера в службе "База данных Azure для MySQL"](concepts-server-logs.md).
