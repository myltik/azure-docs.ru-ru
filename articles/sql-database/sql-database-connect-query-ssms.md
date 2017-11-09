---
title: "SSMS: подключение и запрос данных в базе данных SQL Azure | Документация Майкрософт"
description: "Узнайте, как подключиться к базе данных SQL в Azure с помощью SQL Server Management Studio (SSMS). Затем выполните инструкции Transact-SQL (T-SQL) для запроса и изменения данных."
metacanonical: 
keywords: "подключение к базе данных sql, sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/26/2017
ms.author: carlrab
ms.openlocfilehash: f67ba82c3aaf312662096964b7b7942efa108607
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Подключайтесь к базе данных Azure SQL и создавайте запросы к ней с помощью SQL Server Management Studio

[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) — это интегрированная среда для управления любой инфраструктурой SQL, от SQL Server до базы данных SQL для Microsoft Windows. В этом кратком руководстве показано, как использовать SSMS для подключения к базе данных Azure SQL, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных. 

## <a name="prerequisites"></a>Предварительные требования

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)
- [Создание базы данных с помощью PowerShell](sql-database-get-started-powershell.md)

Перед началом работы установите последнюю версию [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="sql-server-connection-information"></a>Сведения о подключении SQL Server

Получите сведения о подключении, необходимые для подключения к базе данных SQL Azure. Вам понадобится следующее: полное имя сервера, имя базы данных и сведения для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на рисунке ниже. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**.

   ![Сведения о подключении](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер базы данных SQL Azure, перейдите к соответствующей странице, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль. 

## <a name="connect-to-your-database"></a>Подключение к базе данных

Используйте SQL Server Management Studio для подключения к серверу базы данных SQL Azure. 

> [!IMPORTANT]
> Логический сервер базы данных SQL Azure прослушивает порт 1433. Чтобы подключиться к логическому серверу базы данных SQL Azure из среды, ограничиваемой корпоративным брандмауэром, этот порт должен быть открыт в брандмауэре.
>

1. Откройте среду SQL Server Management Studio.

2. В диалоговом окне **Подключение к серверу** введите следующие значения.

   | Настройка       | Рекомендуемое значение | Описание | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Тип сервера** | Ядро СУБД | Это обязательное значение. |
   | **Server name** (Имя сервера) | Полное имя сервера | Имя должно быть примерно таким: **mynewserver20170313.database.windows.net**. |
   | **Аутентификация** | проверка подлинности SQL Server | В рамках работы с этим руководством мы настроили только один тип проверки подлинности — проверку подлинности SQL. |
   | **Имя входа** | Учетная запись администратора сервера | Это учетная запись, указанная при создании сервера. |
   | **Пароль** | Пароль учетной записи администратора сервера | Это пароль, указанный при создании сервера. |

   ![Подключение к серверу](./media/sql-database-connect-query-ssms/connect.png)  

3. Щелкните **Параметры** в диалоговом окне **Подключение к серверу**. В разделе **Подключение к базе данных** введите **mySampleDatabase**, чтобы подключиться к этой базе данных.

   ![Подключение к базе данных на сервере](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Щелкните **Подключить**. Откроется окно обозревателя объектов в SSMS. 

   ![Подключение к серверу установлено](./media/sql-database-connect-query-ssms/connected.png)  

5. В обозревателе объектов разверните **базы данных**, затем выберите **mySampleDatabase**, чтобы просмотреть объекты в образце базы данных.

## <a name="query-data"></a>Запрос данных

Используйте следующий код, чтобы запросить 20 основных продуктов из категории с помощью инструкции [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL.

1. В обозревателе объектов щелкните правой кнопкой мыши **mySampleDatabase** и выберите пункт **Новый запрос**. Откроется пустое окно запроса, подключенное к базе данных.
2. В окне запроса введите следующее:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. На панели инструментов щелкните **Выполнить**, чтобы получить данные из таблиц Product и ProductCategory.

    ![query](./media/sql-database-connect-query-ssms/query.png)

## <a name="insert-data"></a>Добавление данных

Используйте указанный ниже код, чтобы вставить новый продукт в таблицу SalesLT.Product с помощью инструкции [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL.

1. В окне запроса замените предыдущий запрос следующим содержимым:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. На панели инструментов щелкните **Выполнить**, чтобы вставить новую строку в таблице Product.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Обновление данных

Используйте следующий код, чтобы обновить новый продукт, добавленный ранее, с помощью инструкции [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL.

1. В окне запроса замените предыдущий запрос следующим содержимым:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. На панели инструментов щелкните **Выполнить**, чтобы обновить указанную строку в таблице Product.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Удаление данных

Используйте следующий код, чтобы удалить новый продукт, добавленный ранее, с помощью инструкции [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL.

1. В окне запроса замените предыдущий запрос следующим содержимым:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. На панели инструментов щелкните **Выполнить**, чтобы удалить указанную строку из таблицы Product.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о [создании и администрировании серверов и баз данных с помощью Transact-SQL](sql-database-servers-databases.md).
- Дополнительные сведения о решении SSMS см. в статье об [использовании SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Чтобы подключиться и выполнять запросы при помощи портала Azure, прочитайте статью [Портал Azure. Использование редактора SQL-запросов для подключения к данным и их запроса](sql-database-connect-query-portal.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Visual Studio Code](sql-database-connect-query-vscode.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью .NET](sql-database-connect-query-dotnet.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью PHP](sql-database-connect-query-php.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Node.js](sql-database-connect-query-nodejs.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Java](sql-database-connect-query-java.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Python](sql-database-connect-query-python.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Ruby](sql-database-connect-query-ruby.md).
