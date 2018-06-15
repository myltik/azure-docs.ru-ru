---
title: Создание пользователей на сервере службы "База данных Azure для PostgreSQL"
description: В этой статье описывается создание учетных записей пользователей для взаимодействия с сервером службы "База данных Azure для PostgreSQL".
services: postgresql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 87a73929185112190d5dd6698e014db225ebc08e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
ms.locfileid: "29574187"
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Создание пользователей на сервере службы "База данных Azure для PostgreSQL" 
В этой статье описывается создание пользователей на сервере службы "База данных Azure для PostgreSQL".

## <a name="the-server-admin-account"></a>Учетная запись администратора сервера
При создании первой службы "База данных Azure для PostgreSQL" вы указали имя пользователя и пароль администратора сервера. Дополнительные сведения и пошаговые инструкции см. в [этом кратком руководстве](quickstart-create-server-database-portal.md). Так как имя пользователя администратора сервера настраивает пользователь, можно найти выбранное имя на портале Azure.

Сервер службы "База данных Azure для PostgreSQL" создается с тремя определенными по умолчанию ролями. Эти роли можно просмотреть, выполнив команду: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- пользователь-администратор сервера

Пользователь-администратор сервера является членом роли azure_pg_admin. Но учетная запись администратора сервера не входит в роль azure_superuser. Так как эта служба является управляемой службой PaaS, только Майкрософт входит в роль суперпользователя. 

Подсистема PostgreSQL использует права для управления доступом к объектам базы данных, как описано в [документации по продукту PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). В службе "База данных Azure для PostgreSQL" пользователю-администратору сервера предоставляются следующие права: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

Учетную запись пользователя администратора сервера можно использовать для создания дополнительных пользователей и включения этих пользователей в роль azure_pg_admin. Кроме того, учетная запись администратора сервера может использоваться для создания менее привилегированных пользователей и ролей, имеющих доступ к отдельным базам данных и схемам.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Как создать дополнительных администраторов в службе "База данных Azure для PostgreSQL"
1. Получите сведения о подключении и имя пользователя администратора.
   Чтобы подключиться к серверу базы данных, вам потребуются учетные данные администратора для входа и полное имя сервера. Вы можете легко найти данные для входа на странице **Обзор** сервера или на странице **Свойства** на портале Azure. 

2. Используйте учетную запись и пароль администратора для подключения к серверу базы данных. Используйте предпочитаемое клиентское средство, например pgAdmin или psql.
   Если вы не знаете, как подключиться, см. раздел [Подключение к базе данных PostgreSQL с помощью psql в Cloud Shell](./quickstart-create-server-database-portal.md#connect-to-the-postgresql-database-by-using-psql-in-cloud-shell)

3. Измените и выполните следующий код SQL. Замените новым именем пользователя значение заполнителя <new_user>, а заполнитель пароля замените надежным паролем. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Как создать пользователей базы данных в службе "База данных Azure для PostgreSQL"

1. Получите сведения о подключении и имя пользователя администратора.
   Чтобы подключиться к серверу базы данных, вам потребуются учетные данные администратора для входа и полное имя сервера. Вы можете легко найти данные для входа на странице **Обзор** сервера или на странице **Свойства** на портале Azure. 

2. Используйте учетную запись и пароль администратора для подключения к серверу базы данных. Используйте предпочитаемое клиентское средство, например pgAdmin или psql.

3. Измените и выполните следующий код SQL. Замените значение заполнителя `<db_user>` новым именем пользователя, а значение заполнителя `<newdb>` — именем базы данных. Замените заполнитель пароля надежным паролем. 

   Этот код SQL создает базу данных testdb для примера. Затем он создает пользователя в службе PostgreSQL и предоставляет ему привилегии для подключения к новой базе данных для этого пользователя. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE testdb TO <db_user>;
   ```

4. При использовании учетной записи администратора вам может потребоваться предоставить дополнительные права для безопасности объектов в базе данных. См. [документацию PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) для получения дальнейших сведений о ролях базы данных и правах доступа. Например:  
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Чтобы войти на сервер, укажите эту базу данных и введите новое имя пользователя и пароль. В этом примере показана командная строка psql. После ввода этой команды вам будет предложено ввести пароль для имени пользователя. Укажите собственные имя сервера, имя базы данных и имя пользователя.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Дополнительная информация
Откройте брандмауэр для IP-адресов компьютеров новых пользователей, чтобы обеспечить их подключение. Для этого ознакомьтесь с разделом [Создание правил брандмауэра базы данных Azure для PostgreSQL и управление ими с помощью портала Azure](howto-manage-firewall-using-portal.md) или [Azure CLI](howto-manage-firewall-using-cli.md).

Чтобы получить дополнительные сведения об управлении учетными записями пользователей, ознакомьтесь с [ролями базы данных и привилегиями](https://www.postgresql.org/docs/current/static/user-manag.html), [синтаксисом GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) и [привилегиями](https://www.postgresql.org/docs/current/static/ddl-priv.html) в документации по продукту PostgreSQL.
