<properties
   pageTitle="Секции таблиц в хранилище данных SQL | Microsoft Azure"
   description="Советы по использованию секций таблиц в хранилище данных SQL Azure для разработки решений."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Секции таблиц в хранилище данных SQL

Чтобы перенести определения секций SQL Server в хранилище данных SQL:

- Удалите функции и схемы секционирования SQL Server, так как это осуществляется при создании таблицы.
- Определите разделы при создании таблицы. Просто укажите граничные точки секции и то, как должна действовать граничная точка: `RANGE RIGHT` или `RANGE LEFT`.

### Изменение размера секции
Хранилище данных SQL предоставляет администратору базы данных на выбор несколько типов таблиц: в виде кучи, с кластеризованным индексом (CI) и кластеризованным индексом хранилища столбцов (CCI). Администратор базы данных может разбить таблицу каждого из этих видов на несколько секций, чтобы повысить производительность. Однако в определенных случаях создание таблицы с чрезмерным количеством секций может снизить производительность или привести к ошибкам запросов. Это особенно актуально для таблиц типа CCI. Администратор базы данных должен понимать, когда следует использовать секционирование и сколько секций необходимо создать, чтобы максимально эффективно использовать эту возможность. Следующие рекомендации помогут администраторам баз данных принять правильные решения в рамках своих сценариев.

Как правило, секционирование таблиц полезно в двух случаях:

1. Использование переключения секций для быстрого урезания раздела таблицы. Таблица фактов со стандартной структурой содержит строки лишь в течение определенного периода времени. Например, таблица фактов по продажам может содержать данные лишь за последние 36 месяцев. В конце каждого месяца данные о продажах за самый давний месяц удаляются из таблицы. Для этого можно просто удалить все строки для нужного месяца, но построчное удаление большого объема данных может занять очень много времени. Чтобы оптимизировать работу этого сценария, хранилище данных SQL поддерживает переключение секций, позволяющее удалить целую группу строк в секции в рамках одной быстрой операции.   

2. Секцинирование позволяет без труда исключать из запросов обработку больших групп строк (то есть секций), если запрос помещает на нужный столбец секции предикат. Например, если таблица фактов по продажам разделена на 36 месяцев с помощью поля дат продаж, запросы, выполняющие фильтрацию на основе даты продажи, могут пропускать секции, не соответствующие условиям фильтра. По сути, используемое таким образом секционирование представляет собой индекс низкой точности.

При создании кластеризованных индексов хранилища столбцов в хранилище данных SQL администратору базы данных следует учесть дополнительный фактор: номер строки. В таблицах типа CCI можно добиться высокой степени сжатия и помочь хранилищу данных SQL увеличить производительность запросов. В силу внутренних особенностей сжатия в хранилище данных SQL, каждая секция в таблице типа CCI должна содержать значительное количество строк перед сжатием данных. Кроме того, хранилище данных SQL распределяет данные по множеству дистрибутивов, каждый из которых затем разделяется на секции. Для оптимального сжатия и производительности в каждом дистрибутиве и секции должно содержаться не менее 100 000 строк. Вернемся к приведенному выше примеру. Если таблица фактов по продажам содержит 36 секций по месяцам, а хранилище данных SQL содержит 60 дистрибутивов, то за каждый месяц в таблице должно содержаться 6 миллионов строк, то есть 216 миллионов строк при заполнении данных за все месяцы. Если количество строк в таблице гораздо ниже рекомендуемого минимума, администратору базы данных следует рассмотреть вариант создания таблицы с меньшим числом секций, чтобы увеличить количество строк в каждом дистрибутиве.


Чтобы изменить размер текущей базы данных на уровне секций, используйте запрос, подобный показанному ниже:

```
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
JOIN        sys.allocation_units a          ON      a.[container_id]        = p.[partition_id]
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

Общее количество распределений равно количеству расположений хранения, используемых при создании таблицы. Проще говоря, что каждая таблица создается один раз для распределения.

Также можно предвидеть примерное число строк и, следовательно, будущий размер каждой секции. Секция в хранилище исходных данных будет разделена на распределения.

Используйте следующие вычисления, чтобы определить размер секции:

Размер секции MPP = размер секции SMP / число распределений

Можно узнать, сколько распределений в базе данных хранилища данных SQL, воспользовавшись следующим запросом:

```
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

