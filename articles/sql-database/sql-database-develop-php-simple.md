---
title: "Подключение к базе данных SQL с помощью приложения PHP в операционной системе Windows | Документация Майкрософт"
description: "В этой статье представлен пример программы PHP, которая подключается к базе данных SQL Azure из клиентской ОС Windows, и показаны ссылки на программные компоненты, необходимые для клиента."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/13/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 94fa09526683582bc017213d0ad9455f31cb22ae
ms.openlocfilehash: fba66e9d41daa2df34fbb3ffd8c92e664eaa560e
ms.lasthandoff: 02/21/2017


---

# <a name="connect-to-sql-database-by-using-php"></a>Подключение к Базе данных SQL с помощью PHP
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

В этом разделе описывается подключение и запрос к базе данных SQL Azure с использованием PHP. Данный пример можно выполнять в Windows или Linux. 


## <a name="step-1-create-a-sql-database"></a>Шаг 1. Создание базы данных SQL
Чтобы узнать, как создать образец базы данных, перейдите на страницу [Начало работы](sql-database-get-started.md) .  Очень важно соблюдать инструкции руководства во время создания **шаблона базы данных AdventureWorks**. Приведенные ниже примеры работают только со **схемой AdventureWorks**. После создания базы данных не забудьте разрешить доступ к вашему IP-адресу, включив правила брандмауэра. Это описано в разделе [Начало работы](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Шаг 2. Настройка среды разработки

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать сценарий Python. Введите следующие команды, чтобы установить **драйвер Microsoft ODBC для Linux**, **pdo_sqlsrv** и **sqlsrv**. Драйвер Microsoft PHP для SQL Server использует драйвер Microsoft ODBC для Linux для подключения к базам данных SQL.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql unixodbc-dev gcc g++ build-essential
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

## <a name="step-3-run-sample-code"></a>Шаг 3. Выполнение примера кода
Создайте файл **sql_sample.php** и вставьте в него следующий код. Это можно выполнить с помощью командной строки:

```
php sql_sample.php
```

### <a name="connect-to-your-sql-database"></a>Подключение к базе данных SQL
Функция [sqlsrv connect](http://php.net/manual/en/function.sqlsrv-connect.php) используется для подключения к Базе данных SQL.

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
    );
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
?>
```

### <a name="execute-an-sql-select-statement"></a>Выполнение оператора SQL SELECT
Функция [sqlsrv_query](http://php.net/manual/en/function.sqlsrv-query.php) может использоваться для извлечения результирующего набора из запроса к базе данных SQL. 

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";  
$getProducts = sqlsrv_query($conn, $tsql);  
if ($getProducts == FALSE)  
    die(FormatErrors(sqlsrv_errors()));  
$productCount = 0;  
while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))  
{  
    echo($row['CompanyName']);  
    echo("<br/>");  
    $productCount++;  
}  
sqlsrv_free_stmt($getProducts);  
sqlsrv_close($conn);    
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Вставка строки, передача параметров и получение созданного первичного ключа
Для получения автоматически созданных значений [первичного ключа](https://msdn.microsoft.com/library/ms186775.aspx) в базе данных SQL можно использовать свойство [IDENTITY](https://msdn.microsoft.com/library/ff878058.aspx) и объект [SEQUENCE](https://msdn.microsoft.com/library/ms179610.aspx). 


```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";  
//Insert query  
$insertReview = sqlsrv_query($conn, $tsql);  
if($insertReview == FALSE)  
    die(FormatErrors( sqlsrv_errors()));  
echo "Product Key inserted is :";  
while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))  
{     
    echo($row['ProductID']);  
}  
sqlsrv_free_stmt($insertReview);  
sqlsrv_close($conn);  
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```


## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с разделом [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* Получите дополнительные сведения о [драйвере Microsoft PHP для SQL Server](https://github.com/Microsoft/msphpsql/)
* [Сообщите о проблеме или задайте вопросы](https://github.com/Microsoft/msphpsql/issues).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Вы можете изучить все [возможности Базы данных SQL](https://azure.microsoft.com/services/sql-database/)

