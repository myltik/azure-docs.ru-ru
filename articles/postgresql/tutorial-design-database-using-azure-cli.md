---
title: "Проектирование первой базы данных Azure для PostgreSQL с помощью Azure CLI | Документация Майкрософт"
description: "Это руководство содержит сведения о проектировании первой базы данных Azure для PostgreSQL с помощью Azure CLI."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fbac141243f0f36aab1a2ef9f28b4e107fd2d390
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Проектирование первой базы данных Azure для PostgreSQL с помощью Azure CLI 
Из этого руководства вы узнаете, как с помощью Azure CLI (интерфейса командной строки) и других служебных программ выполнять следующие операции:
> [!div class="checklist"]
> * создание базы данных Azure для PostgreSQL;
> * настройка брандмауэра сервера;
> * использование служебной программы [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) для создания базы данных;
> * Загрузка примера данных
> * Запрос данных
> * Обновление данных
> * восстановление данных.

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.
```azurecli
az login
```

Если вы используете несколько подписок, выберите соответствующую подписку, в которой находится ресурс либо в которой за него взимается плата. Выберите конкретный идентификатор подписки вашей учетной записи, выполнив команду [az account set](/cli/azure/account#set).
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myresourcegroup` в расположении `westus`.
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера базы данных Azure для PostgreSQL

Создайте [сервер базы данных Azure для PostgreSQL](overview.md), выполнив команду **az postgres server create**. Сервер содержит группу баз данных, которыми можно управлять как группой. 

В указанном ниже примере в группе ресурсов `myresourcegroup` создается сервер `mypgserver-20170401` с именем для входа администратора сервера `mylogin`. Имя сервера сопоставляется с DNS-именем, и поэтому оно должно быть глобально уникальным в рамках Azure. Замените `<server_admin_password>` собственным значением.
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> Указанные здесь учетные данные и пароль администратора сервера понадобятся позже в этом руководстве, чтобы войти на сервер и в его базу данных. Запомните или запишите эту информацию для последующего использования.

По умолчанию на сервере создается база данных **postgres**. База данных [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) — это база данных по умолчанию, предназначенная для использования пользователями, служебными программами и сторонними приложениями. 


## <a name="configure-a-server-level-firewall-rule"></a>Настройка правила брандмауэра на уровне сервера

Создайте правило брандмауэра на уровне сервера Azure PostgreSQL, выполнив команду **az postgres server firewall-rule create**. Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) или [PgAdmin](https://www.pgadmin.org/), подключаться к серверу через брандмауэр службы Azure PostgreSQL. 

Вы можете задать правило брандмауэра, охватывающее диапазон IP-адресов, которые могут подключаться из сети. В указанном ниже примере для создания правила брандмауэра `AllowAllIps` для диапазона IP-адресов используется команда **az postgres server firewall-rule create**. Чтобы открыть все IP-адреса, используйте 0.0.0.0 как начальный IP-адрес, а 255.255.255.255 — как конечный.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Сервер Azure PostgreSQL обменивается данными через порт 5432. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 5432 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу базы данных SQL Azure. Для этого ваш ИТ-отдел должен открыть порт 5432.
>

## <a name="get-the-connection-information"></a>Получение сведений о подключении

Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа.
```azurecli
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

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Подключение к базе данных Azure для PostgreSQL с помощью psql

Если на клиентском компьютере установлено PostgreSQL, вы можете использовать локальный экземпляр [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), чтобы подключиться к серверу Azure PostgreSQL. Теперь подключимся к серверу Azure PostgreSQL с помощью служебной программы командной строки psql.

1. Чтобы подключиться к серверу базы данных Azure для PostgreSQL, выполните следующую команду psql:
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Например, следующая команда устанавливает подключение к базе данных по умолчанию **postgres** на сервере PostgreSQL **mypgserver-20170401.postgres.database.azure.com**, используя учетные данные для доступа. Введите `<server_admin_password>`, указанный при появлении запроса на ввод пароля.
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  Подключившись к серверу, создайте пустую базу данных с помощью командной строки.
```bash
CREATE DATABASE mypgsqldb;
```

3.  Чтобы подключиться к созданной базе данных **mypgsqldb**, выполните следующую команду в командной строке:
```bash
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Создание таблиц в базе данных
Теперь, когда вы знаете, как подключиться к базе данных Azure для PostgreSQL, рассмотрим, как выполнить некоторые основные задачи.

Сначала можно создать таблицу и заполнить ее некоторыми данными. Давайте создадим таблицу, с помощью которой можно отслеживать данные инвентаризации.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Вы можете просмотреть созданную таблицу в списке таблиц, введя:
```bash
\dt
```

## <a name="load-data-into-the-tables"></a>Загрузка данных в таблицу
Теперь, когда таблица создана, мы можем вставить в нее данные. Чтобы вставить некоторые строки данных, в открытом окне командной строки выполните следующий запрос:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Итак, в созданной ранее таблице содержится две строки данных.

## <a name="query-and-update-the-data-in-the-tables"></a>Запрос и обновление данных в таблицах
Чтобы извлечь сведения из таблицы базы данных, выполните приведенный ниже запрос. 
```sql
SELECT * FROM inventory;
```

Вы можете также обновить данные в таблицах, выполнив следующую команду:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

При извлечении данных строка будет обновляться соответствующим образом.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Восстановление базы данных до предыдущей точки во времени
Представьте, что вы случайно удалили таблицу. Восстановить ее будет не просто. База данных Azure для PostgreSQL позволяет вернуться в любой момент времени (в течение последних 7 дней (для уровня "Базовый") и 35 дней (для уровня "Стандартный")) и восстановить данные на определенный момент времени на новом сервере. Вы можете восстановить удаленные данные с помощью нового сервера. Указанные ниже шаги позволяют восстановить сервер до точки во времени, когда была создана таблица.

Чтобы восстановить данные, вам потребуются следующие сведения:
- **Точка восстановления.** Выберите время до того момента, когда был изменен сервер. Значение точки восстановления должно быть не меньше значения самой старой резервной копии исходной базы данных.
- **Целевой сервер.** Укажите новое имя сервера, который нужно восстановить.
- **Исходный сервер.** Укажите имя сервера, из которого требуется выполнить восстановление.
- **Расположение.** Вы не сможете выбрать регион, по умолчанию он совпадает с исходным сервером.
- **Ценовая категория.** Это значение нельзя изменить при восстановлении сервера. Она совпадает с ценовой категорией исходного сервера. 

```azurecli
az postgres server restore --resource-group myResourceGroup --name mypgserver-20170401-restored --restore-point-in-time "2017-04-13 03:10" --source-server-name mypgserver-20170401
```

Выполните предыдущую команду, чтобы восстановить сервер до [точки во времени](./howto-restore-server-portal.md) перед удалением таблиц. Восстановление сервера до точки во времени создает копию сервера, где расположен исходный сервер, с состоянием на момент указанной точки во времени (в пределах срока хранения, установленного для вашего [уровня служб](./concepts-service-tiers.md)).

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как с помощью Azure CLI (интерфейса командной строки) и других служебных программ выполнить следующие операции:
> [!div class="checklist"]
> * создание базы данных Azure для PostgreSQL;
> * настройка брандмауэра сервера;
> * использование служебной программы [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) для создания базы данных;
> * Загрузка примера данных
> * Запрос данных
> * Обновление данных
> * восстановление данных.

Чтобы узнать, как выполнять похожие задачи с помощью портала Azure, см. сведения в руководстве [Проектирование первой базы данных Azure для PostgreSQL с помощью портала Azure](tutorial-design-database-using-azure-portal.md).

