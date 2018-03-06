---
title: "Настройка журналов сервера для PostgreSQL и получение к ним доступа с помощью Azure CLI"
description: "В этой статье описывается настройка журналов сервера в базе данных Azure для PostgreSQL с помощью командной строки CLI Azure и получение доступа к этим журналам."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e12a8907b641b4591ed5ff9fdd5d8458eb75525e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Настройка журналов сервера и получение к ним доступа с помощью Azure CLI
Вы можете скачать журналы ошибок сервера PostgreSQL с помощью интерфейса командной строки (Azure CLI). Однако доступ к журналам транзакций не поддерживается. 

## <a name="prerequisites"></a>предварительным требованиям
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

Вы можете получить список файлов журналов для сервера **mydemoserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup** и направить его в текстовый файл с именем **log\_files\_list.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Скачивание журналов с сервера в локальную среду
Команда [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) позволит загрузить отдельные файлы журналов для сервера. 

Этот пример скачивает в локальную среду определенный файл журнала для сервера **mydemoserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Дополнительная информация
- Чтобы узнать больше о журналах сервера, изучите раздел [Журналы сервера в базе данных Azure для PostgreSQL](concepts-server-logs.md).
- Дополнительные сведения о параметрах сервера см. в статье [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).
