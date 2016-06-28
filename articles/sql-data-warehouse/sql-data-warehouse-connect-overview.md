<properties
   pageTitle="Подключение к хранилищу данных SQL Azure | Microsoft Azure"
   description="Общие сведения о подключении к хранилищу данных SQL Azure."
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
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# Подключение к хранилищу данных SQL Azure

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-connect-overview.md)
- [Аутентификация](sql-data-warehouse-authentication.md)
- [Драйверы](sql-data-warehouse-connection-strings.md)

Общие сведения о подключении к хранилищу данных SQL Azure.

## Строка подключения на портале

Ваше хранилище данных SQL связано с сервером SQL Server в Azure. Для подключения вам потребуется полное доменное имя сервера (**имя\_сервера**.database.windows.net*).

Чтобы найти полное имя сервера, сделайте следующее.

1. Перейдите на [портал Azure][].
2. Щелкните **Базы данных SQL** и выберите базу данных, к которой необходимо подключиться. В этом примере используется образец базы данных AdventureWorksDW.
3. Найдите полное имя сервера.

    ![Полное имя сервера][1]

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

Этот запрос определяет текущий сеанс.

```sql
SELECT SESSION_ID()
;
```

Для просмотра всех запросов, выполняемых или недавно выполненных в вашем хранилище данных, можно использовать следующий пример. С его помощью представление будет создано, а затем запущено.

```sql
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

SELECT * FROM dbo.vSessionRequests;
```

## Дальнейшие действия

Сведения об отправке запросов к хранилищу данных с помощью Visual Studio и других приложений см. в статье [Запросы к хранилищу данных SQL Azure (Visual Studio)][].


<!--Arcticles-->

[Запросы к хранилищу данных SQL Azure (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[портал Azure]: https://portal.azure.com
[портала Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0622_2016-->