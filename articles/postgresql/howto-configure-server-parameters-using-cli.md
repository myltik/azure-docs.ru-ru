---
title: "Настройка параметров службы в базе данных Azure для PostgreSQL | Документация Майкрософт"
description: "В этой статье описывается настройка параметров службы в базе данных Azure для PostgreSQL с помощью командной строки CLI Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 96c5ab5caa4fea178a3108947fa858d395650e08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Настройка параметров конфигурации сервера с помощью Azure CLI
С помощью интерфейса командной строки (Azure CLI) можно вывести список параметров конфигурации для сервера Azure PostgreSQL, а также отобразить и обновить их. Только подмножество конфигураций ядра предоставляется на уровне сервера и может быть изменено. 

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимо сделать следующее:
- создайте базу данных и сервер базы данных Azure для PostgreSQL, выполнив инструкции из раздела [Создание базы данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md);
- установите интерфейс командной строки [Azure CLI 2.0](/cli/azure/install-azure-cli) на компьютере или используйте [Azure Cloud Shell](../cloud-shell/overview.md) на портале Azure с помощью браузера.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Получение списка параметров конфигурации сервера для базы данных Azure для сервера PostgreSQL
Чтобы перечислить все изменяемые параметры на сервере и их значения, выполните команду [az postgres server configuration list](/cli/azure/postgres/server/configuration#list).

Например, можно вывести список параметров конфигурации сервера для сервера **mypgserver-20170401.postgres.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>Отображение сведений о параметре конфигурации сервера
Чтобы отобразить сведения об определенном параметре конфигурации для сервера, выполните команду [az postgres server configuration show](/cli/azure/postgres/server/configuration#show).

Этот пример отображает сведения параметра конфигурации сервера **log\_min\_messages** для сервера **mypgserver-20170401.postgres.database.azure.com** в группе ресурсов **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>Изменение значения параметра конфигурации сервера
Вы также можете изменить значение определенного параметра конфигурации сервера. При этом обновляется базовое значение конфигурации для ядра сервера. Чтобы обновить конфигурацию, выполните команду [az postgres server configuration set](/cli/azure/postgres/server/configuration#set). 

Обновите параметр конфигурации сервера **log\_min\_messages** для сервера **mypgserver-20170401.postgres.database.azure.com** в группе ресурсов **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
Если вы хотите сбросить значение параметра конфигурации, нужно просто убрать необязательный параметр `--value`, и служба применит значение по умолчанию. В приведенном выше примере это может выглядеть следующим образом.
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
Данная команда восстановит значение по умолчанию **WARNING** для параметра конфигурации **log\_min\_messages**. Дополнительные сведения о конфигурации сервера и допустимых значениях приведены в документации PostgreSQL по [конфигурации сервера](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Дальнейшие действия
- Чтобы настроить журналы сервера и получать к ним доступ, ознакомьтесь с разделом [Журналы сервера в базе данных Azure для PostgreSQL](concepts-server-logs.md).
