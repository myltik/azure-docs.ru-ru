.<properties
   pageTitle="Мониторинг рабочей нагрузки с помощью динамических административных представлений | Microsoft Azure"
   description="Узнайте о том, как организовать отслеживание рабочей нагрузки с помощью динамических административных представлений."
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
   ms.date="08/28/2016"
   ms.author="sonyama;barbkess"/>

# Мониторинг рабочей нагрузки с помощью динамических административных представлений

В этой статье рассказывается, как использовать динамические административные представления (DMV) для наблюдения за рабочей нагрузкой и проверки выполнения запросов в хранилище данных SQL Azure.

## Мониторинг подключений

Все операции входа в хранилище данных SQL регистрируются в [sys.dm\_pdw\_exec\_sessions][]. Это динамическое административное представление содержит записи о последних 10 000 операций входа. Идентификатор session\_id является первичным ключом и назначается последовательно для каждого нового входа.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## Наблюдение за выполнением запросов

Все выполняемые запросы к хранилищу данных SQL регистрируются в [sys.dm\_pdw\_exec\_requests][]. Это динамическое административное представление содержит записи о последних 10 000 запросах. Идентификатор request\_id уникально идентифицирует каждый запрос и является первичным ключом для этого динамического административного представления. Идентификатор request\_id назначается последовательно для каждого нового запроса с добавлением префикса QID, означающего идентификатор запроса. При запросе конкретного session\_id из этого динамического административного представления будут показаны все запросы для данной операции входа.

>[AZURE.NOTE] Хранимые процедуры используют несколько идентификаторов request\_id. Идентификатора запросов назначаются последовательно.

Ниже приведено несколько действий для проверки планов выполнения запросов и длительности конкретных запросов.

### Шаг 1. Определение запроса, который нужно исследовать

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

Запросы в состоянии **Приостановлено** поставлены в очередь из-за ограничений параллелизма. Эти запросы также отображаются в результатах запроса Waits к sys.dm\_pdw\_waits с типом UserConcurrencyResourceType. Дополнительные сведения об ограничениях параллелизма см. в разделе [Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL][]. Запросы также могут быть отложены по другим причинам, в том числе из-за блокировки объектов. Если запрос ожидает ресурс, ознакомьтесь с [изучением запросов, ожидающих ресурсы][], описанным далее в этой статье.

Чтобы упростить поиск запроса в таблице sys.dm\_pdw\_exec\_requests, используйте [LABEL][], чтобы добавить комментарий к запросу, который можно будет найти в представлении sys.dm\_pdw\_exec\_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### Шаг 2. Изучение плана запроса

С помощью идентификатора запроса получите план DSQL запроса из [sys.dm\_pdw\_request\_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Если план DSQL выполняется больше времени, чем ожидалось, причина может быть в том, что это сложный план со множеством действий DSQL или только одним этапом, который выполняется слишком долго. Если план содержит множество действий с несколькими операциями перемещения, рассмотрите возможность оптимизировать распределение таблиц, чтобы сократить перемещение данных. В статье [Распределение таблиц][] объясняется, почему необходимо перемещать данные для разрешения запроса, и описываются некоторые стратегии распределения, позволяющие свести к минимуму перемещение данных.

Чтобы узнать больше об отдельном этапе, перейдите к столбцу *operation\_type* самого длительного этапа запроса и запишите значение **Индекс этапа**.

- Перейдите к шагу 3а для **операций SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Перейдите к шагу 3б для **операций перемещения данных**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### Шаг 3а. Изучение SQL распределенных баз данных

Используйте идентификатор запроса и индекс этапа, чтобы извлечь сведения из представления [sys.dm\_pdw\_sql\_requests][], которое содержит информацию о выполнении этапа запроса на каждой из распределенных баз данных.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Когда выполняется этап запроса, можно использовать [DBCC PDW\_SHOWEXECUTIONPLAN][], чтобы получить из кэша планов SQL Server предполагаемый план SQL Server для этапа, выполняемого с определенным распределением.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### Шаг 3б. Изучение перемещения данных в распределенных базах данных

Используйте идентификатор запроса и индекс этапа, чтобы получить из [sys.dm\_pdw\_dms\_workers][] сведения об этапе перемещения данных, выполняющемся для каждого распределения.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Перейдите к столбцу *total\_elapsed\_time*, чтобы просмотреть, имеется ли определенная операция распространения, выполнение которой занимает значительно больше времени, чем другие, для перемещения данных.
- Обратитесь к столбцу *rows\_processed* для длительно выполняющейся операции распространения и проверьте, является ли количество перемещаемых этой операцией строк значительно большим по сравнению с другими. Если это так, то это может означать отклонение базовых данных.

Если запрос выполняется, то можно использовать [DBCC PDW\_SHOWEXECUTIONPLAN][], чтобы получить из кэша планов SQL Server предполагаемый план для выполняемого шага SQL для определенного распределения.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## Наблюдение за ожидающими запросами

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

Если запрос активно ожидает ресурсы из другого запроса, то его состоянием будет **AcquireResources**. Если запрос содержит все необходимые ресурсы, то его состоянием будет **Granted**.

## Дальнейшие действия
Дополнительные сведения о динамических административных представлениях см. в разделе [Системные представления][]. Советы по управлению хранилищем данных SQL см. в разделе [Общие сведения об управлении][]. Рекомендации см. в разделе [Рекомендации по использованию хранилища данных SQL][].

.<!--Image references-->

.<!--Article references-->
[Общие сведения об управлении]: ./sql-data-warehouse-overview-manage.md
[Рекомендации по использованию хранилища данных SQL]: ./sql-data-warehouse-best-practices.md
[Системные представления]: ./sql-data-warehouse-reference-tsql-system-views.md
[Распределение таблиц]: ./sql-data-warehouse-tables-distribute.md
[Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL]: ./sql-data-warehouse-develop-concurrency.md
[изучением запросов, ожидающих ресурсы]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

<!---HONumber=AcomDC_0831_2016-->