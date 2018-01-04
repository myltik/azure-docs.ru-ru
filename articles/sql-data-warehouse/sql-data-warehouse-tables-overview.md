---
title: "Общие сведения о таблицах в хранилище данных SQL | Документация Майкрософт"
description: "Начало работы с таблицами хранилища данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/14/2017
ms.author: barbkess
ms.openlocfilehash: 46f7d2ea19a88e65b2d039fdf36d1619c4d74020
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>Общие сведения о таблицах в хранилище данных SQL
> [!div class="op_single_selector"]
> * [Обзор][Overview]
> * [Типы данных][Data Types]
> * [Распределение][Distribute]
> * [Индекс][Index]
> * [Секция][Partition]
> * [Статистика][Statistics]
> * [Временные таблицы][Temporary]
> 
> 

Приступить к созданию таблиц в хранилище данных SQL совсем нетрудно.  В основе базового синтаксиса [CREATE TABLE][CREATE TABLE] лежит стандартный синтаксис, с которым вы, скорее всего, уже ознакомились, работая с другими базами данных.  Чтобы создать таблицу, нужно просто назвать таблицу и столбцы, а также определить типы данных для каждого столбца.  Если вы создавали таблицы в других базах данных, вы должны быть знакомы с этим процессом.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

В приведенном выше примере создается таблица Customers с двумя столбцами: FirstName и LastName.  Для каждого столбца определен тип данных VARCHAR(25), который ограничивает длину данных до 25 символов.  Эти основные атрибуты таблицы, как и другие, в большей степени такие же, как и в других базах данных.  Типы данных определяются для каждого столбца. Они обеспечивают целостность данных.  Вы можете добавить индексы, чтобы повысить производительность за счет сокращения операций ввода-вывода.  Если нужно повысить производительность при изменении данных, можно добавить секционирование.

Команда для [переименования][RENAME] таблицы хранилища данных SQL выглядит следующим образом.

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Распределенные таблицы
В распределенных системах, таких как хранилище данных SQL, представлен новый основной атрибут — **столбец распределения**.  Его функции соответствуют названию.  Этот столбец определяет способ распределения или разделения данных в фоновом режиме.  Если при создании таблицы не указать столбец распределения, таблица автоматически распределяется с использованием метода **циклического перебора**.  Хотя в некоторых сценариях таблиц, распределенных с использованием метода циклического перебора, может быть достаточно, определение столбцов распределения может значительно уменьшить перемещение данных во время выполнения запросов. Это позволяет оптимизировать производительность.  Если таблица содержит небольшой объем данных, создайте таблицу таким способом: тип распределения **репликации** копирует данные на каждый вычислительный узел и сохраняет перемещение данных во время выполнения запроса. Дополнительные сведения о том, как выбрать столбец распределения, см. в статье [Распределение таблиц в хранилище данных SQL][Distribute].

## <a name="indexing-and-partitioning-tables"></a>Индексирование и секционирование таблиц
Когда вы усовершенствуете навыки работы с хранилищем данных SQL и захотите оптимизировать производительность, вам потребуется узнать больше о конструкторе таблиц.  Дополнительные сведения см. в статьях, посвященных [типам данных таблиц][Data Types], [распределению][Distribute], [индексированию][Index] и [секционированию][Partition] таблиц.

## <a name="table-statistics"></a>Статистика таблицы
Статистика очень важна для обеспечения наилучшей производительности хранилища данных SQL.  Так как хранилище данных SQL еще не создает и не обновляет статистику автоматически, что свойственно для Базы данных SQL Azure, рекомендуется ознакомиться со статьей о [статистике][Statistics], в которой содержатся сведения об обеспечении наилучшей производительности запросов.

## <a name="temporary-tables"></a>Временные таблицы
Временные таблицы — это таблицы, которые существуют, пока вы пребываете в системе, и не видны другим пользователям.  Если вы используете такие таблицы, другие пользователи не смогут видеть ваши временные результаты. Кроме того, уменьшается потребность в очистке.  Так как временные таблицы используют локальное хранилище, некоторые операции выполняются быстрее.  Дополнительные сведения см в статье [Временные таблицы в хранилище данных SQL][Temporary].

## <a name="external-tables"></a>Внешние таблицы
Внешние таблицы, также известные как таблицы Polybase, можно запрашивать из хранилища данных SQL, но данные, на которые они указывают, расположены за его пределами.  Например можно создать внешней таблицы, которое указывает на файлы в хранилище больших двоичных объектов Azure или хранилища Озера данных Azure.  Дополнительные сведения о создании и запросе внешней таблицы см. в статье [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL (PolyBase)][Load data with Polybase].  

## <a name="unsupported-table-features"></a>Неподдерживаемые функции таблиц
Хотя хранилище данных SQL содержит много функций таблиц, которые характерны для других баз данных, некоторые функции еще не поддерживаются.  Ниже приведен список функций таблиц, которые еще не поддерживаются.

| Неподдерживаемые функции |
| --- |
| Первичный ключ, внешние ключи, [ограничения таблицы][Table Constraints] |
| [Уникальные индексы][Unique Indexes] |
| [Вычисляемые столбцы][Computed Columns] |
| [Разреженные столбцы][Sparse Columns] |
| [Пользовательские типы][User-Defined Types] |
| [Последовательность][Sequence] |
| [Триггеры][Triggers] |
| [Индексированные представления][Indexed Views] |
| [Синонимы][Synonyms] |

## <a name="table-size-queries"></a>Запросы размера таблицы
Простой способ определения пространства и строк, используемых таблицей в каждом из 60 распределений, — с помощью инструкции [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Однако возможности команд DBCC достаточно ограничены.  Динамические административные представления содержат больше сведений, а также обеспечивают больший контроль над результатами запросов.  Начните с создания этого представления, на которое будут ссылаться многие примеры в этой и других статьях.

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

### <a name="table-space-summary"></a>Сводка табличного пространства
Этот запрос возвращает количество строк и объем каждой таблицы.  Он позволяет узнать, какие таблицы занимают большего всего места, в каких таблицах применяется циклический перебор, какие таблицы реплицированы, а в каких используется хэш-распределение.  Для таблиц с хэш-распределением запрос показывает столбец распределения.  В большинстве случаев наибольшая таблица должна иметь хэш-распределение и кластеризованный индекс columnstore.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
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
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Табличное пространство по типу распределения
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

### <a name="table-space-by-index-type"></a>Табличное пространство по типу индекса
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

### <a name="distribution-space-summary"></a>Сводка пространства распределения
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

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в статьях, посвященных [типам данных таблиц][Data Types], [распределению][Distribute], [индексированию][Index] и [секционированию таблиц][Partition], а также [управлению статистикой таблиц][Statistics] и [временным таблицам][Temporary].  Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL Azure][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Load data with Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Table Constraints]: https://msdn.microsoft.com/library/ms188066.aspx
[Computed Columns]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse Columns]: https://msdn.microsoft.com/library/cc280604.aspx
[User-Defined Types]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequence]: https://msdn.microsoft.com/library/ff878091.aspx
[Triggers]: https://msdn.microsoft.com/library/ms189799.aspx
[Indexed Views]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonyms]: https://msdn.microsoft.com/library/ms177544.aspx
[Unique Indexes]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
