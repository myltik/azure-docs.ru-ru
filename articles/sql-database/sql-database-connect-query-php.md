---
title: "Подключение к базе данных SQL Azure с помощью PHP | Документация Майкрософт"
description: "В этой статье представлен пример кода PHP, который можно использовать для подключения и выполнения запросов к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 693bfdf7bb3d9d72fbd6ac42734ca261af7b492d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>База данных SQL Azure: подключение и запрос данных с помощью PHP

В этом кратком руководстве показано, как, используя [PHP](http://php.net/manual/en/intro-whatis.php), подключиться к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных на платформах Mac OS, Ubuntu Linux и Windows.

## <a name="prerequisites"></a>Предварительные требования

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)
- [Создание базы данных с помощью PowerShell](sql-database-get-started-powershell.md)

## <a name="install-php-and-database-communications-software"></a>Установка программного обеспечения связи PHP и базы данных

В этом разделе предполагается, что у вас уже есть опыт разработки на PHP и вы только начали работу с Базой данных SQL Azure. Если вы новичок в разработке на PHP, перейдите на страницу [создания приложения с помощью SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/), выберите язык **PHP** и операционную систему.

### <a name="mac-os"></a>**Mac OS**
Чтобы установить **brew**, **драйвер Microsoft ODBC для Mac** и **драйверы Microsoft PHP для SQL Server**, откройте терминал и введите следующие команды: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Чтобы установить **драйвер Microsoft ODBC для Linux** и **драйверы Microsoft PHP для SQL Server**, введите следующие команды:

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- Установка PHP 7.1.1 (x64) [с помощью установщика веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx?lang=) 
- Установите [драйвер Microsoft ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339). 
- Скачайте непотокобезопасные библиотеки DLL для [драйвера Microsoft PHP для SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) и поместите двоичные файлы в папку PHP\v7.x\ext.
- Затем измените файл php.ini (C:\Program Files\PHP\v7.1\php.ini), добавив в него ссылку на библиотеку DLL. Например:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

На этом этапе библиотеки DLL должны быть зарегистрированы с помощью PHP.

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите сведения о подключении, необходимые для подключения к базе данных SQL Azure. Вам понадобится следующее: полное имя сервера, имя базы данных и сведения для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на рисунке ниже. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер, перейдите на соответствующую страницу, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.     
    
## <a name="select-data"></a>Выбор данных
Используйте следующий код, чтобы запросить 20 наиболее популярных продуктов по категории, используя функцию [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) в инструкции Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Функция sqlsrv_query используется для извлечения результирующего набора из запроса к базе данных SQL. Эта функция принимает запрос и возвращает результирующий набор, по которому может быть выполнена итерация с использованием [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
     FROM [SalesLT].[ProductCategory] pc
     JOIN [SalesLT].[Product] p
     ON pc.productcategoryid = p.productcategoryid";
$getResults= sqlsrv_query($conn, $tsql);
echo ("Reading data from table" . PHP_EOL);
if ($getResults == FALSE)
    echo (sqlsrv_errors());
while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
}
sqlsrv_free_stmt($getResults);
?>
```


## <a name="insert-data"></a>Добавление данных
Используйте указанный ниже код, чтобы вставить новый продукт в таблицу SalesLT.Product с помощью функции [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) и инструкции Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>Обновление данных
Используйте указанный ниже код, чтобы обновить данные в базе данных SQL Azure, используя функцию [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) и инструкцию Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>Удаление данных
Используйте следующий код, чтобы удалить новый продукт, добавленный ранее, с помощью функции [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) и инструкции Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Замените параметры server, database, username и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>Дальнейшие действия
- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/) (Драйверы Microsoft PHP для SQL Server)
- [Сообщите о проблеме или задайте вопросы](https://github.com/Microsoft/msphpsql/issues)


