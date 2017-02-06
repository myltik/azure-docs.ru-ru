---
title: "Управление статистикой таблиц в хранилище данных SQL | Документация Майкрософт"
description: "Начало работы со статистикой таблиц в хранилище данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b2b99ec031ea26b4ab19e7327da035788661a0a8


---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Управление статистикой таблиц в хранилище данных SQL
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

Чем больше хранилищу данных SQL известно о данных, тем быстрее оно выполняет запросы, связанные с данными.  Передать в хранилище данных SQL сведения о данных можно путем сбора их статистики.  Сбор статистики данных — один из самых важных факторов, позволяющих оптимизировать запросы.  При наличии статистики хранилище данных SQL может создать оптимальный план для запросов.  Это связано с тем, что оптимизатор запросов хранилища данных SQL основан на стоимости.  То есть он сравнивает стоимость разных планов запросов, а затем выбирает план с наименьшей стоимостью, который также будет быстрее выполняться.

Статистику можно создать по одному или нескольким столбцам, а также на основе индекса таблицы.  Статистика хранится в виде гистограммы, в которой указан диапазон и избирательность значений.  Это особенно интересно, когда оптимизатор должен оценить предложения JOIN, GROUP BY, HAVING и WHERE в запросе.  Например, если по оценке оптимизатора при использовании одной даты, заданной для фильтрации в запросе, вернется 1 строка, а второй — 1 миллион строк, в обоих случаях оптимизатор выберет совершенно разные планы.  Крайне важно, чтобы создаваемая статистика *точно* отображала текущее состояние таблицы.  Наличие актуальной статистики гарантирует, что оптимизатор выберет подходящий план.  Планы, созданные с помощью оптимизатора, зависят от статистики данных.

В настоящее время процесс создания и обновления статистики выполняется вручную. Тем не менее он очень простой.  В SQL Server эта процедура выполняется иначе — статистика автоматически создается и обновляется по отдельным столбцам и индексам.  С помощью приведенных ниже сведений можно значительно автоматизировать управление статистикой данных. 

## <a name="getting-started-with-statistics"></a>Начало работы со статистикой
 Проще всего приступить к работе со статистикой, создав статистику для каждого столбца.  Так актуальность статистики — очень важный фактор, рекомендуется обновлять ее ежедневно или после каждой загрузки. Однако всегда есть компромиссы между производительностью и затратами на создание и обновление статистики.  Если ведение статистики занимает слишком много времени, нужно выбирать отдельные столбцы, для которых необходимо создавать статистику, или столбцы, требующие частого обновления статистики.  Например, вместо обновления после каждой загрузки можно ежедневно обновлять столбцы дат, когда добавляются новые значения. Более того, статистику рекомендуется вести для столбцов, которые используются в предложениях JOIN, GROUP BY, HAVING и WHERE.  Если таблица содержит большое количество столбцов, используемых только в предложении SELECT, статистика для этих столбцов может оказаться бесполезной. Лучше потратить немного больше времени и определить те столбцы, статистика в которых будет полезной. К тому же таким образом можно сократить время на ведение статистики.

## <a name="multi-column-statistics"></a>Многостолбцовая статистика
Помимо создания статистики по отдельным столбцам для запросов может также оказаться статистика по нескольким столбцам.  Многостолбцовая статистика — это статистика, созданная по списку столбцов.  В первом столбце списка находится одностолбцовая статистика, а также некоторые сведения о корреляции между столбцами (т. н. плотность).  Например, при наличии таблицы, которая присоединяется к двум столбцам другой таблицы, может оказаться, что хранилище данных SQL может оптимизировать план, если ему известно о связи между двумя столбцами.   Многостолбцовая статистика может повысить производительность запросов для некоторых операций, например составных соединений и группировки.

## <a name="updating-statistics"></a>Обновление статистики
Обновление статистики — это важная часть процедуры управления базой данных.  При изменении распределения данных в базе данных статистику необходимо обновить.  Устаревшая статистика приведет к недостаточной производительности запросов.

