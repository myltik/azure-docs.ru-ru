---
title: "Оптимизация транзакций для хранилища данных SQL | Документация Майкрософт"
description: "В этой статье вы найдете рекомендации по написанию эффективного кода для обновлений транзакций в хранилище данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 6f326f26-8a54-49df-a482-9c96a58db371
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ed017b542de11a5e8abe46e1651b04cb61c77265


---
# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Оптимизация транзакций для хранилища данных SQL
В этой статье объясняется, как оптимизировать производительность кода обработки транзакций, чтобы свести к минимуму риск длительных откатов.

## <a name="transactions-and-logging"></a>Транзакции и ведение журнала
Транзакции представляют собой важную часть ядра реляционной СУБД. Хранилище данных SQL использует транзакции во время изменения данных. Эти транзакции могут быть явными или неявными. Одиночные инструкции `INSERT`, `UPDATE` и `DELETE` являются примерами неявных транзакций. Явные транзакции открыто создает разработчик с помощью инструкций `BEGIN TRAN`, `COMMIT TRAN` или `ROLLBACK TRAN`. Эти транзакции обычно используются, когда несколько инструкций изменения нужно связать в одну неделимую единицу. 

Хранилище данных SQL Azure вносит изменения в базу данных с помощью журналов транзакций. У каждого распределения есть свой журнал транзакций. Запись в журналы транзакций выполняется автоматически. Настраивать что-либо не требуется. Этот процесс гарантирует запись, но в то же время увеличивает нагрузку на систему. Чтобы минимизировать этот эффект, нужно написать эффективный код транзакции. Такой код можно условно разделить на две категории.

* Используйте конструкции минимального ведения журналов, когда это возможно.
* Обрабатывайте данные в пакетах, чтобы избежать одиночных длительных транзакций.
* Пользуйтесь шаблоном переключения разделов при внесении значительных изменений в тот или иной раздел.

## <a name="minimal-vs-full-logging"></a>Минимальное ведение журнала и полное ведение журнала
В отличие от полностью регистрируемых операций, которые с помощью журнала транзакций отслеживают каждое изменение строки, минимально регистрируемые операции отслеживают только выделения экстента и изменения метаданных. Иными словами, в рамках минимального ведения журнала протоколируется только та информация, которая нужна, чтобы откатить транзакцию в случае сбоя или явного запроса (`ROLLBACK TRAN`). Так как в журнал транзакций вносится гораздо меньше информации, минимально регистрируемая операция отличается от полностью регистрируемой операции аналогичного размера более высокой производительностью. Кроме того, так как в журнал транзакций вносится меньше записей, создается меньше данных журнала и процесс ввода-вывода становится эффективнее.

Ограничения безопасности транзакций применяются только к полностью регистрируемым операциям.

> [!NOTE]
> Минимально регистрируемые операции могут быть частью явных транзакций. Так как все изменения в структурах выделения отслеживаются, такие операции можно откатывать. Важно понимать, что минимальная регистрация изменений — это не отсутствие регистрации.
> 
> 

## <a name="minimally-logged-operations"></a>Минимально регистрируемые операции
Минимально регистрируемыми могут быть такие операции:

* CREATE TABLE AS SELECT ([CTAS][CTAS]);
* INSERT..SELECT
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Ограничение безопасности транзакций не влияет на внутренние операции перемещения данных (например, `BROADCAST` и `SHUFFLE`).
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Минимальное ведение журнала и массовая загрузка
`CTAS` и `INSERT...SELECT` — это операции массовой загрузки. Тем не менее обе они зависят от определения целевой таблицы и сценария загрузки. В таблице, приведенной ниже, перечислены условия, в зависимости от которых ваша массовая операция будет регистрироваться полностью или минимально.  

| Первичный индекс | Сценарий загрузки | Режим ведения журнала |
| --- | --- | --- |
| Куча |Любой |**Минимальный** |
| Кластеризованный индекс |Пустая целевая таблица |**Минимальный** |
| Кластеризованный индекс |Загруженные строки не перекрывают существующие страницы в целевом объекте |**Минимальный** |
| Кластеризованный индекс |Загруженные строки перекрывают существующие страницы в целевом объекте |Полное |
| Кластеризованный индекс columnstore |Размер пакета >= 102 400 для распределения с выравниванием по разделам |**Минимальный** |
| Кластеризованный индекс columnstore |Размер пакета < 102 400 для распределения с выравниванием по разделам |Полное |

Следует отметить, что любые записи, выполняемые для обновления вторичных или некластеризованных индексов, считаются полностью регистрируемыми операциями.

> [!IMPORTANT]
> В хранилище данных SQL есть 60 распределений. Поэтому, если исходить из того, что все строки распределены равномерно и их целью является один раздел, пакет должен содержать 6 144 000 строк (или больше), чтобы быть минимально регистрируемым, когда запись выполняется в кластеризованный индекс columnstore. Если таблица секционирована и вставляемые строки охватывают границы секции, потребуется 6 144 000 строк на границу секции (при условии, что данные распределяются равномерно). Чтобы вставка данных в распределении была минимально регистрируемой, размер каждого раздела в каждом распределении должен превышать пороговое значение (102 400 строк).
> 
> 

