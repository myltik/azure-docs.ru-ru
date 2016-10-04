<properties
   pageTitle="Подключение к хранилищу данных SQL Azure | Microsoft Azure"
   description="Как найти имя сервера и строку подключения к хранилищу данных SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# Подключение к хранилищу данных SQL Azure

Эта статья поможет вам установить первое подключение к хранилищу данных SQL.

## Поиск имени сервера

Чтобы подключиться к хранилищу данных SQL, прежде всего нужно знать, как найти имя вашего сервера. Например, имя сервера в следующем примере — sample.database.windows.net. Чтобы найти полное имя сервера, сделайте следующее.

1. Перейдите на [портал Azure][].
2. Щелкните **Базы данных SQL**.
3. Щелкните базу данных, к которой вы хотите подключиться.
4. Найдите полное имя сервера.

    ![Полное имя сервера][1]

## Поддерживаемые драйверы и строки подключения

Хранилище данных SQL Azure поддерживает драйверы [ADO.NET][], [ODBC][], [PHP][] и [JDBC][]. Щелкните один из указанных типов драйверов для получения информации об обновлениях и документации. Чтобы автоматически создать строку подключения используемого драйвера на портале Azure, щелкните **Показать строки подключения к базам данных** на странице из предыдущего примера. Ниже приведены примеры синтаксиса строк подключения для каждого драйвера.

> [AZURE.NOTE] Рекомендуем задать время ожидания подключения, равное 300 секундам, чтобы подключение могло выдерживать короткие периоды недоступности.

### Пример строки подключения ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Пример строки подключения ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Пример строки подключения PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Пример строки подключения JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Параметры подключения

Хранилище данных SQL стандартизирует некоторые параметры при установке подключения и создании объектов. Такие параметры нельзя переопределить. К ним относятся следующие:

| Параметр базы данных | Значение |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ВКЛ |
| [QUOTED\_IDENTIFIERS][] | ВКЛ |
| [DATEFORMAT][] | мдг |
| [DATEFIRST][] | 7 |

## Дальнейшие действия

Чтобы подключиться и отправить запрос с помощью Visual Studio, см. инструкции в статье [Запросы к хранилищу данных SQL Azure (Visual Studio)][]. Подробные сведения о способах проверки подлинности см. в статье [Аутентификация в хранилище данных SQL Azure][].

<!--Articles-->
[Запросы к хранилищу данных SQL Azure (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Аутентификация в хранилище данных SQL Azure]: ./sql-data-warehouse-authentication.md

.<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

.<!--Other-->
[портал Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0928_2016-->