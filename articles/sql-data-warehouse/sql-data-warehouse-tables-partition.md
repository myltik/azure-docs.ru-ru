---
title: Секционирование таблиц в хранилище данных SQL | Документация Майкрософт
description: Начало работы с секционированием таблиц в хранилище данных SQL Azure.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: f94bc3770fbd7e707194032cb99c67b09f8a0618
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Секционирование таблиц в хранилище данных SQL
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

Секционирование поддерживается для всех типов таблиц хранилища данных SQL, включая таблицы с кластеризованным индексом Columnstore, кластеризованным индексом и кучу.  Секционирование также поддерживается для всех типов распределения, включая распределение с помощью хэша или методом циклического перебора.  Путем секционирования данные можно разделить на маленькие группы. В большинстве случаев секционирование осуществляется по столбцу дат.

## <a name="benefits-of-partitioning"></a>Преимущества секционирования
Секционирование позволяет упростить операции обслуживания данных и повысить производительность запросов.  Однако не всегда можно получить оба преимущества. Это зависит от способа загрузки данных и возможности использования одного столбца для обоих целей, так как секционирование может быть выполнено только по одному столбцу.

### <a name="benefits-to-loads"></a>Преимущества для нагрузок
Основное преимущество секционирования в хранилище данных SQL — повышение эффективности и производительности загрузки данных с помощью удаления, переключения и объединения секций.  В большинстве случаев данные секционируются по столбцу даты, тесно связанному с порядком загрузки данных в базу данных.  Одно из наиболее значительных преимуществ использования секций для управления данными — то, что журнал транзакций вести не обязательно.  Хотя намного проще использовать обычные операции вставки, обновления и удаления данных, которые не требуют много внимания и усилий, применение секционирования при загрузке может существенно повысить производительность.

С помощью переключения секций можно быстро удалить или заменить часть таблицы.  Например, таблица фактов по продажам может содержать только данные за последние 36 месяцев.  В конце каждого месяца данные о продажах за самый давний месяц удаляются из таблицы.  Удалить эти данные можно с использованием инструкции DELETE.  Тем не менее построчное удаление большого объема данных с помощью инструкции DELETE может длиться слишком долго, а также создать риск больших транзакций, для отката которых в случае неполадки также понадобится много времени.  Более оптимальный подход — удалить самую давнюю секцию данных.  Если удаление отдельных строк может занять несколько часов, то удаление всей секции — несколько секунд.

### <a name="benefits-to-queries"></a>Преимущества для запросов
С помощью секционирования также можно повысить производительность запросов.  Запрос, который применяет фильтр к секционированным данным, может ограничить сканирование только соответствующими секциями. Этот метод фильтрации позволяет не сканировать всю таблицу, а проверить только небольшое подмножество данных. С появлением кластеризованных индексов Columnstore преимущества производительности исключений предикатов менее существенны, но в некоторых случаях они могут быть эффективны для запросов.  Например, если таблица фактов по продажам разделена на 36 месяцев с помощью поля дат продаж, запросы, выполняющие фильтрацию на основе даты продажи, могут пропускать секции, которые не соответствуют условиям фильтра.

## <a name="partition-sizing-guidance"></a>Руководство по определению размеров секции
Хотя секционирование повышает производительность в ряде сценариев, в некоторых случаях создание таблицы со **слишком большим количеством** секций может повлечь снижение производительности.  В частности, это свойственно для таблиц с кластеризированными индексами Columnstore.  Чтобы секционирование было эффективным, нужно понимать, когда следует его использовать и сколько секций необходимо создать.  Не существует строгих правил в отношении количества секций. Выбор зависит от характера данных и числа секций, к которым одновременно применяется нагрузка.  Правильно составленная схема секционирования обычно содержит от нескольких десятков до нескольких сотен секций (но не тысяч).

При секционировании таблиц с **кластеризованными индексами columnstore** важно учитывать, сколько строк будет содержать каждая секция.  Для оптимального сжатия и производительности таких таблиц требуется как минимум 1 миллион строк на одно распределение и одну секцию.  Еще до создания секций хранилище данных SQL делит каждую таблицу на 60 распределенных баз данных.  Таким образом, секционирование таблицы выступает дополнением к распределениям, созданным в фоновом режиме.  Вернемся к нашему примеру. Если таблица фактов по продажам содержит 36 секций по месяцам, а хранилище данных SQL содержит 60 распределений, то за каждый месяц в таблице должно содержаться 60 миллионов строк, то есть 2,1 миллиарда строк при заполнении данных за все месяцы.  Если в таблице намного меньше строк, чем рекомендуемый минимум на одну секцию, уменьшите количество секций, чтобы на каждую из них приходилось больше строк.  Ознакомьтесь также со статьей об [индексировании][Index]. В ней рассматриваются запросы, которые можно выполнять в хранилище данных SQL, чтобы оценить качество кластеризованных индексов columnstore.

## <a name="syntax-difference-from-sql-server"></a>Отличие синтаксиса от SQL Server
При помощи хранилища данных SQL определять секции проще, чем с использованием SQL Server.  В хранилище данных SQL функции и схемы секционирования используются не так, как в SQL Server.  Вам нужно только определить секционированный столбец и граничные точки.  Хотя синтаксис секционирования может немного отличаться от синтаксиса SQL Server, основные понятия одинаковые.  В SQL Server и хранилище данных SQL поддерживается по одному секционированному столбцу на таблицу, которую можно секционировать по диапазонам.  Дополнительные сведения о секционировании см. в статье [Секционированные таблицы и индексы][Partitioned Tables and Indexes].

