---
title: "Управление статистикой таблиц в хранилище данных SQL | Документация Майкрософт"
description: "Начало работы со статистикой таблиц в хранилище данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 11/06/2017
ms.author: barbkess
ms.openlocfilehash: b007e1894f163d50dbf31e3c09b4b5ff329adb59
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2018
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

Чем больше хранилищу данных SQL Azure известно о данных, тем быстрее оно выполняет запросы, связанные с ними. Сбор статистики данных и ее загрузка в хранилище данных SQL — один из самых важных факторов, позволяющих оптимизировать запросы. Это связано с тем, что оптимизатор запросов хранилища данных SQL основан на стоимости. Он сравнивает стоимость разных планов запросов, а затем выбирает план с наименьшей стоимостью, который в большинстве случаев является самым быстро выполнимым. Например, если по оценке оптимизатора при использовании одной даты, заданной для фильтрации в запросе, вернется одна строка, а второй — 1 миллион строк, то оптимизатор может выбрать разные планы.

В настоящее время процесс создания и обновления статистики выполняется вручную. Тем не менее он простой.  Скоро можно будет автоматически создавать и обновлять статистику по отдельным столбцам и индексам.  С помощью приведенных ниже сведений можно значительно автоматизировать управление статистикой данных. 

## <a name="getting-started-with-statistics"></a>Начало работы со статистикой
Проще всего приступить к работе со статистикой, создав статистику для каждого столбца. Устаревшая статистика приводит к неоптимальной производительности запросов. Однако по мере роста данных обновление статистики по всем столбцам может потреблять память. 

Ниже приведены рекомендации для различных сценариев.
| **Сценарий** | Рекомендации |
|:--- |:--- |
| **Приступая к работе** | Обновите все столбцы после перехода на хранилище данных SQL |
| **Столбец, который наиболее важен для статистики** | Ключ хэш-распределения |
| **Второй по значимости столбец для статистики** | Ключ секции |
| **Другие значимые столбцы для статистики** | "Дата", "Частые соединения", GROUP BY, HAVING и WHERE |
| **Частота обновления статистики**  | Консервативная: ежедневно <br></br> После загрузки или преобразования данных |
| **Выборка** |  Если менее 1 млрд строк, используйте выборку по умолчанию (20 процентов) <br></br> Если более 1 млрд строк, подходит статистика по диапазону 2 % |

## <a name="updating-statistics"></a>Обновление статистики

Лучше всего обновлять статистику в столбцах дат каждый день, когда добавляются новые даты. При каждой загрузке строк в хранилище данных добавляются новые даты загрузки или даты транзакций. Это изменяет распределение данных и делает статистику устаревшей. И наоборот, статистика по столбцу страны в таблице клиентов может никогда не обновляться, потому что распределение значений существенно не меняется. Если предположить, что распределение между клиентами постоянно, добавление новых строк в вариант таблицы не изменит распределение данных. Тем не менее, если хранилище данных содержит только одну страну и появляются данные из новой страны, что означает хранение данных из нескольких стран, статистику по столбцу страны необходимо будет обновить.

Один из первых вопросов, задаваемых при устранении неполадок запроса: **Актуальна ли статистика?**

На этот вопрос нельзя ответить, исходя только из возраста данных. Актуальный объект статистики может быть старым, если исходные данные существенно не менялись. Если количество строк или распределение значений для столбца значительно изменяется, то *в этот момент* необходимо обновить статистику.

Из-за отсутствия динамического административного представления контроль изменения данных в таблице с момента последнего обновления по известному возрасту статистики позволяет получить только часть картины.  Можно использовать следующий запрос, чтобы определить время последнего обновления статистики в каждой таблице.  

> [!NOTE]
> Помните, если распределение значений для столбца существенно изменилось, статистику необходимо обновить, независимо от времени ее последнего обновления.  
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

Например, для **столбцов дат** в хранилище данных обычно требуется часто обновлять статистику. При каждой загрузке строк в хранилище данных добавляются новые даты загрузки или даты транзакций. Это изменяет распределение данных и делает статистику устаревшей.  И наоборот, статистика по столбцу пола в таблице клиентов может никогда не обновляться. Если предположить, что распределение между клиентами постоянно, добавление новых строк в вариант таблицы не изменит распределение данных. Тем не менее, если хранилище данных содержит сведения только о клиентах одного пола, а новые требования предписывают гендерные различия, вам надо обновить статистику по столбцу пола.