При загрузке данных в непустую таблицу с кластеризованным индексом некоторые строки могут быть полностью регистрируемыми, а некоторые — минимально. Кластеризованный индекс — это сбалансированное дерево страниц. Если страница, на которой выполняется запись, уже содержит строки другой транзакции, эти операции записи будут полностью регистрируемыми в журнале. Однако если страница пустая, то операция записи на ней будет минимально регистрируемой.

## <a name="optimizing-deletes"></a>Оптимизация операций удаления
`DELETE` является полностью регистрируемой.  Если необходимо удалить большой объем данных в таблице или секции, часто разумнее применить к данным, которые вы хотите оставить, минимально регистрируемую операцию `SELECT` .  Для этого необходимо создать новую таблицу с помощью [CTAS][CTAS].  Затем с помощью [RENAME][RENAME] замените старую таблицу только что созданной.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Оптимизация операций обновления
`UPDATE` является полностью регистрируемой.  Если нужно обновить много строк в таблице или секции, для этого, как правило, намного эффективнее использовать минимально регистрируемую операцию, например [CTAS][CTAS].

В примере ниже полное обновление таблицы преобразовано в операцию `CTAS` , что делает возможным минимальное ведение журнала.

В этом случае мы задним числом добавляем сумму скидки к сумме продаж в таблице.

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Повторное создание больших таблиц может быть удобнее, если использовать функции управления рабочими нагрузками хранилища данных SQL. Для получения дополнительных сведений см. раздел, посвященный управлению рабочими нагрузками, в статье о [параллелизме][параллелизм].
> 
> 

## <a name="optimizing-with-partition-switching"></a>Оптимизация с помощью переключения секций
Если вы столкнулись с крупномасштабными изменениями в [секции таблицы][секции таблицы], то имеет смысл воспользоваться схемой переключения секций. Если изменения данных существенные и охватывают несколько секций, то простая итерация по секциям приводит к тому же результату.

Чтобы переключить разделы, выполните следующие действия.

1. Создайте пустой выходной раздел.
2. Выполните обновление в виде операции CTAS.
3. Переключите существующие данные на выходную таблицу.
4. Подключите новые данные.
5. Очистите данные.

Тем не менее нужно уметь определять разделы, которые следует переключить. Для этого нам понадобится вспомогательная процедура наподобие приведенной ниже. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Эта процедура позволяет максимально повторно использовать код и делает пример переключения секций более компактным.

Приведенный ниже код содержит все пять действий, упомянутых выше. В результате получилась полноценная процедура переключения разделов.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Сведение к минимуму ведения журнала с помощью небольших пакетов
Операции по изменению больших объемов данных бывает полезно разбить на блоки или пакеты, чтобы охватить единицу работы.

Ниже приведен рабочий пример. В целях демонстрации указан небольшой размер пакета. В реальной среде он был бы гораздо больше. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Рекомендации по приостановке и масштабированию
Работу хранилища данных SQL Azure можно по запросу приостановить или возобновить. Также его можно масштабировать. Когда вы приостанавливаете работу хранилища данных SQL или масштабируете его, важно понимать, что запущенные транзакции немедленно прекращаются, что, в свою очередь, откатывает открытые транзакции. Если в рамках рабочей нагрузки выполнено длительное и незавершенное изменение данных перед операцией приостановки или масштабирования, это изменение нужно отменить. Это может повлиять на время, требующееся для приостановки или масштабирования базы данных хранилища данных SQL Azure. 

> [!IMPORTANT]
> `UPDATE` и `DELETE` — это полностью регистрируемые операции, поэтому упомянутые операции отмены и повтора могут выполняться значительно дольше, чем такие же минимально регистрируемые операции. 
> 
> 

Лучше всего перед приостановкой работы или масштабированием хранилища данных SQL подождать, пока не завершатся текущие транзакции по изменению данных. Однако это не всегда удобно. Чтобы снизить риск длительного отката, выполните одно из следующих действий.

* Измените долго выполняющиеся операции, используя [CTAS][CTAS].
* Разбейте операцию на блоки для работы с подмножествами строк.

## <a name="next-steps"></a>Дальнейшие действия
Сведения об уровнях изоляции и ограничениях транзакций см. в статье [Транзакции в хранилище данных SQL][Транзакции в хранилище данных SQL].  Рекомендации по использованию хранилища данных SQL Azure см. в статье [Рекомендации по использованию хранилища данных SQL Azure][Рекомендации по использованию хранилища данных SQL Azure].

<!--Image references-->

<!--Article references-->
[Транзакции в хранилище данных SQL]: ./sql-data-warehouse-develop-transactions.md
[секции таблицы]: ./sql-data-warehouse-tables-partition.md
[Параллелизм]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Рекомендации по использованию хранилища данных SQL Azure]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]:https://msdn.microsoft.com/library/ms188388.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->




<!--HONumber=Nov16_HO3-->