В приведенном ниже примере секционирования хранилища данных SQL с помощью инструкции [CREATE TABLE][CREATE TABLE] таблица FactInternetSales разбивается по столбцу OrderDateKey.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Перенос секционирования из SQL Server
Чтобы перенести определения секций SQL Server в хранилище данных SQL, нужно сделать следующее:

* удалить [схему секционирования][partition scheme] SQL Server;
* добавить определение [функции секционирования][partition function] в инструкцию CREATE TABLE.

Если вы переносите секционированную таблицу из экземпляра SQL Server, количество строк в каждой секции можно запросить с помощью приведенного ниже алгоритма SQL.  Обратите внимание: если в хранилище данных SQL используется такая же степень детализации секционирования, то количество строк в секции уменьшится в 60 раз.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Управление рабочей нагрузкой
Последнее, что необходимо учитывать при принятии решения о секционировании таблицы, это [управление рабочими нагрузками][workload management].  Управление рабочими нагрузками в хранилище данных SQL — это главным образом управление памятью и параллелизмом.  В хранилище данных SQL максимальный объем памяти, выделяемой для каждого распределения во время выполнения запроса, регулируется классами ресурсов.  Теоретически размер секций определяется с учетом других факторов, таких как объем памяти, необходимый для создания кластеризованных индексов columnstore.  Кластеризованные индексы Columnstore наиболее эффективны, когда для них выделяется больший объем памяти.  Поэтому необходимо убедиться, что в перестроенном индексе секции достаточно памяти. Увеличить объем памяти для запроса можно, переключившись с роли по умолчанию (smallrc) на другую роль, например largerc.

Информацию о выделении памяти для каждого распределения можно получить с помощью запроса к динамическим административным представлениям Resource Governor. Фактический размер временно предоставляемого буфера памяти всегда будет меньше, чем результат предложенного ниже запроса. Но этот запрос дает вам достаточно информации, чтобы вычислить размер секций для операций управления данными.  Старайтесь избегать изменения размеров секций за пределы выделенной памяти, предоставляемой классом очень больших ресурсов. Если размер секций превышает эту цифру, возникает риск нехватки памяти, что, в свою очередь, приводит к менее оптимальному сжатию.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Переключение секций
Хранилище данных SQL поддерживает разбиение, слияние и переключение секций. Для каждой из этих функций используется инструкция [ALTER TABLE][ALTER TABLE].

Для переключения секций между двумя таблицами необходимо убедиться, что секции выровнены по соответствующим границам и их определения таблиц совпадают. Вы не можете установить диапазон значений в таблице с помощью ограничений CHECK, а значит, исходная таблица должна иметь такие же границы секций, как целевая. Если границы не будут совпадать, переключение секций завершится ошибкой из-за невозможности синхронизировать метаданные.

### <a name="how-to-split-a-partition-that-contains-data"></a>Как разделить секцию, которая содержит данные
Наиболее эффективный способ разделения секции, которая уже содержит данные, — использовать инструкцию `CTAS` . Если секционированная таблица является кластеризованным индексом columnstore, то разделять в такой таблице можно только пустые секции.

В приведенном ниже примере создается секционированная таблица columnstore. Затем в каждую секцию вставляется одна строка:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> Создавая объект статистики, мы обеспечиваем более точные метаданные таблицы. Если вы пропустите создание статистики, хранилище данных SQL будет использовать значения по умолчанию. Сведения об управлении статистикой таблиц см. в [этой статье][statistics].
> 
> 

Следующий запрос позволяет найти количество строк с помощью представления каталога `sys.partitions`:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Следующая команда разделения завершится ошибкой:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Сообщение 35346, уровень 15, состояние 1, строка 44. Использование предложения SPLIT в инструкции ALTER PARTITION привело к ошибке, поскольку секция непустая.  Если для таблицы существует индекс columnstore, разделить можно только пустые секции. Рекомендуется отключить индекс columnstore перед выполнением инструкции ALTER PARTITION, а после завершения ALTER PARTITION перестроить индекс columnstore.

Можно создать новую таблицу для хранения данных при помощи `CTAS`.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Так как границы этих секций выровнены, переключение допускается. Так вы получите пустую секцию в исходной таблице, чтобы впоследствии разделить ее.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Теперь нам осталось только выровнять данные по новым границам секции, используя `CTAS`, и переключить данные на основную таблицу.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Когда вы завершите перемещение данных, рекомендуем обновить статистику для целевой таблицы. Благодаря обновлению статистика будет точно отражать новое распределение данных по секциям.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Система управления версиями секционирования таблиц
Чтобы избежать **повреждения** определения таблицы в системе управления версиями, попробуйте применить следующий подход:

1. Создайте таблицу как секционированную, но без значений секций.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. `SPLIT` таблицы в процессе развертывания:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

При таком подходе код остается статическим в системе управления версиями, а значения границ секционирования могут быть динамическими и со временем развиваться вместе с хранилищем.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Overview], [типам данных таблиц][Data Types], [распределению таблицы][Distribute], [индексированию таблицы][Index], [управлению статистикой таблиц][Statistics] и [временным таблицам][Temporary].  Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL Azure][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./resource-classes-for-workload-management.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
