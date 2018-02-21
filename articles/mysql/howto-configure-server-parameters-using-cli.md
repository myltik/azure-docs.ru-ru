---
title: "Настройка параметров службы в базе данных Azure для MySQL | Документация Майкрософт"
description: "В этой статье описывается настройка параметров службы в базе данных Azure для MySQL с помощью служебной программы командной строки CLI Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 5983bbf6fac9c3cddda19f6a11d2fe2b18177160
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2017
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Настройка параметров конфигурации сервера с помощью Azure CLI
С помощью служебной программы командной строки (Azure CLI) можно вывести список параметров конфигурации для сервера базы данных Azure для MySQL, а также отобразить и обновить их. Только подмножество конфигураций ядра предоставляется на уровне сервера и может быть изменено. 

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [Сервер базы данных Azure для MySQL.](quickstart-create-mysql-server-database-using-azure-cli.md)
- Служебная программа командной строки [Azure CLI 2.0](/cli/azure/install-azure-cli) (или используйте Azure Cloud Shell в браузере).

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Получение списка параметров конфигурации сервера для базы данных Azure для сервера MySQL
Чтобы перечислить все изменяемые параметры на сервере и их значения, выполните команду [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list).

Например, можно вывести список параметров конфигурации сервера для сервера **myserver4demo.mysql.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```
Определение каждого из перечисленных параметров см. в разделе ссылок MySQL на странице[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) (Системные переменные сервера).

## <a name="show-server-configuration-parameter-details"></a>Отображение сведений о параметре конфигурации сервера
Чтобы отобразить сведения об определенном параметре конфигурации для сервера, выполните команду [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show).

Этот пример отображает сведения параметра конфигурации сервера **slow\_query\_log** для сервера **myserver4demo.mysql.database.azure.com** в группе ресурсов **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server myserver4demo
```
## <a name="modify-a-server-configuration-parameter-value"></a>Изменение значения параметра конфигурации сервера
Вы также можете изменить значение определенного параметра конфигурации сервера. При этом обновляется базовое значение конфигурации для ядра СУБД сервера MySQL. Чтобы обновить конфигурацию, выполните команду [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set). 

Чтобы обновить параметры конфигурации **slow\_query\_log** для сервера **myserver4demo.mysql.database.azure.com** в группе ресурсов **myresourcegroup**, используйте следующую команду:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
```
Если вы хотите сбросить значение параметра конфигурации, нужно просто опустить необязательный параметр `--value`, и служба применит значение по умолчанию. В приведенном выше примере это может выглядеть следующим образом.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo
```
Этот код выполняет сброс конфигурации **slow\_query\_log** к значению по умолчанию **OFF**. 

## <a name="next-steps"></a>Дополнительная информация
- Настройка [параметров сервера на портале Azure](howto-server-parameters.md)
