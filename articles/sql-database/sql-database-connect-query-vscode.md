---
title: "VS Code: подключение и запрос данных в базе данных SQL Azure | Документация Майкрософт"
description: "Сведения о подключении к базе данных SQL в Azure с помощью Visual Studio Code и выполнении инструкций Transact-SQL (T-SQL) для запроса и изменения данных."
metacanonical: 
keywords: "подключение к базе данных SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: fd5cb0d45d0955b7e4c471dc5ccecac65ad7400a
ms.lasthandoff: 03/29/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>База данных SQL Azure: подключение и запрос данных с помощью Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/docs) — это графический редактор кода для Linux, macOS и Windows с поддержкой расширений. С помощью Visual Studio Code и [расширения mssql](https://aka.ms/mssql-marketplace) вы можете подключаться и отправлять запросы к базе данных SQL Azure. В этом кратком руководстве объясняется, как с помощью Visual Studio Code подключаться к базе данных SQL Azure, а также выполнять запросы и использовать инструкции вставки, обновления и удаления.

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)

Сначала установите последнюю версию[Visual Studio Code](https://code.visualstudio.com/Download) и загрузите [расширение mssql](https://aka.ms/mssql-marketplace). Руководство по установке расширения mssql см. [здесь](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code). 

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите полное имя сервера для сервера базы данных SQL Azure на портале Azure. Полное имя сервера используется для подключения к серверу с помощью Visual Studio Code.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На портале Azure на странице вашей базы данных в области **Основное** найдите и скопируйте **имя сервера**. Оно будет использоваться далее в этом руководстве.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>Выбор режима языка SQL

В Visual Studio Code укажите для режима языка значение **SQL**, чтобы активировать команды mssql и T-SQL IntelliSense.

1. Откройте новое окно Visual Studio Code. 

2. Нажмите клавиши **CTRL+K, M**, введите **SQL** и нажмите клавишу **ВВОД**, чтобы указать SQL в качестве режима языка. 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Подключение к серверу

С помощью Visual Studio Code подключитесь к серверу базы данных SQL Azure.

1. В VS Code нажмите клавиши **CTRL+SHIFT+P** (или **F1**), чтобы открыть палитру команд.

2. Введите **sqlcon** и нажмите клавишу **ВВОД**.

3. Нажмите кнопку **Yes** (Да), чтобы выбрать язык **SQL**.

4. Нажмите клавишу **ВВОД**, чтобы выбрать **Create Connection Profile** (Создать профиль подключения). Для экземпляра SQL Server будет создан профиль подключения.

5. Следуйте инструкциям на экране, чтобы указать свойства для нового профиля подключения. Укажите все значения и нажмите клавишу **ВВОД** для продолжения. 

   В таблице ниже описаны свойства профиля подключения.

   | Настройка | Описание |
   |-----|-----|
   | **Server name** (Имя сервера) | Введите полное имя сервера, например **mynewserver20170313.database.windows.net**. |
   | **Database name** (Имя базы данных) | Введите имя базы данных, например **mySampleDatabase**. |
   | **Authentication** (Аутентификация) | Выберите вход SQL. |
   | **User name** (Имя пользователя) | Укажите учетную запись администратора сервера. |
   | **Password (SQL Login)** (Пароль для входа в SQL) | Введите пароль учетной записи администратора сервера. | 
   | **Save Password?** (Сохранить пароль?) | Выберите **Yes** (Да) или **No** (Нет). |
   | **[Optional] Enter a name for this profile** ([Необязательно] Введите имя для этого профиля) | Введите имя профиля подключения, например **mySampleDatabase**. 

6. Нажмите клавишу **ESC**, чтобы закрыть сообщение с информацией о том, что профиль создан и подключен.

7. Проверьте состояние подключения в строке состояния.

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>Запрос данных

С помощью инструкции Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) отправьте запрос на получение данных из базы данных SQL Azure.

1. В окне **редактора** введите в пустое окно запроса следующий запрос:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Нажмите клавиши **CTRL+SHIFT+E**, чтобы получить данные из таблиц Product и ProductCategory.

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>Добавление данных

С помощью инструкции Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) вставьте данные в базу данных SQL Azure.

1. В окне **редактора** удалите предыдущий запрос и введите следующий:

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

3. Нажмите клавиши **CTRL+SHIFT+E**, чтобы вставить новую строку в таблицу Product.

## <a name="update-data"></a>Обновление данных

С помощью инструкции Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) обновите данные в базе данных SQL Azure.

1.  В окне **редактора** удалите предыдущий запрос и введите следующий:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. Нажмите клавиши **CTRL+SHIFT+E**, чтобы обновить указанную строку в таблице Product.

## <a name="delete-data"></a>Удаление данных

С помощью инструкции Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) удалите данные из базы данных SQL Azure.

1. В окне **редактора** удалите предыдущий запрос и введите следующий:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. Нажмите клавиши **CTRL+SHIFT+E**, чтобы удалить указанную строку из таблицы Product.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительную информацию см. в документации по [Visual Studio Code](https://code.visualstudio.com/docs).
- Сведения об отправке запросов и изменении данных с помощью SQL Server Management Studio см. в статье [Использование среды SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).

