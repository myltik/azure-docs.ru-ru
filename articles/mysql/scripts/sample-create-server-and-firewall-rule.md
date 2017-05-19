---
title: "Примеры Azure CLI для создания отдельного сервера базы данных Azure для MySQL и настройка правила брандмауэра | Документация Майкрософт"
description: "Этот пример скрипта CLI создает сервер базы данных Azure для MySQL и настраивает правило брандмауэра на уровне сервера."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fa961f123dddc2e6243d1efbd1d72b994179eb79
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Создание сервера MySQL и настройка правила брандмауэра с помощью Azure CLI
Этот пример скрипта CLI создает сервер базы данных Azure для MySQL и настраивает правило брандмауэра на уровне сервера. После успешного выполнения скрипта доступ к серверу MySQL можно получить из всех служб Azure, а также по настроенному IP-адресу.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresource \
--server mysqlserver4demo \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>Очистка развертывания
 После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.
```azurecli
az group delete --name myresource
```
## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| **Команда** | **Примечания** |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| az mysql server create | Создает сервер MySQL, на котором размещены базы данных. |
| az mysql server firewall create | Создает правило брандмауэра, чтобы разрешить доступ к серверу и размещенным на нем базам данных по введенному диапазону IP-адресов. |
| [az group delete](https://docs.microsoft.com/en-us/cli/azure/resource#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия
[Примеры Azure CLI для базы данных Azure для MySQL](../sample-scripts-azure-cli.md)

