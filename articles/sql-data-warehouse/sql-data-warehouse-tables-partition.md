---
title: "Секционирование таблиц в хранилище данных SQL | Документация Майкрософт"
description: "Начало работы с секционированием таблиц в хранилище данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0d2ff3ce90c355ba63f3fb66982baa621091ae6e
ms.lasthandoff: 12/08/2016


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
Основное преимущество секционирования в хранилище данных SQL — повышение эффективности и производительности загрузки данных с помощью удаления, переключения и объединения секций.  В большинстве случаев данные секционируются по столбцу даты, тесно связанному с последовательностью загрузки данных в базу данных.  Одно из наиболее значительных преимуществ использования секций для управления данными — то, что журнал транзакций вести не обязательно.  Хотя намного проще использовать обычные операции вставки, обновления и удаления данных, которые не требуют много внимания и усилий, применение секционирования при загрузке может существенно повысить производительность.

С помощью переключения секций можно быстро удалить или заменить часть таблицы.  Например, таблица фактов по продажам может содержать только данные за последние 36 месяцев.  В конце каждого месяца данные о продажах за самый давний месяц удаляются из таблицы.  Удалить эти данные можно с использованием инструкции DELETE.  Тем не менее построчное удаление большого объема данных с помощью инструкции DELETE может занять очень много времени, а также создать риск больших транзакций, для отката которых в случае неполадки также понадобится продолжительное время.  Более оптимальный подход — просто удалить самую давнюю секцию данных.  Если удаление отдельных строк может занять несколько часов, то удаление всей секции — несколько секунд.

### <a name="benefits-to-queries"></a>Преимущества для запросов
С помощью секционирования также можно повысить производительность запросов.  Если при выполнении запроса к секционированному столбцу применяется фильтр, проверка ограничивается только подходящими секциями, которые могут составлять небольшое подмножество данных. Таким образом, вся таблица не будет отсканирована.  С появлением кластеризованных индексов Columnstore преимущества производительности исключений предикатов менее существенны, но в некоторых случаях они могут быть эффективны для запросов.  Например, если таблица фактов по продажам разделена на 36 месяцев с помощью поля дат продаж, запросы, выполняющие фильтрацию на основе даты продажи, могут пропускать секции, которые не соответствуют условиям фильтра.

## <a name="partition-sizing-guidance"></a>Руководство по определению размеров секции
Хотя секционирование повышает производительность в ряде сценариев, в некоторых случаях создание таблицы со **слишком большим количеством** секций может повлечь снижение производительности.  В частности, это свойственно для таблиц с кластеризированными индексами Columnstore.  Чтобы секционирование было эффективным, нужно понимать, когда следует его использовать и сколько секций необходимо создать.  Нет твердо установленного правила, регламентирующего количество секций. Это зависит от данных и числа секций, в которые одновременно выполняется загрузка.  Однако рекомендуется добавлять десятки или сотни секций, а не тысячи.

При секционировании таблиц с **кластеризованными индексами columnstore** важно учитывать, сколько строк будет содержать каждая секция.  Для оптимального сжатия и производительности таких таблиц требуется как минимум 1 миллион строк на одно распределение и одну секцию.  Еще до создания секций хранилище данных SQL делит каждую таблицу на 60 распределенных баз данных.  Таким образом, секционирование таблицы выступает дополнением к распределениям, созданным в фоновом режиме.  Вернемся к нашему примеру. Если таблица фактов по продажам содержит 36 секций по месяцам, а хранилище данных SQL содержит 60 распределений, то за каждый месяц в таблице должно содержаться 60 миллионов строк, то есть 2,1 миллиарда строк при заполнении данных за все месяцы.  Если количество строк в таблице гораздо меньше рекомендуемого минимума на одну секцию, следует использовать меньше секций, чтобы увеличить количество строк в каждой из них.  Ознакомьтесь также со статьей об [индексировании][Index], в которой рассматриваются запросы, которые можно выполнять в хранилище данных SQL, чтобы оценить качество кластеризованных индексов columnstore.

## <a name="syntax-difference-from-sql-server"></a>Отличие синтаксиса от SQL Server
В хранилище данных SQL применяется упрощенное определение секций, которое несколько отличается от аналогичного определения в SQL Server.  В хранилище данных SQL функции и схемы секционирования используются не так, как в SQL Server.  Вам нужно только определить секционированный столбец и граничные точки.  Хотя синтаксис секционирования может немного отличаться от синтаксиса SQL Server, основные понятия одинаковые.  В SQL Server и хранилище данных SQL поддерживается по одному секционированному столбцу на таблицу, которую можно секционировать по диапазонам.  Дополнительные сведения о секционировании см. в статье [Секционированные таблицы и индексы][Partitioned Tables and Indexes].

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

