---
title: "Создание базы данных Azure для PostgreSQL с помощью Azure CLI | Документация Майкрософт"
description: "Краткое руководство по созданию базы данных Azure и управлению ею для сервера PostgreSQL с помощью Azure CLI (интерфейса командной строки)."
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 11/27/2017
ms.custom: mvc
ms.openlocfilehash: a2036174b32f6b910b6934d05fd7439f60427947
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Создание базы данных Azure для PostgreSQL с помощью Azure CLI
База данных Azure для PostgreSQL — это управляемая служба, которая позволяет вам запускать, администрировать и масштабировать высокодоступные базы данных PostgreSQL в облаке. Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом кратком руководстве описывается создание базы данных Azure для сервера PostgreSQL в [группе ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) с помощью Azure CLI.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Если интерфейс командной строки выполняется локально, необходимо войти учетную запись, выполнив команду [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in).
```azurecli-interactive
az login
```

Если вы используете несколько подписок, выберите соответствующую, в которой за ресурс будет взиматься плата. Выберите конкретный идентификатор подписки вашей учетной записи, выполнив команду [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myresourcegroup` в расположении `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера базы данных Azure для PostgreSQL

Создайте [сервер базы данных Azure для PostgreSQL](overview.md), выполнив команду [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). Сервер содержит группу баз данных, которыми можно управлять как группой. 

В примере ниже в группе ресурсов `myresourcegroup` показано создание сервера с именем `mypgserver-20170401` для входа администратора сервера `mylogin`. Имя сервера сопоставляется с DNS-именем, и поэтому оно должно быть глобально уникальным в рамках Azure. Замените `<server_admin_password>` собственным значением.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> Указанные здесь учетные данные и пароль администратора сервера понадобятся позже в этом руководстве, чтобы войти на сервер и в его базу данных. Запомните или запишите эту информацию для последующего использования.

По умолчанию на сервере создается база данных **postgres**. База данных [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) — это база данных по умолчанию, предназначенная для использования пользователями, служебными программами и сторонними приложениями. 


## <a name="configure-a-server-level-firewall-rule"></a>Настройка правила брандмауэра на уровне сервера

Создайте правило брандмауэра на уровне сервера Azure PostgreSQL, выполнив команду [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) или [PgAdmin](https://www.pgadmin.org/), подключаться к серверу через брандмауэр службы Azure PostgreSQL. 

Вы можете задать правило брандмауэра, охватывающее диапазон IP-адресов, которые могут подключаться из сети. В указанном ниже примере для создания правила брандмауэра `AllowAllIps` для диапазона IP-адресов используется команда [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). Чтобы открыть все IP-адреса, используйте 0.0.0.0 как начальный IP-адрес, а 255.255.255.255 — как конечный.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Сервер PostgreSQL Azure обменивается данными через порт 5432. При попытках подключения из корпоративной сети может оказаться, что исходящий трафик через порт 5432 запрещен сетевым брандмауэром. Чтобы вы могли подключиться к серверу базы данных SQL Azure, ваш ИТ-отдел должен открыть порт 5432.

## <a name="get-the-connection-information"></a>Получение сведений о подключении

Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

Результаты выводятся в формате JSON. Запишите **administratorLogin** и **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>Подключение к базе данных PostgreSQL с помощью psql

Если на клиентском компьютере установлено PostgreSQL, вы можете использовать локальный экземпляр [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), чтобы подключиться к серверу Azure PostgreSQL. Теперь подключимся к серверу Azure PostgreSQL с помощью служебной программы командной строки psql.

1. Чтобы подключиться к серверу базы данных Azure для PostgreSQL, выполните следующую команду psql:
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Например, следующая команда устанавливает подключение к базе данных по умолчанию **postgres** на сервере PostgreSQL **mypgserver-20170401.postgres.database.azure.com**, используя учетные данные для доступа. Введите `<server_admin_password>`, указанный при появлении запроса на ввод пароля.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  Подключившись к серверу, создайте пустую базу данных с помощью командной строки.
```sql
CREATE DATABASE mypgsqldb;
```

3.  Чтобы подключиться к созданной базе данных **mypgsqldb**, выполните следующую команду в командной строке:
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Подключение к базе данных PostgreSQL с помощью pgAdmin

Чтобы подключиться к серверу PostgreSQL Azure с помощью инструмента графического интерфейса пользователя _pgAdmin_, сделайте следующее:
1.  Запустите приложение _pgAdmin_ на клиентском компьютере. Вы можете установить _pgAdmin_ с помощью http://www.pgadmin.org/.
2.  В меню **Быстрые ссылки** выберите **Добавление сервера**.
3.  На вкладке **Общие** в диалоговом окне **Создать сервер** введите уникальное понятное имя для сервера. Например, **Azure PostgreSQL Server**.
 ![Инструмент pgAdmin. Создание сервера](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  В диалоговом окне **Создать сервер** на вкладке **Подключение** введите следующее:
    - Полное имя сервера (например, **mypgserver-20170401.postgres.database.azure.com**) в поле **Host Name/Address** (Имя или адрес узла). 
    - Введите порт 5432 в поле **Порт**. 
    - Введите **учетные данные администратора сервера для входа (user@mypgserver)** , полученные ранее в рамках этого краткого руководства, а также пароль, который вы вводили при создании сервера. Используйте поля — **Имя пользователя** и **Пароль** соответственно.
    - Выберите для **режима SSL** значение **Запросить**. По умолчанию все серверы PostgreSQL Azure создаются с включенным применением SSL. Чтобы выключить применение SSL, ознакомьтесь со статьей [о настройке подключения SSL в базе данных Azure для PostgreSQL](./concepts-ssl-connection-security.md).

    ![pgAdmin. Создание сервера](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  Щелкните **Сохранить**.
6.  В левой области браузера разверните **Группы серверов**. Выберите свой сервер **Azure PostgreSQL Server**.
7.  Выберите раздел **Сервер** для подключения, а затем выберите в нем **Базы данных**. 
8.  Щелкните правой кнопкой мыши **Базы данных**, чтобы создать базу данных.
9.  Выберите имя базы данных **mypgsqldb** и ее владельца, используя учетные данные администратора сервера для входа **mylogin**.
10. Нажмите кнопку **Сохранить**, чтобы создать пустую базу данных.
11. В разделе **Браузер** разверните группу **Серверы**. Разверните созданный сервер, чтобы увидеть базу данных **mypgsqldb**.
 ![pgAdmin. Создание базы данных](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>Очистка ресурсов

Очистите все ресурсы, созданные при работе с кратким руководством, удалив [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Другие краткие руководства в этой коллекции созданы на основе этого документа. Если вы планируете продолжать работу с этими краткими руководствами, не удаляйте созданные ресурсы. Если вы не планируете продолжать работу, удалите все созданные с помощью Azure CLI ресурсы, выполнив следующие действия.

```azurecli-interactive
az group delete --name myresourcegroup
```

Если вы хотите удалить созданный сервер, выполните команду [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./howto-migrate-using-export-and-import.md)