Подробные пояснения см. в статье, посвященной [управлению статистикой][Statistics], на сайте MSDN.

## <a name="implementing-statistics-management"></a>Реализация управления статистикой
Часто рекомендуется расширить процесс загрузки данных, чтобы обеспечить обновление статистики в конце загрузки. Загрузка данных происходит, когда таблицы часто меняют свой размер и (или) распределение значений. Поэтому логично реализовать некоторые процессы управления.

Ниже приведены некоторые основные принципы обновления статистики в процессе загрузки:

* Убедитесь, что для каждой загружаемой таблицы обновляется по крайней мере один объект статистики. Тогда при обновлении статистики обновляется информация о размере таблицы (число строк и страниц).
* Сосредоточьтесь на столбцах, участвующих в предложениях JOIN, GROUP BY, ORDER BY и DISTINCT.
* Рекомендуется чаще обновлять столбцы "с возрастающим порядком ключа", например даты транзакций, потому что эти значения не будут включены в гистограмму статистики.
* Рекомендуется реже обновлять столбцы со статическим распределением.
* Помните, что каждый объект статистики обновляется последовательно. Просто реализовать `UPDATE STATISTICS <TABLE_NAME>` может быть не идеальным решением, особенно для обширных таблиц с большим количеством объектов статистики.

Подробные пояснения см. в статье [Оценка количества элементов (SQL Server)][Cardinality Estimation] на сайте MSDN.

## <a name="examples-create-statistics"></a>Примеры: создание статистики
Эти примеры показывают, как использовать различные параметры для создания статистики. Параметры, которые можно использовать для каждого столбца, зависят от характеристик данных и того, как столбец будет использован в запросах.

### <a name="create-single-column-statistics-with-default-options"></a>Создание одностолбцовой статистики с параметрами по умолчанию
Чтобы создать одностолбцовую статистику, достаточно просто указать имя объекта статистики и имя столбца.

В этом синтаксисе все параметры используются по умолчанию. По умолчанию хранилище данных SQL создает выборку из **20 процентов** таблицы, когда создает статистику.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Например: 

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Создание одностолбцовой статистики путем проверки всех строк
В большинстве случаев достаточно использовать частоту выборки по умолчанию, 20 процентов. Однако вы можете настроить частоту выборки.

Для выборки всей таблицы используйте следующий синтаксис:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Например: 

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Создание одностолбцовой статистики с указанием размера выборки
В качестве альтернативы можно указать размер выборки в процентах:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Создание одностолбцовой статистики только для некоторых строк
Можно также создать статистику для части строк в таблице. Это называется отфильтрованной статистикой.

Например, отфильтрованную статистику можно использовать при планировании запроса определенной секции большой секционированной таблицы. Создавая статистику только по значениям секции, можно повысить точность статистики и, таким образом, увеличить производительность запросов.

Этот пример создает статистику по диапазону значений. Значения можно легко определить для сопоставления с диапазоном значений в секции.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Чтобы оптимизатор запросов рассмотрел возможность использования отфильтрованной статистики, когда выбирает план распределенного запроса, запрос должен быть в пределах определения объекта статистики. Если взять приведенный выше пример, предложение WHERE запроса должно указать значения col1 от 2000101 до 20001231.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Создание одностолбцовой статистики со всеми параметрами
Можно также комбинировать параметры. В приведенном ниже примере создается отфильтрованный объект статистики с настраиваемым размером выборки:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Полные справочные сведения см. в статье [CREATE STATISTICS (Transact-SQL)][CREATE STATISTICS] на сайте MSDN.

### <a name="create-multi-column-statistics"></a>Создание многостолбцовой статистики
Для создания объекта статистики с несколькими столбцами просто используйте предыдущие примеры, но укажите больше столбцов.

> [!NOTE]
> Гистограмма, используемая для оценки количества строк в результатах запроса, доступна только для первого столбца, указанного в определении объекта статистики.
> 
> 

В этом примере гистограмма создана для *product\_category*. Статистика между столбцами вычисляется по *product\_category* и *product\_*.

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Так как между *product\_category* и *product\_sub\_category* настроена корреляция, объект статистики с несколькими столбцами может быть полезен, когда к этим столбцам обращаются одновременно.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Создание статистики для всех столбцов в таблице
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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Использование хранимой процедуры для создания статистики для всех столбцов в базе данных
В хранилище данных SQL нет системной хранимой процедуры, эквивалентной sp_create_stats в SQL Server. Эта хранимая процедура создает объект одностолбцовой статистики для каждого столбца базы данных, для которого статистики еще нет.

