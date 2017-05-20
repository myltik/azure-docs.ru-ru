---
title: "Настройка параметров службы в базе данных Azure для PostgreSQL | Документация Майкрософт"
description: "Описывается, как настроить параметры службы в базе данных Azure для PostgreSQL."
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
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Настройка параметров конфигурации сервера с помощью Azure CLI
С помощью интерфейса командной строки (Azure CLI) можно вывести список параметров конфигурации для сервера Azure PostgreSQL, а также отобразить и обновить их. Однако только подмножество конфигураций ядра предоставляется на уровне сервера и может быть изменено. 

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер и база данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md);
- установленная программа командной строки [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>Вывод списка параметров конфигурации сервера Azure PostgreSQL
Чтобы получить список всех изменяемых параметров на сервере Azure PostgreSQL и их значения, выполните команду **az postgres server configuration**, как показано ниже.
> az postgres server configuration list --resource-group <resource group name> --server <server name>

Например, можно вывести список параметров конфигурации сервера Azure PostgreSQL **mypgserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>Отображение сведений о параметре конфигурации сервера
Чтобы отобразить сведения об определенном параметре конфигурации сервера Azure PostgreSQL, выполните команду **az postgres server configuration**.
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
Например, можно вывести сведения о параметре конфигурации **log\_min\_messages** сервера Azure PostgreSQL **mypgserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>Изменение значения параметра конфигурации сервера
Можно изменить значение определенных параметров конфигурации сервера Azure PostgreSQL. При этом обновляется базовое значение конфигурации для ядра сервера PostgreSQL. Чтобы изменить значение конфигурации, выполните следующую команду **az postgres server configuration**. 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
Например, можно изменить параметр конфигурации **log\_min\_messages** сервера Azure PostgreSQL **mypgserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
Если вы хотите сбросить значение параметра конфигурации, нужно просто убрать необязательный параметр --value, и служба применит значение по умолчанию. В приведенном выше примере это может выглядеть следующим образом.
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
Данная команда восстановит значение по умолчанию WARNING для параметра конфигурации log\_min\_messages. Дополнительные сведения о конфигурации сервера и допустимых значениях приведены в документации PostgreSQL по [конфигурации сервера](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Дальнейшие действия
- Чтобы настроить журналы сервера и получать к ним доступ, ознакомьтесь с разделом [Журналы сервера в базе данных Azure для PostgreSQL](concepts-server-logs.md).
