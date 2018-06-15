---
title: Проектирование таблиц — хранилище данных SQL Azure | Документация Майкрософт
description: Общие сведения о проектировании таблиц в хранилище данных SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d299ff0d8e719040d503852af6056d9d87738b7d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527945"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Проектирование таблиц в хранилище данных SQL Azure

Ознакомьтесь с ключевыми понятиями о проектировании таблиц в хранилище данных SQL Azure. 

## <a name="determine-table-category"></a>Определение категории таблицы 

При [схеме типа "звезда"](https://en.wikipedia.org/wiki/Star_schema) данные упорядочиваются в таблицы фактов и измерений. Некоторые таблицы используются для интеграции или хранения данных до того, как они перейдут в таблицу фактов или измерений. При конструировании таблицы решите, к какой таблице относятся данные: фактов, измерений или интеграции. Это решение влияет на соответствующую структуру и распределение таблицы. 

- **Таблицы фактов** содержат количественные данные, которые обычно создаются в транзакционной системе, а затем загружаются в хранилище данных. Например, розничное предприятие ежедневно создает транзакции продаж, а затем загружает данные в таблицу фактов хранилища данных для анализа.

- **Таблицы измерений** содержат данные атрибутов, которые могут измениться, хотя обычно это происходит редко. Например, имя и адрес клиента хранятся в таблице измерений и обновляются только при изменении профиля клиента. Чтобы свести к минимуму размер большой таблицы фактов, необязательно включать имя и адрес клиента в каждой строке. Вместо этого в таблице фактов и таблице измерений может совместно использоваться идентификатор клиента. Запрос может объединить две таблицы, чтобы связать профиль и транзакции клиента. 

- **Таблицы интеграции** служат для интеграции или промежуточного размещения данных. Можно создать таблицу интеграции в виде обычной таблицы, внешней таблицы или временной таблицы. Например, вы можете загружать данные в промежуточную таблицу, выполнять преобразования данных в режиме промежуточного размещения, а затем вставлять их в рабочую таблицу.

## <a name="schema-and-table-names"></a>Имена схем и таблиц
В хранилище данных SQL хранилище данных относится к типу базы данных. Все таблицы в хранилище данных содержатся в той же базе данных.  Вы не можете объединить таблицы, находящиеся в разных хранилищах данных. Это поведение отличается от SQL Server, где поддерживается соединение между базами данных. 

В базе данных SQL Server можно использовать fact, dim или integrate для имен схемы. Если вы переносите базу данных SQL Server в хранилище данных SQL, лучше всего переносить все таблицы фактов, измерений и интеграции в одну схему в этом хранилище. Например, можно сохранить все таблицы в примере хранилища данных [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) в рамках одной схемы, которая называется WWI. В следующем коде создается [пользовательская схема](/sql/t-sql/statements/create-schema-transact-sql) с именем WWI.

```sql
CREATE SCHEMA wwi;
```

Чтобы показать организацию таблиц в хранилище данных SQL, можно использовать в качестве префиксов имен таблиц значения fact, dim и int. В следующей таблице показаны некоторые имена схем и таблиц для WideWorldImportersDW. В ней сравниваются имена в SQL Server и хранилище данных SQL. 

| Таблица WideWorldImportersDW  | Тип таблицы | SQL Server; | Хранилище данных SQL. |
|:-----|:-----|:------|:-----|
| City | Измерение | Dimension.City | wwi.DimCity |
| Порядок | Факты | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Сохраняемость таблицы 

Данные таблиц хранятся на постоянной основе в службе хранилища Azure, временно — в службе хранилища Azure или в хранилище данных, являющемся внешним по отношению к хранилищу данных.

### <a name="regular-table"></a>Обычная таблица

Данные обычной таблицы хранятся в службе хранилища Azure как часть хранилища данных. Таблица и данные сохраняются независимо от того, открыт ли сеанс.  В этом примере создается обычная таблица с двумя столбцами. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Временная таблица
Временная таблица существует, только пока отрыт сеанс. Если вы используете такие таблицы, другие пользователи не смогут видеть ваши временные результаты. Кроме того, уменьшается потребность в очистке.  Так как временные таблицы используют локальное хранилище, некоторые операции выполняются быстрее.  Дополнительные сведения см. в статье [Временные таблицы в хранилище данных SQL](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Внешняя таблица
Внешняя таблица указывает на данные, расположенные в большом двоичном объекте службы хранилища Azure или Azure Data Lake Store. При использовании в сочетании с инструкцией CREATE TABLE AS SELECT данные, выбранные из внешней таблицы, импортируются в хранилище данных SQL. Таким образом внешние таблицы можно использовать для загрузки данных. Руководство по загрузке см. в статье [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure с помощью PolyBase](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Типы данных
Хранилище данных SQL поддерживает самые распространенные типы данных. Список поддерживаемых типов данных представлен в [справочнике по типах данных в инструкции CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes). Минимизация размера типов данных помогает улучшить производительность запросов. Рекомендации по использованию типов данных см. в статье [Руководство по определению типов данных для таблиц в хранилище данных SQL](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Распределенные таблицы
Основополагающая функция хранилища данных SQL — это хранение и обработка таблиц с помощью 60 [распределений](massively-parallel-processing-mpp-architecture.md#distributions).  Таблицы распределяются методом циклического перебора, хэш-распределения или репликации.

### <a name="hash-distributed-tables"></a>Таблицы с хэш-распределением
При использовании хэш-распределения строки распределяются по значению в столбце распределения. Хэш-распределенная таблица обеспечивает высокую производительность при выполнении запросов с соединением для больших таблиц. Существует несколько факторов, которые влияют на выбор столбца распределения. 

Дополнительные сведения см. в статье [Руководство по проектированию распределенных таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Реплицированные таблицы
Копия реплицированной таблицы хранится на каждом вычислительном узле. Запросы в реплицированных таблицах выполняются быстро, поскольку объединения в них не требуют перемещения данных. Тем не менее репликация требует дополнительного места и она не целесообразна для больших таблиц. 

Дополнительные сведения см. в статье [Руководство по проектированию для использования реплицированных таблиц в хранилище данных SQL Azure](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Таблицы с распределением методом циклического перебора
Строки таблицы с распределением методом циклического перебора распределяются равномерно по всем распределениям. Строки распределяются случайным образом. Загрузка данных в таблицу с распределением методом циклического перебора происходит быстро.  Однако запросы могут требовать больше перемещений данных, чем при других методах распределения. 

Дополнительные сведения см. в статье [Руководство по проектированию распределенных таблиц в хранилище данных SQL Azure](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Общие методы распределения для таблиц
Вариант распределения таблицы часто зависит от категории таблицы. 

| Категории таблицы | Рекомендуемый вариант распределения |
|:---------------|:--------------------|
| Факты           | Используйте хэш-распределение с кластеризованным индексом columnstore. Производительность улучшается, когда две хэш-таблицы объединены по одному столбцу распределения. |
| Измерение      | Используйте репликацию для небольших таблиц. Если таблицы слишком велики для хранения на каждом вычислительном узле, используйте хэш-распределение. |
| Промежуточная        | Используйте циклический перебор для промежуточных таблиц. Загрузка с помощью инструкции CTAS выполняется быстро. Когда данные окажутся в промежуточной таблице, используйте INSERT...SELECT, чтобы переместить данные в рабочие таблицы. |

## <a name="table-partitions"></a>Разделы таблицы
Секционированная таблица хранит данные и выполняет операции со строками таблицы в соответствии с диапазонами данных. Например, таблицу можно разделить по дню, месяцу или году. Вы можете улучшить производительность запросов путем исключения секций, что ограничивает проверку запросов к данным в секции. Вы также можете управлять данными путем переключения разделов. Так как данные в хранилище данных SQL уже распределены, слишком большое количество разделов может замедлить производительность запросов. Дополнительные сведения см. в статье [Секционирование таблиц в хранилище данных SQL](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Индексы columnstore
По умолчанию в хранилище данных SQL таблицы хранятся в виде кластеризованных индексов columnstore. Такая форма хранения данных обеспечивает высокое сжатие данных и производительность запросов в больших таблицах.  Кластеризованный индекс columnstore обычно является лучшим выбором, но в некоторых случаях подходящей структурой хранения являются кластеризованный индекс или куча.

Список функций индексов columnstore см. в статье [Новые возможности индексов columnstore](/sql/relational-databases/indexes/columnstore-indexes-whats-new). Сведения о повышении производительности индекса columnstore см. в статье [Максимальное повышение качества группы строк для индекса columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Статистика
Оптимизатор запросов использует статистику уровня столбца при создании плана выполнения запроса. Чтобы повысить производительность запросов, важно создать статистику по отдельным столбцам, особенно столбцам, используемым в объединениях запросов. Создание и обновление статистики не происходит автоматически. [Создайте статистику](/sql/t-sql/statements/create-statistics-transact-sql) после создания таблицы. Обновите статистику после добавления или изменения значительного числа строк. Например, обновите статистику после загрузки. Дополнительные сведения см. в статье [Управление статистикой таблиц в хранилище данных SQL](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Команды для создания таблиц
Вы можете создать пустую таблицу. Вы также можете создать и заполнить таблицу результатами инструкции Select. Ниже приведены команды T-SQL для создания таблицы.

| Инструкция T-SQL | ОПИСАНИЕ |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Создает пустую таблицу, определив все столбцы и параметры таблицы. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) | Создает внешнюю таблицу. Определение таблицы хранится в хранилище данных SQL. Данные таблицы хранятся в хранилище BLOB-объектов Azure или в Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Задает новую таблицу с результатами инструкции Select. Столбцы и типы данных таблицы основаны на результатах инструкции Select. Чтобы импортировать данные, эта инструкция может выбрать данные из внешней таблицы. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Создает новую внешнюю таблицу, экспортируя результаты инструкции Select во внешнее расположение.  Расположением является либо хранилище BLOB-объектов Azure, либо Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Согласование исходных данных с хранилищем данных

Таблицы хранилища данных заполняются путем загрузки данных из другого источника данных. Для успешной загрузки число и типы данных столбцов исходных данных должны совпадать с определением таблицы в хранилище данных. Согласование данных может оказаться самой сложной частью разработки таблиц. 

Если данные поступают из нескольких хранилищ данных, вы можете поместить их в хранилище данных и сохранить их в таблице интеграции. Когда данные находятся в таблице интеграции, вы можете использовать возможности хранилища данных SQL для выполнения операций преобразования. После подготовки данных их можно вставить в рабочие таблицы.

## <a name="unsupported-table-features"></a>Неподдерживаемые функции таблиц
Хранилище данных SQL поддерживает многие, но не все функции таблицы, предлагаемые другими базами данных.  В следующем списке содержатся некоторые функции таблицы, которые не поддерживаются в хранилище данных SQL.

- [Ограничения таблицы](/sql/t-sql/statements/alter-table-table-constraint-transact-sql) PRIMARY KEY, FOREIGN KEYS, UNIQUE, CHECK;

- [Вычисляемые столбцы](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Индексированные представления](/sql/relational-databases/views/create-indexed-views)
- [Последовательность](/sql/t-sql/statements/create-sequence-transact-sql)
- [Разреженные столбцы](/sql/relational-databases/tables/use-sparse-columns)
- [суррогатные ключи]() (реализация посредством [удостоверения](sql-data-warehouse-tables-identity.md));
- [синонимы;](/sql/t-sql/statements/create-synonym-transact-sql)
- [Триггеры](/sql/t-sql/statements/create-trigger-transact-sql)
- [Уникальные индексы](/sql/t-sql/statements/create-index-transact-sql)
- [Пользовательские типы](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Запросы размера таблицы
Простой способ определить пространство и строки, используемые таблицей в каждом из 60 распределений, — применить инструкцию [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Однако возможности команд DBCC достаточно ограничены.  Динамические административные представления (DMV) отображают больше сведений, чем команды DBCC. Начните с создания этого представления.

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

Этот запрос возвращает количество строк и объем каждой таблицы.  Оно позволяет узнать, какие таблицы занимают большего всего места, в каких таблицах применяется циклический перебор, какие таблицы реплицированы, а в каких используется хэш-распределение.  Для таблиц с хэш-распределением запрос показывает столбец распределения.  

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

## <a name="next-steps"></a>Дополнительная информация
После создания таблицы для хранилища данных загрузите данные в таблицу.  Инструкции см. в статье о [загрузке данных в хранилище данных SQL](load-data-wideworldimportersdw.md).