---
title: Использование Node.js для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать Node.js для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 0d1cdd40264ff76b0175c861b3084ed7e7b62a31
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="use-nodejs-to-query-an-azure-sql-database"></a>Использование Node.js для создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [Node.js](https://nodejs.org/en/) для создания программы, которая подключается к Базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.

## <a name="prerequisites"></a>предварительным требованиям

Ниже указаны требования для работы с этим кратким руководством.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Правило брандмауэра уровня сервера](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) для общедоступного IP-адреса компьютера, на котором выполняются действия из этого краткого руководства.

- Убедитесь, что установлен компонент Node.js и связанное программное обеспечение для вашей операционной системы.
    - **Mac OS.** Установите Homebrew и Node.js, а затем драйвер ODBC и SQLCMD. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
    - **Ubuntu.** Установите Node.js, а затем драйвер ODBC и SQLCMD. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
    - **Windows.** Установите Chocolatey и Node.js, а затем драйвер ODBC и SQLCMD. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="sql-server-connection-information"></a>Сведения о подключении SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Необходимо настроить правила брандмауэра для общедоступного IP-адреса компьютера, на котором выполняются действия из этого руководства. Если вы используете другой компьютер или имеете другой общедоступный IP-адрес, создайте [правила брандмауэра на уровне сервера с помощью портала Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="create-a-nodejs-project"></a>Создание проекта Node.js

Откройте командную строку и создайте папку с именем *sqltest*. Перейдите к созданной папке и выполните следующую команду:

    
    npm init -y
    npm install tedious
    npm install async
    

## <a name="insert-code-to-query-sql-database"></a>Вставка кода для отправки запроса к базе данных SQL

1. В среде разработки или в предпочитаемом текстовом редакторе создайте файл **sqltest.js**.

2. Замените содержимое следующим кодом и добавьте соответствующие значения для сервера, базы данных, пользователя и пароля.

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
                   }
               );
    
        request.on('row', function(columns) {
           columns.forEach(function(column) {
               console.log("%s\t%s", column.metadata.colName, column.value);
            });
                });
        connection.execSql(request);
      }
```

## <a name="run-the-code"></a>Выполнение кода

1. В командной строке выполните следующие команды:

   ```js
   node sqltest.js
   ```

2. Убедитесь, что возвращены первые 20 строк, а затем закройте окно приложения.

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь со сведениями о [драйвере Microsoft Node.js для SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/).
- Узнайте, как [подключиться и отправить запрос к базе данных SQL Azure с помощью .NET Core](sql-database-connect-query-dotnet-core.md) в Windows, Linux и Mac OS.  
- См. дополнительные сведения о [начале работы с .NET Core в Windows, Linux и Mac OS с помощью командной строки](/dotnet/core/tutorials/using-with-xplat-cli).
- Узнайте, как спроектировать первую базу данных SQL с помощью [SSMS](sql-database-design-first-database.md) или [.NET](sql-database-design-first-database-csharp.md).
- Узнайте, как [подключиться и создавать запросы с помощью SSMS](sql-database-connect-query-ssms.md).
- Узнайте, как [подключиться и создавать запросы помощью Visual Studio Code](sql-database-connect-query-vscode.md).

