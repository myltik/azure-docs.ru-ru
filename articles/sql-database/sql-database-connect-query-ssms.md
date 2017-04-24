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
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a08fe566f4962684bc4aad220687e9cd36fc4abf
ms.lasthandoff: 04/17/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Подключайтесь к базе данных Azure SQL и создавайте запросы к ней с помощью SQL Server Management Studio

[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) — это средство управления, используемое для создания и администрирования ресурсов SQL Server с помощью пользовательского интерфейса или скриптов. В этом кратком руководстве показано, как использовать SSMS для подключения к базе данных Azure SQL, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных. 

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)

Перед началом работы установите последнюю версию [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите полное имя сервера для сервера базы данных SQL Azure на портале Azure. Используйте полное имя сервера, чтобы подключиться к серверу с помощью SQL Server Management Studio.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на рисунке ниже. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**.

   ![Сведения о подключении](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Если вы забыли данные для входа на сервер базы данных SQL Azure, перейдите к соответствующей странице, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль. 

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>Подключение к базе данных на логическом сервере базы данных SQL

Используйте SQL Server Management Studio для подключения к серверу базы данных SQL Azure. 

> [!IMPORTANT]
> Логический сервер базы данных SQL Azure прослушивает порт 1433. Чтобы подключиться к логическому серверу базы данных SQL Azure из среды, ограничиваемой корпоративным брандмауэром, этот порт должен быть открыт в брандмауэре.
>

1. Откройте среду SQL Server Management Studio.

2. В диалоговом окне **Подключение к серверу** введите следующие значения.
   - **Тип сервера** — укажите ядро СУБД.
   - **Имя сервера** — введите полное имя сервера, например **mynewserver20170313.database.windows.net**.
   - **Проверка подлинности** — укажите проверку подлинности SQL Server.
   - **Имя входа** — введите учетную запись администратора сервера.
   - **Пароль** — введите пароль учетной записи администратора сервера.

   ![Подключение к серверу](./media/sql-database-connect-query-ssms/connect.png)  

3. Щелкните **Параметры** в диалоговом окне **Подключение к серверу**. В разделе **Подключение к базе данных** введите **mySampleDatabase**, чтобы подключиться к этой базе данных.

   ![Подключение к базе данных на сервере](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Щелкните **Подключить**. Откроется окно обозревателя объектов в SSMS. 

   ![Подключение к серверу установлено](./media/sql-database-connect-query-ssms/connected.png)  

5. В обозревателе объектов разверните **базы данных**, затем выберите **mySampleDatabase**, чтобы просмотреть объекты в образце базы данных.

## <a name="query-data"></a>Запрос данных

С помощью инструкции Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) отправьте запрос на получение данных из базы данных SQL Azure.

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

С помощью инструкции Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) вставьте данные в базу данных SQL Azure.

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

С помощью инструкции Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) обновите данные в базе данных SQL Azure.

1. В окне запроса замените предыдущий запрос следующим содержимым:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. На панели инструментов щелкните **Выполнить**, чтобы обновить указанную строку в таблице Product.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Удаление данных

С помощью инструкции Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) удалите данные из базы данных SQL Azure.

1. В окне запроса замените предыдущий запрос следующим содержимым:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. На панели инструментов щелкните **Выполнить**, чтобы удалить указанную строку из таблицы Product.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о решении SSMS см. в статье об [использовании SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- См. дополнительные сведения о [подключении и создании запросов с помощью Visual Studio Code](sql-database-connect-query-vscode.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью .NET](sql-database-connect-query-dotnet.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью PHP](sql-database-connect-query-php.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Node.js](sql-database-connect-query-nodejs.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Java](sql-database-connect-query-java.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Python](sql-database-connect-query-python.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Ruby](sql-database-connect-query-ruby.md).

