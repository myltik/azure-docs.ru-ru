---
title: "Мониторинг рабочей нагрузки с помощью динамических административных представлений | Документация Майкрософт"
description: "Узнайте о том, как организовать отслеживание рабочей нагрузки с помощью динамических административных представлений."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 69ecd479-0941-48df-b3d0-cf54c79e6549
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3735d656429da1f1fe7569f640b272b099382032
ms.contentlocale: ru-ru
ms.lasthandoff: 04/03/2017


---
# <a name="monitor-your-workload-using-dmvs"></a>Мониторинг рабочей нагрузки с помощью динамических административных представлений
В этой статье рассказывается, как использовать динамические административные представления (DMV) для наблюдения за рабочей нагрузкой и проверки выполнения запросов в хранилище данных SQL Azure.

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

Запросы в состоянии **Приостановлено** поставлены в очередь из-за ограничений параллелизма. Эти запросы также отображаются в результатах запроса Waits к sys.dm_pdw_waits с типом UserConcurrencyResourceType. Дополнительные сведения об ограничениях параллелизма см. в разделе [Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL][Concurrency and workload management]. Запросы также могут быть отложены по другим причинам, в том числе из-за блокировки объектов.  Если запрос ожидает ресурс, ознакомьтесь с разделом [Исследование запросов, ожидающих ресурсы][Investigating queries waiting for resources] далее в этой статье.

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
* Для длительной операции распространения обратитесь к столбцу *rows_processed* и проверьте, является ли количество перемещаемых строк для этой операции значительно большим по сравнению с другими операциями. Если это так, то это может означать отклонение базовых данных.

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

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о динамических административных представлениях см. в разделе [Системные представления][System views].
Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL][SQL Data Warehouse best practices].

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
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