Если вы переносите секционированную таблицу из экземпляра SQL Server, количество строк в каждой секции можно запросить с помощью приведенного ниже алгоритма SQL.  Имейте в виду, что если в хранилище данных SQL используется такая же степень детализации секционирования, то количество строк в секции уменьшится в 60 раз.  

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
Последнее, что необходимо учитывать при принятии решения о секционировании таблицы, это [управление рабочими нагрузками][workload management].  Управление рабочими нагрузками в хранилище данных SQL — это главным образом управление памятью и параллелизмом.  В хранилище данных SQL максимальный объем памяти, выделяемой для каждого распределения во время выполнения запроса, регулируется классами ресурсов.  Теоретически размер секций будет определен с учетом других факторов, таких как объем памяти, необходимый для создания кластеризованных индексов Columnstore.  Кластеризованные индексы Columnstore наиболее эффективны, когда для них выделяется больший объем памяти.  Поэтому необходимо убедиться, что при перестроении индекса секции отсутствует нехватка памяти. Увеличить объем памяти для запроса можно, переключившись с роли по умолчанию (smallrc) на другую роль, например largerc.

Информацию о выделении памяти для каждого распределения можно получить с помощью запроса к динамическим административным представлениям регулятора ресурсов. На самом деле выделение памяти будет меньше, чем цифры ниже. Информация приведена на уровне рекомендации, ее можно использовать при планировании размера секций для операций управления данными.  Старайтесь избегать изменения размеров секций за пределы выделенной памяти, предоставляемой классом очень больших ресурсов. Если размер секций превышает эту цифру, возникает риск нехватки памяти, что, в свою очередь, приводит к менее оптимальному сжатию.

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

Для переключения секций между двумя таблицами необходимо убедиться, что секции выровнены по соответствующим границам и их определения таблиц совпадают. Не допускаются ограничения CHECK для задания диапазона значений в таблице, исходная таблица должна содержать те же границы секций, что и целевая таблица. Если это не так, переключение секций завершится ошибкой, так как метаданные не будут синхронизированы.

### <a name="how-to-split-a-partition-that-contains-data"></a>Как разделить секцию, которая содержит данные
Наиболее эффективный способ разделения секции, которая уже содержит данные, — использовать инструкцию `CTAS` . Если секционированная таблица является кластеризованным индексом columnstore, то секция таблицы должна быть пустой, прежде чем она может быть разделена.

Ниже приведен пример секционированной таблицы columnstore, содержащей по одной строке в каждой секции:

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
> Создавая объект статистики, мы обеспечиваем более точные метаданные таблицы. Если пропустить создание объекта статистики, хранилище данных SQL будет использовать значения по умолчанию. Сведения об управлении статистикой таблиц в хранилище данных SQL см. в [этой статье][statistics].
> 
> 

Далее мы можем выполнить запрос числа строк, воспользовавшись представлением каталога `sys.partitions` .

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

Если мы попытаемся разделить эту таблицу, появится сообщение об ошибке:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Сообщение 35346, уровень 15, состояние 1, строка 44. Использование предложения SPLIT в инструкции ALTER PARTITION привело к ошибке, поскольку секция непустая.  Если для таблицы существует индекс columnstore, разделить можно только пустые секции. Рекомендуется отключить индекс columnstore перед выполнением инструкции ALTER PARTITION, а после завершения ALTER PARTITION перестроить индекс columnstore.

Однако можно использовать `CTAS` , чтобы создать новую таблицу для хранения наших данных.

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

Так как границы секций выровнены, переключение разрешено. Это позволит оставить исходную таблицу с пустым разделом, который мы впоследствии сможем разделить.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Все, что осталось сделать, — выровнять наши данные по новым границам секции границы с помощью `CTAS` и переключить данные обратно в основную таблицу.

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

После завершения перемещение данных рекомендуется обновить статистику для целевой таблицы, чтобы убедиться, что она точно отражает новое распределение данных в соответствующих разделах:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Система управления версиями секционирования таблиц
Чтобы избежать **порчи** определения таблицы в системе управления версиями, можно рассмотреть следующий метод:

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

## <a name="next-steps"></a>Дальнейшие действия
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
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->