Лучше всего обновлять статистику в столбцах дат каждый день, когда добавляются новые даты.  При каждой загрузке строк в хранилище данных добавляются новые даты загрузки или даты транзакций. Это изменяет распределение данных и делает статистику устаревшей. И наоборот, статистика по столбцу страны в таблице клиентов может никогда не обновляться, поскольку распределение значений существенно не меняется. Если предположить, что распределение между клиентами постоянно, добавление новых строк в вариант таблицы не изменит распределение данных. Тем не менее, если хранилище данных содержит только одну страну и появляются данные из новой страны, что означает хранение данных из нескольких стран, статистику по столбцу страны необходимо будет обновить.

Один из первых вопросов, задаваемых при устранения неполадок запроса: "Актуальна ли статистика?"

На этот вопрос нельзя ответить, исходя только из возраста данных. Актуальный объект статистики может быть очень старым, если исходные данные существенно не менялись. Если количество строк или распределение значений для заданного столбца значительно изменяется, вот *тогда* необходимо обновить статистику.  

Для справки **SQL Server** (не хранилище данных SQL) автоматически обновляет статистику в следующих случаях:

* Если в таблице есть нулевые строки, при добавлении новых строк статистика обновится автоматически.
* При добавлении более 500 строк в таблицу, изначально содержащую менее 500 строк (например, если у вас было 499 строк, а после добавления 500 строк в ней стало 999 строк), будет выполнено автоматическое обновление. 
* Если в таблице больше 500 строк, статистика будет обновляться автоматически, как только вы добавите еще 500 строк + 20 % размера таблицы.

В отсутствие динамического административного представления контроль изменения данных в таблице с момента последнего обновления по известному возрасту статистики позволяет получить только часть картины.  Можно использовать следующий запрос, чтобы определить время последнего обновления статистики в каждой таблице.  

> [!NOTE]
> Помните, если распределение значений для заданного столбца существенно изменилось, статистику необходимо обновить независимо от времени ее последнего обновления.  
> 
> 

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Например, для столбцов дат в хранилище данных обычно требуется часто обновлять статистику. При каждой загрузке строк в хранилище данных добавляются новые даты загрузки или даты транзакций. Это изменяет распределение данных и делает статистику устаревшей.  И наоборот, статистика по столбцу пола в таблице клиентов может никогда не обновляться. Если предположить, что распределение между клиентами постоянно, добавление новых строк в вариант таблицы не изменит распределение данных. Тем не менее, если хранилище данных содержит только один пол, а новые требования предписывают несколько полов, вам определенно надо обновить статистику по столбцу пола.

Подробные пояснения см. в статье, посвященной [управлению статистикой][Statistics], на сайте MSDN.

## <a name="implementing-statistics-management"></a>Реализация управления статистикой
Часто рекомендуется расширить процесс загрузки данных, чтобы обеспечить обновление статистики в конце загрузки. Загрузка данных происходит, когда таблицы часто меняют свой размер и (или) распределение значений. Поэтому логично реализовать некоторые процессы управления.

Ниже приведены некоторые основные принципы обновления статистики в процессе загрузки:

* Убедитесь, что для каждой загружаемой таблицы обновляется по крайней мере один объект статистики. Тогда при обновлении статистики обновляется информация о размере таблицы (число строк и страниц).
* Сосредоточьтесь на столбцах, участвующие в предложениях JOIN, GROUP BY, ORDER BY и DISTINCT.
* Рекомендуется чаще обновлять столбцы «с возрастающим порядком ключа», например даты транзакций, потому что эти значения не будут включены в гистограмму статистики.
* Рекомендуется реже обновлять столбцы со статическим распределением.
* Помните, что каждый объект статистики обновляется последовательно. Просто реализовать `UPDATE STATISTICS <TABLE_NAME>` может быть не идеальным решением, особенно для обширных таблиц с большим количеством объектов статистики.

> [!NOTE]
> Более подробную информацию о [возрастающем порядке ключа] можно найти в техническом документе модели оценки количества элементов SQL Server 2014.
> 
> 

Подробные пояснения см. в статье [Оценка количества элементов (SQL Server)][Cardinality Estimation] на сайте MSDN.

## <a name="examples-create-statistics"></a>Примеры: создание статистики
Эти примеры показывают, как использовать различные параметры для создания статистики. Параметры, которые можно использовать для каждого столбца, зависят от характеристик данных и того, как столбец будет использован в запросах.

