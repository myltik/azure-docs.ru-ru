---
title: "Подключение к базе данных SQL Azure с помощью Node.js | Документация Майкрософт"
description: "В этой статье представлен пример кода Node.js, который можно использовать для подключения и выполнения запросов к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/17/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 4de9eb8f55bfda8b223417f5c1ed4e71b0f063c6
ms.lasthandoff: 04/21/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>База данных SQL Azure: подключение и запрос данных с помощью Node.js

В этом кратком руководстве показано, как подключиться к базе данных SQL Azure с помощью [Node.js](https://nodejs.org/en/), а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных на платформах Windows, Ubuntu Linux и Mac.

Начальной точкой в этом руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)

## <a name="install-nodejs"></a>Установка Node.js 

В этом разделе предполагается, что у вас уже есть опыт разработки на Node.js и вы только начали работу с Базой данных SQL Azure. Если вы новичок в разработке на Node.js, перейдите на страницу [создания приложения с помощью SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/), выберите язык **Node.js** и операционную систему.

### <a name="mac-os"></a>**Mac OS**
Чтобы установить **brew** (простой в использовании диспетчер пакетов для Mac OS X) и **Node.js**, выполните следующую команду:

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Чтобы установить **Node.js** и **npm** (диспетчер пакетов для Node.js), выполните следующую команду:

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
Войдите на [страницу скачиваемых файлов Node.js](https://nodejs.org/en/download/) и выберите нужный установщик Windows.


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>Установка драйвера tedious SQL Server для Node.js
Для Node.js мы рекомендуем использовать драйвер **[tedious](https://github.com/tediousjs/tedious)**. Tedious — это решение с открытым исходным кодом, усовершенствованное корпорацией Майкрософт для приложений Node.js на любой платформе. В рамках этого руководства вам потребуется пустой каталог для хранения кода и устанавливаемых зависимостей `npm`.

Чтобы установить драйвер **tedious**, в каталоге выполните следующую команду:

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите сведения о подключении, необходимые для подключения к базе данных SQL Azure. Вам понадобится следующее: полное имя сервера, имя базы данных и сведения для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на рисунке ниже. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер базы данных SQL Azure, перейдите к соответствующей странице, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.
    
## <a name="select-data"></a>Выбор данных

Используйте указанный ниже код, чтобы запросить 20 наиболее популярных продуктов по категории из базы данных SQL Azure. Сначала импортируйте классы Connect и Request из библиотеки драйвера tedious. Затем создайте объект конфигурации и замените переменные **username**, **password**, **server** и **database** значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. Создайте объект `Connection`, используя указанный объект `config`. После этого определите обратный вызов для события `connect` объекта `connection`, чтобы выполнить функцию `queryDatabase()`.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
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

## <a name="insert-data-into-the-database"></a>Вставка данных в базу данных
Используйте указанный ниже код, чтобы вставить новый продукт в таблицу SalesLT.Product с помощью функции `insertIntoDatabase()` и инструкции Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Замените переменные **username**, **password**, **server** и **database** значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>Обновление данных в базе данных
Используйте следующий код, чтобы удалить новый продукт, добавленный ранее, с помощью функции `updateInDatabase()` и инструкции Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Замените переменные **username**, **password**, **server** и **database** значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. В этом примере используется имя продукта, добавленное в предыдущем примере.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>Удаление данных из базы данных
Чтобы удалить данные из базы данных, используйте указанный ниже код. Замените переменные **username**, **password**, **server** и **database** значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. На этот раз в функции `deleteFromDatabase()` используйте инструкцию **DELETE**. В этом примере также используется имя продукта, добавленное в предыдущем примере.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>Дальнейшие действия

- Получите дополнительные сведения о [драйвере Microsoft Node.js для SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- Дополнительные сведения о подключении к базе данных SQL с помощью SQL Server Management Studio и выполнении запроса к ней см. [здесь](sql-database-connect-query-ssms.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Visual Studio Code](sql-database-connect-query-vscode.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью .NET](sql-database-connect-query-dotnet.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью PHP](sql-database-connect-query-php.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Java](sql-database-connect-query-java.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Python](sql-database-connect-query-python.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Ruby](sql-database-connect-query-ruby.md).