Следующий пример поможет приступить к проектированию базы данных. Вы можете адаптировать код в соответствии со своими потребностями.

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

- Обновить один объект статистики. Указать имя объекта статистики, который вы хотите обновить.
- Обновить все объекты статистики для таблицы. Указать имя таблицы, а не один объект статистики.

### <a name="update-one-specific-statistics-object"></a>Обновление одного указанного объекта статистики
Для обновления указанного объекта статистики используйте следующий синтаксис:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Например: 

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Обновляя определенные объекты статистики, можно свести к минимуму затраты времени и ресурсов на управление статистикой. Однако необходимо хорошенько подумать, чтобы выбрать наиболее подходящие объекты статистики для обновления.

### <a name="update-all-statistics-on-a-table"></a>Обновление всей статистики для таблицы
Ниже показан простой метод обновления всех объектов статистики для таблицы.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Например: 

```sql
UPDATE STATISTICS dbo.table1;
```

Эта инструкция проста в использовании. Просто помните, что она обновляет *всю* статистику для таблицы и, следовательно, может выполнять больше работы, чем необходимо. Если производительность не является проблемой, то это самый простой и эффективный способ гарантировать, что статистика актуальна.

> [!NOTE]
> При обновлении всей статистики для таблицы хранилище данных SQL осуществляет сканирование, чтобы выполнить выборку из таблицы для каждого объекта статистики. Если таблица большого размера и содержит много столбцов и статистики, может оказаться эффективнее обновлять отдельные данные статистики по необходимости.
> 
> 

Реализацию процедуры `UPDATE STATISTICS` см. в статье [Temporary tables in SQL Data Warehouse][Temporary] (Временные таблицы в хранилище данных SQL). Метод реализации слегка отличается от процедуры `CREATE STATISTICS`, описанной выше, но результат одинаков.

Полный синтаксис см. в статье [UPDATE STATISTICS (Transact-SQL)][Update Statistics] на сайте MSDN.

## <a name="statistics-metadata"></a>Метаданные статистики
Существует несколько системных представлений и функций, которые можно использовать для поиска информации о статистике. Например, можно узнать, устарел ли объект статистики, воспользовавшись функцией stats-date, чтобы посмотреть, когда статистика была в последний раз создана или обновлена.

### <a name="catalog-views-for-statistics"></a>Представления каталога для статистики
Вот какие системные представления показывают информацию о статистике:

| Представления каталога | ОПИСАНИЕ |
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

| Системная функция | ОПИСАНИЕ |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |Дата последнего обновления объекта статистики. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Сводная и подробная информация о распределении значений согласно объекту статистики. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Сочетание столбцов и функций статистики в одном представлении
Это представление содержит столбцы, относящиеся к статистике, и результаты функции STATS_DATE().

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

- Заголовок
- Вектор плотности
- Гистограмма

Заголовок метаданных о статистике. Гистограмма отображает распределение значений в первом ключевом столбце объекта статистики. Вектор плотности измеряет корреляцию между столбцами. Хранилище данных SQL вычисляет оценку количества элементов с помощью данных в объекте статистики.

### <a name="show-header-density-and-histogram"></a>Отображение заголовка, плотности и гистограммы
Этот простой пример показывает все три части объекта статистики.

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

- Недокументированные возможности не поддерживаются.
- Нельзя использовать Stats_stream.
- Нельзя соединить результаты для определенных подмножеств данных статистики. Например, (STAT_HEADER JOIN DENSITY_VECTOR).
- Невозможно задать NO_INFOMSGS для подавления сообщений.
- Нельзя использовать квадратные скобки вокруг имен статистики.
- Нельзя использовать имена столбцов для идентификации объектов статистики.
- Пользовательская ошибка 2767 не поддерживается.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в статье [Инструкция DBCC SHOW_STATISTICS (Transact-SQL)][DBCC SHOW_STATISTICS] на сайте MSDN.

  Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Overview], [типам данных таблиц][Data Types], [распределению][Distribute], [индексированию][Index] и [секционированию таблицы][Partition], а также [временным таблицам][Temporary].
  
   Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL Azure][SQL Data Warehouse Best Practices].  

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
