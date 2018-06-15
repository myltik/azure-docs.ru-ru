---
title: Руководство. Использование эластичных запросов для хранилища данных SQL Azure | Документация Майкрософт
description: Для задач этого руководства используется функция эластичных запросов. Она позволяет обращаться к хранилищу данных SQL Azure из службы "База данных SQL Azure".
services: sql-data-warehouse
author: hirokib
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: a31f035b5ec086a046028956c4a9c0de0d6a313d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526198"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Руководство. Доступ к данным в хранилище данных SQL Azure из службы "База данных SQL Azure" с помощью эластичных запросов

Для задач этого руководства используется функция эластичных запросов. Она позволяет обращаться к хранилищу данных SQL Azure из службы "База данных SQL Azure". 

## <a name="prerequisites-for-the-tutorial"></a>Предварительные требования для прохождения этого учебника

Для работы с этим руководством необходимо:

1. Установить SQL Server Management Studio (SSMS).
2. Создать сервер SQL Azure с базой данных и хранилищем данных.
3. Настроить правил брандмауэра для доступа к серверу SQL Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Настройка подключения между экземплярами хранилища данных SQL и базы данных SQL 

1. Используя SSMS или другой клиент запросов, откройте новый запрос к базе данных **master** на логическом сервере.

2. Создайте имя для входа и пользователя для подключения к хранилищу данных SQL и базе данных SQL.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Используя SSMS или другой клиент запросов, откройте новый запрос к **экземпляру хранилища данных SQL** на логическом сервере.

4. В этом экземпляре хранилища данных создайте пользователя с именем для входа, созданным на шаге 2.

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Предоставьте пользователю из шага 4 разрешения на операции, которые должна выполнить база данных SQL. В этом примере предоставляется разрешение только на операцию SELECT для определенной схемы. Тем самым показывается, как можно ограничить запросы из базы данных SQL определенной областью. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Используя SSMS или другой клиент запросов, откройте новый запрос к **экземпляру базы данных SQL** на логическом сервере.

7. Если вы еще не сделали этого, создайте главный ключ. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Создайте учетные данные для базы данных, используя учетные данные, созданные на шаге 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Создайте внешний источник данных, указывающий на экземпляр хранилища данных.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Теперь можно создать внешние таблицы, которые ссылаются на этот источник внешних данных. Запросы с использованием этих таблиц отправляются в экземпляр хранилища данных для обработки, а затем возвращаются в экземпляр базы данных.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Отправка эластичного запроса из базы данных SQL к хранилищу данных SQL

Далее мы создадим в экземпляре хранилища данных таблицу с несколькими значениями. Затем мы покажем, как настроить внешнюю таблицу для отправки запроса к экземпляру хранилища данных из экземпляра базы данных.

1. Используя SSMS или другой клиент запросов, откройте новый запрос к **хранилищу данных SQL** на логическом сервере.

2. Отправьте приведенный ниже запрос, чтобы создать таблицу **OrdersInformation** в экземпляре хранилища данных.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Используя SSMS или другой клиент запросов, откройте новый запрос к **базе данных SQL** на логическом сервере.

4. Отправьте приведенные ниже запрос, чтобы создать определение внешней таблицы, указывающей на таблицу **OrdersInformation** в экземпляре хранилища данных.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Обратите внимание на то, что теперь в **экземпляре базы данных SQL** есть определение внешней таблицы.

   ![Определение внешней таблицы для эластичного запроса](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Отправьте приведенный ниже запрос, который запрашивает данные из экземпляра хранилища данных. Он должен вернуть пять значений, которые были вставлены на шаге 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Обратите внимание на то, что хотя значений мало, этот запрос выполняется достаточно долго. При использовании эластичного запроса с хранилищем данных следует учесть дополнительные затраты на обработку запроса и передачу данных по сети. Используйте удаленное выполнение эластичных запросов, если вам важнее вычислительная мощность, в не задержка.

Поздравляем, вы ознакомились с основами эластичных запросов. 

## <a name="next-steps"></a>Дополнительная информация
См. [рекомендации по использованию эластичных запросов в хранилище данных SQL Azure](how-to-use-elastic-query-with-sql-data-warehouse.md).