---
title: Анализ рабочей нагрузки в хранилище данных SQL Azure | Документация Майкрософт
description: Методы анализа приоритетов запросов для рабочей нагрузки в хранилище данных SQL Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6b0d39b81b72615a9522e95558a59007b10bf109
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795363"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Анализ рабочей нагрузки в хранилище данных SQL Azure
Методы анализа приоритетов запросов для рабочей нагрузки в хранилище данных SQL Azure.

## <a name="workload-groups"></a>Группы рабочей нагрузки 
Классы ресурсов в хранилище данных SQL реализованы с помощью групп рабочей нагрузки. Для управления поведением классов ресурсов для различных размеров DWU используются восемь групп рабочей нагрузки. Для любого значения DWU хранилище данных SQL использует только четыре группы рабочей нагрузки из восьми. Это связано с тем, что каждая группа рабочей нагрузки назначается только одному из четырех классов ресурсов: smallrc, mediumrc, largerc или xlargerc. Этот аспект важно учитывать, так как некоторым группам рабочей нагрузки назначается более высокий *приоритет*. Значение приоритета используется при планировании распределения ресурсов ЦП. Запросы, которым назначена высокая важность, получают в три раза больше циклов ЦП, чем запросы средней важности. Поэтому сопоставление слотов выдачи также определяет приоритет ЦП. Если для выполнения запроса используется 16 или больше слотов, он помечается как запрос с высоким приоритетом.

В таблице ниже приведены сведения о приоритете для каждой группы рабочей нагрузки.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Сопоставление групп рабочей нагрузки со слотами выдачи и приоритетами

| Группы рабочей нагрузки | Сопоставление слотов выдачи | МБ/распределение (эластичность) | МБ/распределение (вычислительные ресурсы) | Приоритет |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Средний             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Средний             |
| SloDWGroupC02   | 4.                        |    400                         | 1000                        | Средний             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Средний             |
| SloDWGroupC04   | 16                       |  1 600                         | 4000                        | Высокий               |
| SloDWGroupC05   | 32                       |  3200                         | 8000                        | Высокий               |
| SloDWGroupC06   | 64                       |  6400                         | 16 000                      | Высокий               |
| SloDWGroupC07   | 128                      | 12 800                         | 32 000                      | Высокий               |
| SloDWGroupC08   | 256                      | 25 600                         | 64 000                      | Высокий               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
На диаграмме **Выделение и использование слотов параллелизма** видно, что DW500 использует 1, 4, 8 или 16 слотов выдачи для классов smallrc, mediumrc, largerc и xlargerc, соответственно. Чтобы узнать, какая важность присвоена каждому классу ресурсов, вы можете ознакомиться с этими значениями на предыдущей диаграмме.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Сопоставление классов ресурсов с приоритетами для DW500
| Класс ресурсов | Группа рабочей нагрузки | Число используемых слотов выдачи | МБ/распределение | приоритет |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Средний     |
| mediumrc       | SloDWGroupC02  | 4.                      | 400               | Средний     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Средний     |
| xlargerc       | SloDWGroupC04  | 16                     | 1 600             | Высокий       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Средний     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Средний     |
| staticrc30     | SloDWGroupC02  | 4.                      | 400               | Средний     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Средний     |
| staticrc50     | SloDWGroupC03  | 16                     | 1 600             | Высокий       |
| staticrc60     | SloDWGroupC03  | 16                     | 1 600             | Высокий       |
| staticrc70     | SloDWGroupC03  | 16                     | 1 600             | Высокий       |
| staticrc80     | SloDWGroupC03  | 16                     | 1 600             | Высокий       |

## <a name="view-workload-groups"></a>Просмотр групп рабочей нагрузки
В следующем запросе показаны подробные сведения о выделении ресурсов памяти с точки зрения регулятора ресурсов. Это полезно для анализа текущего и прошлого использования групп рабочей нагрузки при устранении неполадок.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Представление, используемое для определения запросов, поставленных в очередь, и другие динамические административные представления
Определить запросы, попавшие в очередь параллельной обработки, можно с помощью динамического административного представления `sys.dm_pdw_exec_requests` . Запросы, ожидающие выделения слота параллелизма, находятся в **приостановленном**состоянии.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Чтобы просмотреть роли управления рабочей нагрузкой, можно использовать представление `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Следующий запрос позволяет определить, в какие роли добавлены пользователи.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

В хранилище данных SQL предусмотрены следующие типы ожиданий.

* **LocalQueriesConcurrencyResourceType**относится к запросам, которые не входят в платформу слотов выдачи. В качестве примеров таких запросов можно привести запросы и системные функции динамических административных представлений, такие как `SELECT @@VERSION` .
* **UserConcurrencyResourceType**относится к запросам, которые входят в платформу слотов выдачи. В качестве примеров использования этого типа ресурсов можно привести запросы к таблицам пользователя.
* **DmsConcurrencyResourceType**относится к ожиданиям, связанным с операциями перемещения данных.
* **BackupConcurrencyResourceType**может использоваться при создании резервной копии базы данных. Максимальное значение для этого типа ресурсов равно 1. При одновременном запросе сразу нескольких резервных копий все остальные запросы помещаются в очередь.

Определить, какие ресурсы необходимы для запроса, можно при помощи динамического административного представления `sys.dm_pdw_waits` .

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

Динамическое административное представление `sys.dm_pdw_resource_waits` показывает только время ожидания ресурса по конкретному запросу. Время ожидания ресурсов — это время, в течение которого запросы ожидают предоставления ресурсов, а время ожидания сигнала — время, необходимое базовому экземпляру SQL Server, чтобы спланировать выделение запросу ресурсов ЦП.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
Вы также можете использовать DMV `sys.dm_pdw_resource_waits`, чтобы вычислить, сколько слотов выдачи было предоставлено.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

Для анализа тенденций времени ожидания за прошедший период используется динамическое административное представление `sys.dm_pdw_wait_stats` .

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об управлении пользователями и безопасностью базы данных см. в статье [Защита базы данных в хранилище данных SQL](sql-data-warehouse-overview-manage-security.md). Дополнительные сведения о повышении качества кластеризованных индексов columnstore за счет повышения класса ресурсов см. в разделе [Повышение качества сегментов за счет перестроения индексов](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).