Теперь вы знаете размер каждой секции в исходной системе, а ожидаемый размер для хранилища данных SQL вы можете определить по границе секции.

### Управление рабочей нагрузкой
Последнее, что необходимо учитывать при принятии решения о секции таблицы, это управление рабочими нагрузками. В хранилище данных SQL максимальный объем памяти, выделяемой для каждого распределения во время выполнения запроса, регулируется этой функцией. Дополнительную информацию об [управлении рабочими нагрузками] см. в следующей статье. В идеале размер секции будет изменяться операциями в памяти, например, внутренними перестроениями индекса columnstore. Перестроение индекса — это операция, требующая большого объема памяти. Поэтому необходимо убедиться, что при перестроении индекса секции отсутствует нехватка памяти. Увеличить объем памяти для запроса можно, переключившись с роли по умолчанию на другую доступную роль.

Информацию о выделении памяти для каждого распределения можно получить с помощью запроса к динамическим административным представлениям регулятора ресурсов. На самом деле выделение памяти будет меньше, чем цифры ниже. Информация приведена на уровне рекомендации, ее можно использовать при планировании размера секций для операций управления данными.

```
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

> [AZURE.NOTE]Старайтесь избегать изменения размеров секций за пределы выделенной памяти, предоставляемой классом очень больших ресурсов. Если размер секций превышает эту цифру, возникает риск нехватки памяти, что, в свою очередь, приводит к менее оптимальному сжатию.

## Переключение секций
Для переключения секций между двумя таблицами необходимо убедиться, что секции выровнены по соответствующим границам и их определения таблиц совпадают. Не допускаются ограничения CHECK для задания диапазона значений в таблице, исходная таблица должна содержать те же границы секций, что и целевая таблица. Если это не так, переключение секций завершится ошибкой, так как метаданные не будут синхронизированы.

### Как разделить секцию, которая содержит данные
Наиболее эффективный способ разделения секции, которая уже содержит данные, — использовать инструкцию `CTAS`. Если секционированная таблица является кластеризованным индексом columnstore, то секция таблицы должна быть пустой, прежде чем она может быть разделена.

Ниже приведен пример секционированной таблицы columnstore, содержащей по одной строке в каждой секции:

```
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

> [AZURE.NOTE]Создавая объект статистики, мы обеспечиваем более точные метаданные таблицы. Если пропустить создание объекта статистики, хранилище данных SQL будет использовать значения по умолчанию. Чтобы ознакомиться с информацией о статистике, см. раздел [Статистика][].

Далее мы можем выполнить запрос числа строк, воспользовавшись представлением каталога `sys.partitions`:

```
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

```
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Сообщение 35346, уровень 15, состояние 1, строка 44. Использование предложения SPLIT в инструкции ALTER PARTITION привело к ошибке, поскольку секция непустая. Если для таблицы существует индекс columnstore, разделить можно только пустые секции. Рекомендуется отключить индекс columnstore перед выполнением инструкции ALTER PARTITION, а после завершения ALTER PARTITION перестроить индекс columnstore.

Однако можно использовать `CTAS`, чтобы создать новую таблицу для хранения наших данных.

```
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

```
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Все, что осталось сделать, — выровнять наши данные по новым границам секции границы с помощью `CTAS` и переключить данные обратно в основную таблицу.

```
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

```
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### Система управления версиями секционирования таблиц
Чтобы избежать **порчи** определения таблицы в системе управления версиями, можно рассмотреть следующий метод:

1. Создайте таблицу как секционированную, но без значений секций.

```
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

2. Выполните `SPLIT` таблицы в процессе развертывания:

```
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

>[AZURE.NOTE]Переключение секций имеет ряд отличий по сравнению с SQL Server. Не забудьте прочитать статью [Перенос кода][], чтобы получить дополнительную информацию по этой теме.


## Дальнейшие действия
После успешного переноса схемы базы данных в хранилище данных SQL вы можете переходить к одной из приведенных ниже статей:

- [Перенос данных][]
- [Перенос кода][]

<!--Image references-->

<!-- Article references -->
[Перенос кода]: sql-data-warehouse-migrate-code.md
[Перенос данных]: sql-data-warehouse-migrate-data.md
[Статистика]: sql-data-warehouse-develop-statistics.md
[управлении рабочими нагрузками]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=AcomDC_0114_2016-->