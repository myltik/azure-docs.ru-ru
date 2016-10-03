<properties
   pageTitle="Общие сведения о таблицах в хранилище данных SQL | Microsoft Azure"
   description="Начало работы с таблицами хранилища данных SQL Azure."
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
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Общие сведения о таблицах в хранилище данных SQL

> [AZURE.SELECTOR]
- [Обзор][]
- [Типы данных][]
- [Распределение][]
- [Индекс][]
- [Секция][]
- [Статистика][]
- [Временные таблицы][]

Приступить к созданию таблиц в хранилище данных SQL совсем нетрудно. В основе базового синтаксиса [CREATE TABLE][] лежит стандартный синтаксис, с которым вы, скорее всего, уже ознакомились, работая с другими базами данных. Чтобы создать таблицу, нужно просто назвать таблицу и столбцы, а также определить типы данных для каждого столбца. Если вы создавали таблицы в других базах данных, вы должны быть знакомы с этим процессом.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

В приведенном выше примере создается таблица Customers с двумя столбцами: FirstName и LastName. Для каждого столбца определен тип данных VARCHAR(25), который ограничивает длину данных до 25 символов. Эти основные атрибуты таблицы, как и другие, в большей степени такие же, как и в других базах данных. Типы данных определяются для каждого столбца. Они обеспечивают целостность данных. Вы можете добавить индексы, чтобы повысить производительность за счет сокращения операций ввода-вывода. Если нужно повысить производительность при изменении данных, можно добавить секционирование.

[Переименование][RENAME] таблицы хранилища данных SQL выглядит следующим образом:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## Распределенные таблицы

В распределенных системах, таких как хранилище данных SQL, представлен новый основной атрибут — **столбец распределения**. Его функции соответствуют названию. Этот столбец определяет способ распределения или разделения данных в фоновом режиме. Если при создании таблицы не указать столбец распределения, таблица автоматически распределяется с использованием метода **циклического перебора**. Хотя в некоторых сценариях таблиц, распределенных с использованием метода циклического перебора, может быть достаточно, определение столбцов распределения может значительно уменьшить перемещение данных во время выполнения запросов. Это позволяет оптимизировать производительность. Дополнительные сведения о том, как выбрать столбец распределения, см. в статье о [распределении таблицы][Distribute].

## Индексирование и секционирование таблиц

Когда вы усовершенствуете навыки работы с хранилищем данных SQL и захотите оптимизировать производительность, вам потребуется узнать больше о конструкторе таблиц. Дополнительные сведения см. в статьях, посвященных [типам данных таблиц][Data Types], [распределению][Distribute], [индексированию][Index] и [секционированию таблицы][Partition].

## Статистика таблицы

Статистика очень важна для обеспечения наилучшей производительности хранилища данных SQL. Так как хранилище данных SQL еще не создает и не обновляет статистику автоматически, что свойственно для Базы данных SQL Azure, рекомендуется ознакомиться со статьей о [статистике][], в которой содержатся сведения по обеспечению наилучшей производительности запросов.

## Временные таблицы

Временные таблицы — это таблицы, которые существуют, пока вы пребываете в системе, и не видны другим пользователям. Если вы используете такие таблицы, другие пользователи не смогут видеть ваши временные результаты. Кроме того, уменьшается потребность в очистке. Так как временные таблицы используют локальное хранилище, некоторые операции выполняются быстрее. Дополнительные сведения см в статье о [временной таблице][Temporary].

## Внешние таблицы

Внешние таблицы, также известные как таблицы Polybase, можно запрашивать из хранилища данных SQL, но данные, на которые они указывают, расположены за его пределами. Например, можно создать внешнюю таблицу, указывающую на файлы в хранилище BLOB-объектов Azure. Дополнительные сведения о создании и запрашивании внешней таблицы см. в статье о [загрузке данных с помощью Polybase][].

## Неподдерживаемые функции таблиц

Хотя хранилище данных SQL содержит много функций таблиц, которые характерны для других баз данных, некоторые функции еще не поддерживаются. Ниже приведен список функций таблиц, которые еще не поддерживаются.

| Неподдерживаемые функции |
| --- |
|[Свойство IDENTITY][] \(см. статью блога о [назначении суррогатных ключей][])|
|Первичный ключ, внешние ключи, [ограничения таблицы][] UNIQUE и CHECK|
|[Уникальные индексы][]|
|[Вычисляемые столбцы][]|
|[Разреженные столбцы][]|
|[Пользовательские типы][]|
|[Последовательность][]|
|[триггеры;][]|
|[Индексированные представления][]|
|[синонимы;][]|

## Запросы размера таблицы

Простой способ определения пространства и строк, используемых таблицей в каждом из 60 распределений, — при помощи команды [DBCC PDW\_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Однако возможности команд DBCC достаточно ограничены. Динамические административные представления содержат больше сведений, а также обеспечивают больший контроль над результатами запросов. Начните с создания этого представления, на которое будут ссылаться многие примеры в этой и других статьях.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### Сводка табличного пространства

Этот запрос возвращает количество строк и объем каждой таблицы. Он позволяет узнать, какие таблица занимают большего всего места, в каких таблицах применяется циклический перебор, а в каких — хэш-распределение. Для таблиц с хэш-распределением запрос показывает столбец распределения. В большинстве случаев наибольшая таблица должна иметь хэш-распределение и кластеризованный индекс columnstore.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,	  distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,	  distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### Табличное пространство по типу распределения

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### Табличное пространство по типу индекса

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### Сводка пространства распределения

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## Дальнейшие действия

Дополнительные сведения см. в статьях [Типы данных таблиц в хранилище данных SQL][Data Types], [Распределение таблиц в хранилище данных SQL][Distribute], [Индексирование таблиц в хранилище данных SQL][Index], [Секционирование таблиц в хранилище данных SQL][Partition], [Управление статистикой таблиц в хранилище данных SQL][Statistics] и [Временные таблицы в хранилище данных SQL][Temporary]. Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL Azure][].

<!--Image references-->

<!--Article references-->
[Обзор]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Типы данных]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Распределение]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Индекс]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Секция]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Статистика]: ./sql-data-warehouse-tables-statistics.md
[статистике]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Временные таблицы]: ./sql-data-warehouse-tables-temporary.md
[Рекомендации по использованию хранилища данных SQL Azure]: ./sql-data-warehouse-best-practices.md
[загрузке данных с помощью Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW\_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Свойство IDENTITY]: https://msdn.microsoft.com/library/ms186775.aspx
[назначении суррогатных ключей]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[ограничения таблицы]: https://msdn.microsoft.com/library/ms188066.aspx
[Вычисляемые столбцы]: https://msdn.microsoft.com/library/ms186241.aspx
[Разреженные столбцы]: https://msdn.microsoft.com/library/cc280604.aspx
[Пользовательские типы]: https://msdn.microsoft.com/library/ms131694.aspx
[Последовательность]: https://msdn.microsoft.com/library/ff878091.aspx
[триггеры;]: https://msdn.microsoft.com/library/ms189799.aspx
[Индексированные представления]: https://msdn.microsoft.com/library/ms191432.aspx
[синонимы;]: https://msdn.microsoft.com/library/ms177544.aspx
[Уникальные индексы]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0810_2016-->