### <a name="a-create-single-column-statistics-with-default-options"></a>О. Создание одностолбцовой статистики с параметрами по умолчанию
Чтобы создать одностолбцовую статистику, достаточно просто указать имя объекта статистики и имя столбца.

В этом синтаксисе все параметры используются по умолчанию. По умолчанию хранилище данных SQL создает выборку из 20 процентов таблицы, когда создает статистику.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Например:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Создание одностолбцовой статистики путем проверки всех строк
В большинстве случаев достаточно использовать частоту выборки по умолчанию, 20 процентов. Однако вы можете настроить частоту выборки.

Для выборки всей таблицы используйте следующий синтаксис:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Например:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Создание одностолбцовой статистики с указанием размера выборки
В качестве альтернативы можно указать размер выборки в процентах:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Создание одностолбцовой статистики только для некоторых строк
Еще один вариант: можно создать статистику для части строк в таблице. Это называется отфильтрованной статистикой.

Например, отфильтрованную статистику можно использовать при планировании запроса определенной секции большой секционированной таблицы. Создавая статистику только по значениям секции, можно повысить точность статистики и, таким образом, увеличить производительность запросов.

Этот пример создает статистику по диапазону значений. Легко определить значения для сопоставления с диапазоном значений в секции.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Чтобы оптимизатор запросов рассмотрел возможность использования отфильтрованной статистики, когда выбирает план распределенного запроса, запрос должен быть в пределах определения объекта статистики. Если взять приведенный выше пример, предложение WHERE запроса должно указать значения col1 от 2000101 до 20001231.
> 
> 

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Создание одностолбцовой статистики со всеми параметрами
Разумеется, параметры можно комбинировать. В приведенный ниже пример создает отфильтрованный объект статистики с настраиваемым размером выборки:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Полные справочные сведения см. в статье [CREATE STATISTICS (Transact-SQL)][CREATE STATISTICS] на сайте MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Создание многостолбцовой статистики
Для создания многостолбцовой статистики просто используйте предыдущие примеры, но укажите больше столбцов.

> [!NOTE]
> Гистограмма, используемая для оценки количества строк в результатах запроса, доступна только для первого столбца, указанного в определении объекта статистики.
> 
> 

В этом примере гистограмма создана для *product\_category*. Статистика между столбцами вычисляется по *product\_category* и *product\_sub_c\ategory*.

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Так как между *product\_category* и *product\_sub\_category* настроена корреляция, многостолбцовая статистика может быть полезна, когда к этим столбцам обращаются одновременно.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>Ж. Создание статистики для всех столбцов в таблице
Один из способов создать статистику — выполнить команды CREATE STATISTICS после создания таблицы.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>З. Использование хранимой процедуры для создания статистики для всех столбцов в базе данных
В хранилище данных SQL нет системной хранимой процедуры, эквивалентной [sp_create_stats][] в SQL Server. Эта хранимая процедура создает объект одностолбцовой статистики для каждого столбца базы данных, для которого статистики еще нет.

Это поможет приступить к проектированию базы данных. Вы можете адаптировать код в соответствии со своими нуждами.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Чтобы создать статистику для всех столбцов в таблице с помощью этой процедуры, просто вызовите ее.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Примеры: обновление статистики
Чтобы обновить статистику, можно:

1. Обновить один объект статистики. Указать имя объекта статистики, который вы хотите обновить.
2. Обновить все объекты статистики для таблицы. Указать имя таблицы, а не один объект статистики.

### <a name="a-update-one-specific-statistics-object"></a>О. Обновление одного указанного объекта статистики
Для обновления указанного объекта статистики используйте следующий синтаксис:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Например:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Обновляя определенные объекты статистики, можно свести к минимуму затраты времени и ресурсов на управление статистикой. Однако необходимо хорошенько подумать, чтобы выбрать наиболее подходящие объекты статистики для обновления.

### <a name="b-update-all-statistics-on-a-table"></a>B. Обновление всей статистики для таблицы
Ниже показан простой метод обновления всех объектов статистики для таблицы.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Например:

```sql
UPDATE STATISTICS dbo.table1;
```

Эта инструкция проста в использовании. Просто помните, что она обновляет всю статистику для таблицы, и, следовательно, может выполнять больше работы, чем необходимо. Если производительность не является проблемой — это самый простой и эффективный способ обеспечения актуальности статистики.

