<properties
   pageTitle="Мониторинг базы данных SQL Azure с помощью динамических представлений управления | Microsoft Azure"
   description="Узнайте, как выявлять и диагностировать распространенные проблемы производительности с помощью динамических представлений управления для мониторинга Базы данных SQL Microsoft Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# Мониторинг базы данных SQL Azure с помощью динамических представлений управления

База данных SQL Microsoft Azure предлагает ряд динамических представлений управления для диагностирования проблем производительности, которые могут быть вызваны заблокированными или долго выполняющимися запросами, узкими местами ресурсов, непродуманным планом запросов и т. д. Этот раздел содержит информацию о том, как выявлять распространенные проблемы производительности с помощью динамических административных представлений.

База данных SQL частично поддерживает три категории динамических представлений управления:

- динамические представления управления, относящиеся к базам данных;
- динамические представления управления, относящиеся к выполнению;
- динамические представления управления, относящиеся к транзакциям.

Подробные сведения о динамических представлениях управления см. в разделе [Динамические административные представления и функции (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) электронной документации на SQL Server.

## Разрешения

В Базе данных SQL для запроса динамического представления управления требуется наличие разрешений **VIEW DATABASE STATE**. Разрешение **VIEW DATABASE STATE** возвращает сведения обо всех объектах в текущей базе данных. Чтобы предоставить разрешение **VIEW DATABASE STATE** определенному пользователю базы данных, выполните следующий запрос:

```GRANT VIEW DATABASE STATE TO database_user; ```

В экземпляре локального сервера SQL Server динамические административные представления возвращают сведения о состоянии сервера. В базе данных SQL они возвращают сведения только о текущей логической базе данных.

## Вычисление размера базы данных

Следующий запрос возвращает размер базы данных в мегабайтах:

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

Следующий запрос возвращает размер отдельных объектов базы данных в мегабайтах:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## Мониторинг подключений

Для получения информации о подключениях, установленных к определенному серверу базы данных SQL Azure, можно использовать представление [sys.dm\_exec\_connection](https://msdn.microsoft.com/library/ms181509.aspx). Кроме того, представление [sys.dm\_exec\_sessions](https://msdn.microsoft.com/library/ms176013.aspx) может оказаться полезным при получении сведений обо всех активных подключениях пользователя и внутренних задачах. Следующий запрос получает информацию о текущем подключении:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Если при выполнении представлений **sys.dm\_exec\_requests** и **sys.dm\_exec\_sessions** у вас есть разрешение **VIEW DATABASE STATE** для базы данных, то вы увидите все выполняющиеся сеансы в базе данных. В противном случае вы увидите только текущий сеанс.

## Мониторинг производительности запросов

Медленные или длительные запросы могут потреблять значительные системные ресурсы. В этом разделе показано, как использовать динамические представления управления для выявления нескольких распространенных проблем производительности запросов. Статья [Устранение неполадок производительности в SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) на веб-сайте Microsoft TechNet — не новый, но по-прежнему полезный справочник по устранению неполадок.

### Поиск верхних N запросов

В следующем примере возвращаются сведения о пяти первых запросах, отсортированных по среднему времени ЦП. В этом примере выполняется сбор запросов по хэшу запроса, то есть логически схожие запросы группируются по общему потреблению ресурсов.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### Мониторинг заблокированных запросов

Медленные или длительные запросы могут вызывать избыточное потребление ресурсов, что приводит к блокировке запросов. Причиной блокировки может быть неэффективная структура приложений, неудачные планы запросов, отсутствие полезных индексов и т. д. Представление sys.dm\_tran\_locks можно использовать для получения сведений о текущих блокировках в Базе данных SQL Azure. Пример кода см. в разделе [sys.dm\_tran\_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) в электронной документации на SQL Server.

### Мониторинг планов запросов

Неэффективный план запросов может повысить потребление ресурсов ЦП. В следующем примере представление [sys.dm\_exec\_query\_stats](https://msdn.microsoft.com/library/ms189741.aspx) используется, чтобы определить, какой запрос использует наибольшее количество ресурсов ЦП.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## Дополнительные материалы

[Введение в базы данных SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0921_2016-->