---
title: "Создание правил брандмауэра базы данных Azure для PostgreSQL и управление ими с помощью Azure CLI | Документация Майкрософт"
description: "В этой статье описывается, как создать базу данных Azure для правил брандмауэра PostgreSQL и управлять ею с помощью интерфейса командной строки Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 7d51932cb7c0b5b959e3fcf38f651933b619cbda
ms.contentlocale: ru-ru
ms.lasthandoff: 06/16/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Создание правил брандмауэра базы данных Azure для PostgreSQL и управление ими с помощью Azure CLI
Правила брандмауэра уровня сервера позволяют администраторам управлять доступом к серверу базы данных Azure для PostgreSQL с указанного IP-адреса или диапазона IP-адресов. С помощью удобных команд Azure CLI можно создавать, обновлять, удалять, выводить список и отображать правила брандмауэра для управления сервером. Обзор брандмауэров базы данных Azure для PostgreSQL приведен в разделе [Правила брандмауэра сервера базы данных Azure для PostgreSQL](concepts-firewall-rules.md).

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер и база данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md);
- Установите служебную программу командной строки [Azure CLI 2.0](/cli/azure/install-azure-cli) или используйте Azure Cloud Shell в браузере.

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Настройка правил брандмауэра сервера базы данных Azure для PostgreSQL
Команда [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) используется для настройки правил брандмауэра.

## <a name="list-firewall-rules"></a>Вывод списка правил брандмауэра 
Чтобы вывести список существующих правил брандмауэра сервера на сервере, запустите команду [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#list).
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
Выходные данные будут содержать правила, если они имеются, в используемом по умолчанию формате JSON. Вы можете использовать `--output table`, чтобы получить выходные данные в виде более удобной таблицы.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Создание правила брандмауэра
Чтобы создать правило брандмауэра на сервере, выполните команду [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create). 

Этот пример разрешает доступ к серверу **mypgserver-20170401.postgres.database.azure.com** всем диапазонам IP-адресов.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Чтобы разрешить доступ с отдельного IP-адреса, укажите одинаковый начальный и конечный IP-адрес, как показано в этом примере.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
При успешном выполнении команды ее выходные данные будут содержать сведения о созданном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.

## <a name="update-firewall-rule"></a>Обновление правила брандмауэра 
Обновите существующее правило брандмауэра на сервере с помощью команды [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#update). В качестве входных данных укажите имя существующего правила брандмауэра, которое нужно обновить, а также атрибуты начального и конечного IP-адресов.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
При успешном выполнении команды ее выходные данные будут содержать сведения об обновленном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.
> [!NOTE]
> Если правило брандмауэра не существует, оно будет создано командой update.

## <a name="show-firewall-rule-details"></a>Отображение сведений о правиле брандмауэра
Вы также можете отобразить сведения о существующем правиле брандмауэра, выполнив команду [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#show).
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
При успешном выполнении команды ее выходные данные будут содержать сведения об указанном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.

## <a name="delete-firewall-rule"></a>Удаление правила брандмауэра
Чтобы отменить доступ для диапазона IP-адресов с сервера, удалите существующее правило брандмауэра, выполнив команду [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#delete). Укажите имя существующего правила брандмауэра.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
При успешном выполнении выходные данные отсутствуют. В случае сбоя возвращается сообщение об ошибке.

## <a name="next-steps"></a>Дальнейшие действия
- Аналогичным образом можно использовать веб-браузер, чтобы [создать правила брандмауэра базы данных Azure для PostgreSQL и управлять ими с помощью портала Azure](howto-manage-firewall-using-portal.md).
- Узнайте больше о [правилах брандмауэра сервера базы данных Azure для PostgreSQL](concepts-firewall-rules.md).
- Справка по подключению к серверу базы данных Azure для PostgreSQL доступна в разделе [Библиотеки подключений для базы данных Azure для PostgreSQL](concepts-connection-libraries.md).

