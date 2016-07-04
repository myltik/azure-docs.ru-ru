<properties
   pageTitle="Драйверы для хранилища данных SQL Azure | Microsoft Azure"
   description="Строки подключения и драйверы для хранилища данных SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>


# Драйверы для хранилища данных SQL Azure

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-connect-overview.md)
- [Аутентификация](sql-data-warehouse-authentication.md)
- [Драйверы](sql-data-warehouse-connection-strings.md)


Для подключения к хранилищу данных SQL можно использовать любой из следующих протоколов приложений.

- ADO.NET
- ODBC
- PHP
- JDBC 

Ниже приведены некоторые примеры строк подключения для каждого протокола. Кроме того, для настройки строки подключения можно использовать портал Azure. Для этого просто перейдите к базе данных на портале Azure. В разделе *Основные компоненты* щелкните *Показать строки подключения к базе данных*.

## Пример строки подключения по протоколу ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## Пример строки подключения по протоколу ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## Пример строки подключения по протоколу PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## Пример строки подключения по протоколу JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Дальнейшие действия

Чтобы приступить к отправке запросов к хранилищу данных с помощью Visual Studio и других приложений, ознакомьтесь с разделом [Query Azure SQL Data Warehouse (Visual Studio)][] (Выполнение запросов к хранилищу данных SQL Azure (Visual Studio)).

<!--Image references-->

<!--Azure.com references-->


<!--MSDN references-->

<!--Other references-->

<!---HONumber=AcomDC_0622_2016-->