> [!NOTE]
> При обновлении всей статистики для таблицы хранилище данных SQL осуществляет сканирование, чтобы выполнить выборку из таблицы для каждой статистики. Если таблица большого размера, содержит много столбцов и статистики, может оказаться эффективнее обновлять отдельные данные статистики по необходимости.
> 
> 

Реализацию процедуры `UPDATE STATISTICS` см. в статье о [временных таблицах][Temporary]. Метод реализации слегка отличается от описанной выше процедуры `CREATE STATISTICS`, но результат одинаков.

Полный синтаксис см. в статье [UPDATE STATISTICS (Transact-SQL)][Update Statistics] на сайте MSDN.

## <a name="statistics-metadata"></a>Метаданные статистики
Существует несколько системных представлений и функций, которые можно использовать для поиска информации о статистике. Например, можно узнать, устарел ли объект статистики, воспользовавшись функцией stats-date, чтобы посмотреть, когда статистика была в последний раз создана или обновлена.

### <a name="catalog-views-for-statistics"></a>Представления каталога для статистики
Вот какие системные представления показывают информацию о статистике:

| Представление каталога | Описание |
|:--- |:--- |
| [sys.columns][sys.columns] |Одна строка для каждого столбца. |
| [sys.objects][sys.objects] |Одна строка для каждого объекта в базе данных. |
| [sys.schemas][sys.schemas] |Одна строка для каждой схемы в базе данных. |
| [sys.stats][sys.stats] |Одна строка для каждого объекта статистики. |
| [sys.stats_columns][sys.stats_columns] |Одна строка для каждого столбца в объекте статистики. Ссылается на sys.columns. |
| [sys.tables][sys.tables] |Одна строка для каждой таблицы (включая внешние таблицы). |
| [sys.table_types][sys.table_types] |Одна строка для каждого типа данных. |

### <a name="system-functions-for-statistics"></a>Системные функции для статистики
Эти системные функции полезны для работы со статистикой:

| Системная функция | Описание |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |Дата последнего обновления объекта статистики. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Предоставляет сводную и подробную информацию о распределении значений согласно объекту статистики. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Сочетание столбцов и функций статистики в одном представлении
Это представление содержит столбцы, относящиеся к статистике, и результаты функции [STATS_DATE()][].

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Примеры DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() отображает данные, хранящиеся в объекте статистики. Эти данные состоят из трех частей:

1. Заголовок
2. вектор плотности;
3. Гистограмма

Заголовок метаданных о статистике. Гистограмма отображает распределение значений в первом ключевом столбце объекта статистики. Вектор плотности измеряет корреляцию между столбцами. Хранилище данных SQL вычисляет оценку количества элементов с помощью данных в объекте статистики.

### <a name="show-header-density-and-histogram"></a>Отображение заголовка, плотности и гистограммы
Это простой пример показывает все три части объекта статистики.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Например:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Отображение одной или нескольких частей DBCC SHOW_STATISTICS()
Если вы заинтересованы только в просмотре определенных частей, используйте предложение `WITH` и укажите, какие части требуется показать:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Например:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Отличия DBCC SHOW_STATISTICS()
В хранилище данных SQL используется более строгая реализация DBCC SHOW_STATISTICS(), чем в SQL Server.

1. Недокументированные возможности не поддерживаются.
2. Нельзя использовать Stats_stream.
3. Нельзя соединить результаты для определенных подмножеств данных статистики, например (STAT_HEADER JOIN DENSITY_VECTOR).
4. Невозможно задать NO_INFOMSGS для подавления сообщений.
5. Нельзя использовать квадратные скобки вокруг имен статистики.
6. Нельзя использовать имена столбцов для идентификации объектов статистики.
7. Пользовательская ошибка 2767 не поддерживается.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в статье [Инструкция DBCC SHOW_STATISTICS (Transact-SQL)][DBCC SHOW_STATISTICS] на сайте MSDN.  Дополнительные сведения см. в статьях, посвященным [общим сведениям о таблицах][Overview], [типам данных таблиц][Data Types], [распределению][Distribute], [индексированию][Index] и [секционированию таблиц][Partition], а также [временным таблицам][Temporary].  Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL Azure][SQL Data Warehouse Best Practices].  

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

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  



<!--HONumber=Dec16_HO2-->


