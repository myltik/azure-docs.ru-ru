<properties
   pageTitle="Подключение к хранилищу данных SQL | Microsoft Azure"
   description="Рекомендации по подключению к хранилищу данных SQL для разработки решений."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Подключение к хранилищу данных SQL 
Для подключения к хранилищу данных SQL необходимо передавать учетные данные безопасности для проверки подлинности. После установки соединения вы можете обнаружить, что некоторые параметры подключения настраиваются при установке сеанса запроса.

В этой статье рассматриваются следующие аспекты подключения к хранилищу данных SQL:

- Аутентификация
- Параметры подключения
- Сеансы и идентификаторы запросов


## Аутентификация
Для подключения к хранилищу данных SQL необходимо предоставить следующие сведения:

- Полное имя сервера 
- Тип проверки подлинности SQL
- Имя пользователя 
- Пароль
- База данных по умолчанию (необязательно)

Следует отметить, что пользователи должны проходить проверку подлинности с использованием проверки подлинности SQL. Доверенная проверка подлинности в настоящее время не поддерживается.

По умолчанию устанавливается подключение к основной, а не к пользовательской базе данных. Для подключения к пользовательской базе данных можно выполнить одно из следующих действий:

1. Укажите базу данных по умолчанию при регистрации сервера в обозревателе объектов SQL Server в SSDT или в строке подключения приложения. Например, включите параметр InitialCatalog для подключения ODBC.
2. Выделите пользовательскую базу данных перед созданием сеанса в SSDT.

> [AZURE.NOTE]Инструкции по подключению к хранилищу данных SQL с SSDT см. во вводной статье о [подключении и запросах][].

Важно отметить, что использовать оператор **USE<your DB>** (Transact-SQL) для изменения базы данных, к которой осуществляется подключение, нельзя.

## Протоколы подключения к приложениям
Для подключения к хранилищу данных SQL можно использовать любой из следующих протоколов:

- ADO.NET
- ODBC
- PHP
- JDBC

### Пример строки подключения по протоколу ADO.NET

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Пример строки подключения по протоколу ODBC

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Пример строки подключения по протоколу PHP

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Пример строки подключения по протоколу JDBC

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Параметры подключения
Хранилище данных SQL стандартизирует некоторые параметры при установке соединения и создании объектов. Переопределить эти параметры нельзя.

| Параметр базы данных | Значение |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | ВКЛ |
| QUOTED\_IDENTIFIERS | ВКЛ |
| NO\_COUNT | ВЫКЛ |
| DATEFORMAT | мдг |
| DATEFIRST | 7 |
| Сортировка базы данных | SQL\_Latin1\_General\_CP1\_CI\_AS |

## Сеансы и запросы
Как только соединение будет установлено, а сеанс запущен, можно приступать к записи и отправке запросов в хранилище данных SQL.

Каждый запрос получает один или несколько идентификаторов. Все запросы, отправленные через одно и то же соединение, являются частью одного сеанса и получают одинаковый идентификатор сеанса.

Однако в связи с тем, что хранилище данных SQL является распределенной системой MPP, идентификаторы сеанса и запроса выглядят здесь немного иначе, чем в SQL Server.

Сеансы и запросы логически представляются соответствующими идентификаторами.
	
| Идентификатор | Пример значения |
| :--------- | :------------ |
| Идентификатор сеанса | SID123456 |
| Идентификатор запроса | QID123456 |

Обратите внимание, что идентификатор сеанса имеет префикс SID (сокр. от Session ID), а идентификатор сеанса —префикс QID (сокр. от Query ID).

Эти сведения помогут вам идентифицировать запрос при контроле его выполнения. Отслеживать выполнение запроса можно с помощью [портала Azure] или динамических административных представлений.

Для идентификации текущего сеанса используйте следующую функцию:

```
SELECT SESSION_ID()
;
```

Для просмотра всех запросов, выполняемых или недавно выполненных в вашем хранилище данных, можно использовать запрос следующего вида:

```
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;
```

Обратите внимание, что этот запрос внедрен в представление, — это позволит включить его в решение. Для просмотра результатов необходимо будет создать и выполнить это представление.

## Дальнейшие действия
Установив подключение, можно приступить к созданию таблиц. Более подробную информацию см. в статье о [разработке таблиц].

<!--Image references-->

<!--Azure.com references-->
[подключении и запросах]: ./sql-data-warehouse-get-started-connect.md
[разработке таблиц]: ./sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=AcomDC_0107_2016-->