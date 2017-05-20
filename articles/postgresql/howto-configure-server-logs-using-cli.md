---
title: "Настройка журналов сервера для PostgreSQL и получение к ним доступа с помощью Azure CLI | Документация Майкрософт"
description: "Описывается настройка журналов сервера в базе данных Azure для PostgreSQL и получение доступа к ним."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dc047e6d5194035586931b3cacc4d5cd07f6cfc2
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Настройка журналов сервера и получение к ним доступа с помощью Azure CLI
Чтобы просмотреть и скачать журналы ошибок сервера Azure PostgreSQL, можно воспользоваться интерфейсом командной строки Azure CLI. Однако доступ к журналам транзакций не поддерживается. 

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md);
- установленная программа командной строки [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Настройка ведения журнала для базы данных Azure для PostgreSQL
Можно настроить на сервере доступ к журналам запросов и журналам ошибок. Журналы ошибок могут содержать сведения об автоматической очистке, подключениях и контрольных точках.
1. Включите ведение журнала.
2. Измените log\_statement и log\_min\_duration\_statement, чтобы включить ведение журнала запросов.
3. Измените срок хранения.

Дополнительные сведения см. в разделе [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-postgresql-server"></a>Вывод списка журналов для сервера Azure PostgreSQL
Чтобы получить список доступных журналов для сервера, выполните команду **az postgres server-logs**, как показано в следующем примере.
```azurecli
az postgres server-logs list --resource-group <resource group name> --server <server name> [ --file-last-written --filename-contains --max-file-size ]
```
Например, можно вывести список файлов журналов для сервера Azure PostgreSQL **mypgserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup** и направить его в текстовый файл **log\_files\_list.txt. **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup** --server **mypgserver** > log\_files\_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Скачивание журналов с сервера в локальную среду
Можно также скачать отдельные файлы журнала для сервера Azure PostgreSQL. 
```azurecli
az postgres server-logs download –name <log file name> --resource-group <resource group name> --server <server name>
```
Этот пример скачивает в локальную среду определенный файл журнала для сервера Azure PostgreSQL **mypgserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli
az postgres server-logs download --name 20170414-mypgserver-postgresql.log --resource-group **myresourcegroup** --server **mypgserver**
```
## <a name="next-steps"></a>Дальнейшие действия
- Чтобы узнать больше о журналах сервера, изучите раздел [Журналы сервера в базе данных Azure для PostgreSQL](concepts-server-logs.md).
- Дополнительные сведения о параметрах сервера см. в статье [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).
