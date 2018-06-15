---
title: Настройка параметров службы "База данных Azure для MySQL"
description: В этой статье описывается настройка параметров службы в базе данных Azure для MySQL с помощью служебной программы командной строки CLI Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 4c04cb77513ec070edce739aa0a49447dc915a1b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265220"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Настройка параметров конфигурации сервера с помощью Azure CLI
С помощью служебной программы командной строки (Azure CLI) можно вывести список параметров конфигурации для сервера базы данных Azure для MySQL, а также отобразить и обновить их. Только подмножество конфигураций ядра предоставляется на уровне сервера и может быть изменено. 

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [Сервер базы данных Azure для MySQL.](quickstart-create-mysql-server-database-using-azure-cli.md)
- Служебная программа командной строки [Azure CLI 2.0](/cli/azure/install-azure-cli) (или используйте Azure Cloud Shell в браузере).

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Получение списка параметров конфигурации сервера для базы данных Azure для сервера MySQL
Чтобы перечислить все изменяемые параметры на сервере и их значения, выполните команду [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list).

Например, можно вывести список параметров конфигурации сервера для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Определение каждого из перечисленных параметров см. в разделе ссылок MySQL на странице[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) (Системные переменные сервера).

## <a name="show-server-configuration-parameter-details"></a>Отображение сведений о параметре конфигурации сервера
Чтобы отобразить сведения об определенном параметре конфигурации для сервера, выполните команду [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show).

Этот пример отображает сведения параметра конфигурации сервера **slow\_query\_log** для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Изменение значения параметра конфигурации сервера
Вы также можете изменить значение определенного параметра конфигурации сервера. При этом обновляется базовое значение конфигурации для ядра СУБД сервера MySQL. Чтобы обновить конфигурацию, выполните команду [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set). 

Чтобы обновить параметры конфигурации **slow\_query\_log** для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup**, используйте следующую команду:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Если вы хотите сбросить значение параметра конфигурации, нужно просто опустить необязательный параметр `--value`, и служба применит значение по умолчанию. В приведенном выше примере это может выглядеть следующим образом.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Этот код выполняет сброс конфигурации **slow\_query\_log** к значению по умолчанию **OFF**. 

## <a name="next-steps"></a>Дополнительная информация
- Настройка [параметров сервера на портале Azure](howto-server-parameters.md)