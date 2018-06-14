---
title: Мониторинг рабочей нагрузки с помощью динамических административных представлений | Документация Майкрософт
description: Узнайте о том, как организовать отслеживание рабочей нагрузки с помощью динамических административных представлений.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 887fa4b9f950531438986269d041189d45cdecb2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522478"
---
# <a name="monitor-your-workload-using-dmvs"></a>Мониторинг рабочей нагрузки с помощью динамических административных представлений
В этой статье объясняется, как отслеживать рабочую нагрузку с помощью динамических административных представлений (DMV). Она включает в себя анализ выполнения запросов в хранилище данных SQL Azure.

## <a name="permissions"></a>Разрешения
Для запроса динамических административных представлений в этой статье, необходимо разрешение VIEW DATABASE STATE или CONTROL. Более предпочтительно разрешение VIEW DATABASE STATE, так как оно гораздо строже.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Мониторинг подключений
Все операции входа в хранилище данных SQL регистрируются в [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Это динамическое административное представление содержит записи о последних 10 000 операций входа.  Идентификатор session_id является первичным ключом и назначается последовательно для каждого нового входа.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Наблюдение за выполнением запросов
Все запросы к хранилищу данных SQL регистрируются в [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Это динамическое административное представление содержит записи о последних 10 000 запросах.  Идентификатор request_id уникально идентифицирует каждый запрос и является первичным ключом для этого динамического административного представления.  Идентификатор request_id назначается последовательно для каждого нового запроса с добавлением префикса QID, означающего идентификатор запроса.  При запросе конкретного session_id из этого динамического административного представления будут показаны все запросы для данной операции входа.

> [!NOTE]
> Хранимые процедуры используют несколько идентификаторов request_id.  Идентификатора запросов назначаются последовательно. 
> 
> 

Ниже приведено несколько действий для проверки планов выполнения запросов и длительности конкретных запросов.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>Шаг 1. Определение запроса, который нужно исследовать
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

**Запишите идентификатор запроса**, который вы хотите исследовать. Он указан в приведенных выше результатах запроса.

Запросы в состоянии **Приостановлено** поставлены в очередь из-за ограничений параллелизма. Эти запросы также отображаются в результатах запроса Waits к sys.dm_pdw_waits с типом UserConcurrencyResourceType. Сведения об ограничениях параллелизма см. в статье об [уровнях производительности](performance-tiers.md) или [классах ресурсов для управления рабочей нагрузкой](resource-classes-for-workload-management.md). Запросы также могут быть отложены по другим причинам, в том числе из-за блокировки объектов.  Если запрос ожидает ресурс, ознакомьтесь с разделом [Исследование запросов, ожидающих ресурсы][Investigating queries waiting for resources] далее в этой статье.

Чтобы упростить поиск запроса в таблице sys.dm_pdw_exec_requests, используйте [LABEL][LABEL], чтобы добавить комментарий к запросу, который можно будет найти в представлении sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>Шаг 2. Изучение плана запроса
С помощью идентификатора запроса получите план DSQL запроса из [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Если план DSQL выполняется больше времени, чем ожидалось, причина может быть в том, что это сложный план со множеством действий DSQL или только одним этапом, который выполняется слишком долго.  Если план содержит множество действий с несколькими операциями перемещения, рассмотрите возможность оптимизировать распределение таблиц, чтобы сократить перемещение данных. В статье [Распределение таблиц][Table distribution] объясняется, почему необходимо перемещать данные для разрешения запроса, и описываются некоторые стратегии распределения, позволяющие свести к минимуму перемещение данных.

Чтобы узнать больше об отдельном этапе, перейдите к столбцу *operation_type* самого длительного этапа запроса и запишите значение **Индекс этапа**:

* Перейдите к шагу 3а для **операций SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Перейдите к шагу 3б для **операций перемещения данных**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>Шаг 3а. Изучение SQL распределенных баз данных
Используйте идентификатор запроса и индекс этапа, чтобы извлечь сведения из представления [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], которое содержит информацию о выполнении этапа запроса на каждой из распределенных баз данных.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Когда выполняется этап запроса, можно использовать [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], чтобы получить из кэша планов SQL Server предполагаемый план SQL Server для этапа, выполняемого с определенным распределением.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>Шаг 3б. Изучение перемещения данных в распределенных базах данных
Используйте идентификатор запроса и индекс этапа, чтобы получить из [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers] сведения об этапе перемещения данных, выполняющемся для каждого распределения.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Перейдите к столбцу *total_elapsed_time*, чтобы просмотреть, есть ли какая-то операция распространения, перемещение данных для которой значительно больше времени по сравнению с другими операциями.
* Для длительной операции распространения обратитесь к столбцу *rows_processed* и проверьте, является ли количество перемещаемых строк для этой операции значительно большим по сравнению с другими операциями. Если это так, такой результат может означать отклонение базовых данных.

Если запрос выполняется, то можно использовать [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], чтобы получить из кэша планов SQL Server предполагаемый план для выполняемого шага SQL для определенного распределения.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Наблюдение за ожидающими запросами
Если вы обнаружили, что запрос не выполняется, так как ожидает ресурс, то можете отобразить все ожидаемые запросом ресурсы, выполнив приведенный ниже запрос.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Если запрос активно ожидает ресурсы из другого запроса, то его состоянием будет **AcquireResources**.  Если запрос содержит все необходимые ресурсы, то его состоянием будет **Granted**.

## <a name="monitor-tempdb"></a>Мониторинг tempdb
Чрезмерное использование tempdb может стать основной причиной низкой производительности и нехватки памяти. Вы можете рассмотреть масштабирование хранилища данных, если во время выполнения запроса наблюдается приближение к пределам базы данных tempdb. Ниже описано, как определить использование базы данных tempdb на каждый запрос в каждом узле. 

Создайте следующее представление, чтобы связать соответствующий идентификатор узла для sys.dm_pdw_sql_requests. Наличие идентификатора узла позволит вам использовать другие сквозные динамические административные представления и объединять эти таблицы с sys.dm_pdw_sql_requests.

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
Для отслеживания базы данных tempdb выполните следующий запрос:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>Мониторинг памяти

Объем может стать основной причиной низкой производительности и нехватки памяти. Вы можете рассмотреть масштабирование хранилища данных, если во время выполнения запроса наблюдается активное использование памяти SQL Server (приближение к пределам).

Следующий запрос возвращает использование памяти SQL Server и нехватку памяти на каждом узле:   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>Мониторинг размера журнала транзакций
Следующий запрос возвращает размер журнала транзакций для каждого распределения. Если один из файлов журнала достигает размера 160 ГБ, следует рассмотреть вертикальное масштабирование экземпляра или ограничение размера транзакции. 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>Мониторинг отката журнала транзакций
Если запросы завершаются сбоем или требуют слишком много времени на выполнение, вы можете отследить, происходит ли откат транзакций.
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о динамических административных представлениях см. в статье о [системных представлениях][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
