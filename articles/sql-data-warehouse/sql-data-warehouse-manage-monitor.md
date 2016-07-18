<properties
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
   ms.date="06/30/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# Мониторинг рабочей нагрузки с помощью динамических административных представлений

В этой статье рассказывается, как использовать динамические административные представления (DMV) для наблюдения за рабочей нагрузкой и проверки выполнения запросов в хранилище данных SQL Azure.

## Мониторинг подключений

Представление [Sys.dm\_pdw\_exec\_sessions][] позволяет отслеживать подключения к базе данных хранилища данных SQL Azure. Это представление содержит активные сеансы, а также журнал недавно отключенных сеансов. Session\_id является первичным ключом для этого представления и назначается последовательно для каждого нового входа.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## Проверка выполнения запроса
Для отслеживания выполнения запроса начните с представления [sys.dm\_pdw\_exec\_requests][]. Это представление содержит выполняющиеся запросы, а также журнал запросов, которые недавно были выполнены. Идентификатор request\_id уникально идентифицирует каждый запрос и является первичным ключом для этого представления. Этот идентификатор назначается последовательно для каждого нового запроса. При запросе конкретного session\_id из этой таблицы будут показаны все запросы для данного входа в систему.

>[AZURE.NOTE] Хранимые процедуры используют несколько идентификаторов request\_id. Эти идентификаторы назначаются в порядке очередности.

Ниже приведено несколько действий для проверки планов выполнения запросов и длительности конкретных запросов.

### Шаг 1. Поиск запроса для анализа

```sql
-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find 10 queries which ran the longest
SELECT TOP 10 * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;
```

Запишите идентификатор запроса, который вы хотите исследовать.

### Шаг 2. Проверка того, ожидает ли запрос ресурсы

```sql
-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,  
      waits.object_type,
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

В результатах представленного выше запроса будет показано состояние ожидания запроса.

- Если запрос ожидает ресурсы из другого запроса, то состояние будет **AcquireResources**.
- Если запрос содержит все необходимые ресурсы и не находится в состоянии ожидания, то состояние будет **Granted**. В этом случае следует просмотреть этапы запроса.

### Шаг 3. Поиск этапа плана запроса, который выполняется дольше всех

С помощью идентификатора запроса получите список этапов плана запроса из представления [sys.dm\_pdw\_request\_steps][]. Найдите самый долго выполняющийся этап, обратившись к сведениям об общем затраченном времени.

```sql

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;
```

Сохраните индекс самого длительного этапа.

Перейдите к столбцу *operation\_type* самого долго выполняющегося этапа запроса:

- Перейдите к шагу 4a для **операций SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Перейдите к шагу 4b для **операций перемещения данных**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### Шаг 4а. Поиск сведений о ходе выполнения этапа SQL

Используйте идентификатор запроса и индекс этапа, чтобы извлечь данных из представления [sys.dm\_pdw\_sql\_requests][], которое содержит сведения о выполнении запроса в распределенных экземплярах SQL Server. Запишите идентификатор распределения и SPID, если запрос все еще выполняется и вы хотите получить план из распределения SQL Server.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;
```


Если запрос сейчас выполняется, можно использовать [DBCC PDW\_SHOWEXECUTIONPLAN][], чтобы получить план выполнения SQL Server для выполняемого шага SQL для определенного распределения.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```

### Шаг 4b. Поиск сведений о ходе выполнения этапа DMS

Используйте идентификатор запроса и индекс этапа, чтобы получить сведения об этапе перемещения данных, выполняющемся при каждом распределении, из [sys.dm\_pdw\_dms\_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Перейдите к столбцу *total\_elapsed\_time*, чтобы просмотреть, имеется ли определенная операция распространения, выполнение которой занимает значительно больше времени, чем другие, для перемещения данных.
- Обратитесь к столбцу *rows\_processed* для длительно выполняющейся операции распространения и проверьте, является ли количество перемещаемых этой операцией строк значительно большим по сравнению с другими. Если это так, то это может означать отклонение базовых данных.

Если запрос сейчас выполняется, можно использовать [DBCC PDW\_SHOWEXECUTIONPLAN][], чтобы получить план выполнения SQL Server для выполняемого шага DMS для определенного распределения.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);

```

## Дальнейшие действия
Дополнительные сведения о динамических административных представлениях см. в разделе [Системные представления][]. Советы по управлению хранилищем данных SQL см. в разделе [Общие сведения об управлении][]. Рекомендации см. в разделе [Рекомендации по использованию хранилища данных SQL][].

<!--Image references-->

<!--Article references-->
[Общие сведения об управлении]: ./sql-data-warehouse-overview-manage.md
[Рекомендации по использованию хранилища данных SQL]: ./sql-data-warehouse-best-practices.md
[Системные представления]: ./sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[Sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0706_2016-->