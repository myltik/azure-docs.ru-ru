---
title: "Настройка журналов сервера для PostgreSQL и получение к ним доступа с помощью Azure CLI | Документация Майкрософт"
description: "В этой статье описывается настройка журналов сервера в базе данных Azure для PostgreSQL с помощью командной строки CLI Azure и получение доступа к этим журналам."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: d18ec44ecede44829b488ac9864bbfae2c62883a
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Настройка журналов сервера и получение к ним доступа с помощью Azure CLI
Вы можете скачать журналы ошибок сервера PostgreSQL с помощью интерфейса командной строки (Azure CLI). Однако доступ к журналам транзакций не поддерживается. 

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md);
- Установите служебную программу командной строки [Azure CLI 2.0](/cli/azure/install-azure-cli) или используйте Azure Cloud Shell в браузере.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Настройка ведения журнала для базы данных Azure для PostgreSQL
Можно настроить на сервере доступ к журналам запросов и журналам ошибок. Журналы ошибок могут содержать сведения об автоматической очистке, подключениях и контрольных точках.
1. Включите ведение журнала.
2. Измените log\_statement и log\_min\_duration\_statement, чтобы включить ведение журнала запросов.
3. Измените срок хранения.

Дополнительные сведения см. в статье [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Получение списка журналов для базы данных Azure для сервера PostgreSQL
Чтобы получить список доступных файлов журналов для сервера, выполните команду [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list).

Вы можете получить список файлов журналов для сервера **mypgserver-20170401.postgres.database.azure.com** в группе ресурсов **myresourcegroup** и направить его в текстовый файл с именем **log\_files\_list.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Скачивание журналов с сервера в локальную среду
Команда [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) позволит загрузить отдельные файлы журналов для сервера. 

Этот пример скачивает в локальную среду определенный файл журнала для сервера **mypgserver-20170401.postgres.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>Дальнейшие действия
- Чтобы узнать больше о журналах сервера, изучите раздел [Журналы сервера в базе данных Azure для PostgreSQL](concepts-server-logs.md).
- Дополнительные сведения о параметрах сервера см. в статье [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).
