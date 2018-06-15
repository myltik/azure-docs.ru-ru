---
title: Инструкция CREATE TABLE AS SELECT (CTAS) в хранилище данных SQL Azure | Документация Майкрософт
description: Рекомендации по составлению кода с использованием инструкции CREATE TABLE AS SELECT (CTAS) в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 9bff6b1216ae826203b24a2cdf8a3d7fd0fd586f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599090"
---
# <a name="using-create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Использование инструкции CREATE TABLE AS SELECT (CTAS) в хранилище данных SQL Azure
Рекомендации по составлению кода с использованием инструкции T-SQL CREATE TABLE AS SELECT (CTAS) в хранилище данных SQL Azure для разработки решений.

## <a name="what-is-create-table-as-select-ctas"></a>Что такое инструкция CREATE TABLE AS SELECT (CTAS)?

Инструкция [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) или CTAS — одна из самых важных функций T-SQL. Это распараллеленная операция, которая создает новую таблицу на основе выходных данных инструкции SELECT. CTASD — это самый простой и быстрый способ создания копии таблицы. 

## <a name="selectinto-vs-ctas"></a>Сравнение SELECT..INTO и CTAS;
CTASD можно рассматривать в качестве усовершенствованной версии инструкции [SELECT...INTO](/sql/t-sql/queries/select-into-clause-transact-sql).

Ниже показан пример простой инструкции SELECT...INTO.

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

В приведенном выше примере `[dbo].[FactInternetSales_new]` создается как таблица с распределением ROUND_ROBIN и кластеризованным индексом columnstore, так как эти значения используются по умолчанию для таблицы в хранилище данных SQL Azure.

Однако SELECT...INTO не позволяет изменить метод распределения или тип индекса в ходе операции. Вот где может пригодиться CTAS.

Изменить предыдущий пример для использования CTAS довольно просто.

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

С помощью CTAS можно изменить распределение данных в таблице, а также ее тип. 

> [!NOTE]
> Если вы просто пытаетесь изменить индекс в операции `CTAS` и исходная таблица использует хэш-распределение, то для выполнения операции `CTAS` будет лучше, если вы оставите тот же столбец распределения и тип данных. Это позволит избежать перемещения данных для перекрестного распределения во время операции, что повысит ее эффективность.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Копирование таблицы с помощью CTAS
Возможно, одним из самых распространенных способов использования `CTAS` является создание копии таблицы, позволяющее изменить DDL. Например, если изначально вы создали таблицу как `ROUND_ROBIN` и теперь хотите изменить ее на таблицу, распределенную по столбцу, именно `CTAS` поможет вам изменить столбец распределения. `CTAS` также можно использовать для изменения секционирования, индексирования или типов столбцов.

Предположим, что вы создали эту таблицу с использованием типа распределения по умолчанию `ROUND_ROBIN`, так как в операторе `CREATE TABLE` не был указан столбец распространения.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Теперь вам нужно создать новую копию этой таблицы с кластеризованным индексом Columnstore, чтобы воспользоваться преимуществами производительности кластеризованных таблиц Columnstore. Также требуется распространить эту таблицу по ProductKey, поскольку ожидаются соединения по этому столбцу и требуется избежать перемещения данных во время операций соединения по ProductKey. Кроме того, необходимо добавить секционирование по OrderDateKey, чтобы можно было быстро удалять старые данные посредством удаления старых секций. Здесь приведен оператор CTAS, копирующий старую таблицу в новую таблицу.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Наконец, можно переименовать таблицу, чтобы переключиться на новую таблицу, а затем удалить старую таблицу.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [!NOTE]
> Хранилище данных SQL Azure пока не поддерживает автоматическое создание или автоматическое обновление статистики.  Чтобы добиться максимально высокой производительности запросов, крайне важно сформировать статистические данные для всех столбцов всех таблиц после первой загрузки или после любых значительных изменений в данных.  Дополнительные сведения о работе со статистикой см. в статье о [статистике][Statistics] из цикла статей о разработке.
> 
> 

## <a name="using-ctas-to-work-around-unsupported-features"></a>Обход неподдерживаемых функций с помощью CTAS
CTAS можно также использовать для замены некоторых неподдерживаемых функций, указанных ниже. Зачастую такой вариант оказывается даже более удачным, так как позволяет не только соблюсти требования к коду, но и ускорить его выполнение в хранилище данных SQL. Это связано с распараллеленной структурой системы. Сценарии, которые можно обойти с помощью CTAS:

* Синтаксис соединения ANSI с операторами обновления
* Синтаксис соединения ANSI с операторами удаления
* Оператор MERGE

> [!NOTE]
> Старайтесь всегда начинать с функции CTAS. Если вы считаете, что решить проблему с помощью `CTAS` можно, то, скорее всего, этот вариант окажется лучшим, даже если в результате вам придется записывать больше данных.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>Замена синтаксиса соединения ANSI для операторов обновления
Вам может встретится сложное обновление с объединением более двух таблиц, в котором для операций обновления или удаления используется синтаксис соединения ANSI.

Допустим, вам нужно обновить следующую таблицу:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Исходный запрос мог выглядеть примерно так:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Так как хранилище данных SQL не поддерживает соединения ANSI в предложении `FROM` оператора `UPDATE`, вы не сможете скопировать этот код, не подвергнув его небольшой корректировке.

Для замены этого кода можно использовать комбинацию `CTAS` и явного соединения:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Замена синтаксиса соединения ANSI для операторов удаления
В некоторых случаях для удаления данных лучше использовать `CTAS`. Вместо удаления данных просто выделите данные, которые нужно сохранить. В частности, это относится к операторам `DELETE`, использующим синтаксис соединения ANSI, так как хранилище данных SQL не поддерживает соединения ANSI в предложении `FROM` оператора `DELETE`.

Пример преобразованного оператора DELETE:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Замена операторов объединения
CTAS может (по крайней мере частично) заменить инструкции объединения. Инструкции INSERT и UPDATE можно объединить в одну инструкцию. При этом второй оператор должен ликвидировать все удаленные записи.

Ниже приведен пример инструкции UPSERT.

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Рекомендация по использованию функции CTAS: прямо указывайте тип данных и допустимость нулевого результата
При переносе кода вы можете встретить следующую схему кодовой комбинации:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Вы можете инстинктивно решить, что этот код нужно перенести в CTAS, и будете правы. При этом нужно помнить о подводных камнях.

Представленный ниже код НЕ дает такой же результат:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Обратите внимание, что в столбец "результат" переходит тип данных и допустимость нулевых значений выражения. Если не соблюдать осторожность, это может привести к определенной вариации значений.

Попробуйте применить, например, этот код:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Значение, сохраненное в качестве результата, будет иным. Поскольку значение, сохраненное в столбце результата, используется в других выражениях, ошибка становится еще более значимой.

![Результаты CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Это особенно важно при переносе данных. Второй запрос, безусловно, более точен, однако и здесь существует проблема. Полученные данные будут разными в разных системах, что ставит под сомнение достоверность перенесенных данных. Это один из тех редких случаев, когда "неправильный" ответ на деле оказывается верным!

Причина расхождения этих двух результатов связана с явным преобразованием типов данных. В первом примере таблица дает определение столбца. При вставке строки происходит явное преобразование типа данных. Во втором примере явного преобразования типов данных нет, поскольку тип данных в столбце определяется выражением. Обратите внимание, что во втором примере столбец определен как допускающий нулевое значение, а в первом нет. При создании таблицы в первом примере допустимость нулевых значений в столбце была обозначена прямо. Во втором примере указано только выражение, а значит, определение по умолчанию будет нулевым.  

Для устранения этих проблем необходимо явно задать преобразование типа данных и допустимость нулевых значений в части SELECT инструкции CTAS. Эти свойства нельзя задать в части создания таблицы.

В приведенном ниже примере показано, как исправить код:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Обратите внимание на следующее.

* Можно было использовать CAST или CONVERT.
* Чтобы обеспечить допустимость значений NULL, используется ISNULL, а не COALESCE.
* ISNULL является внешней функцией.
* Вторая часть ISNULL является константой, т. е. 0.

> [!NOTE]
> Для правильного задания допустимости нулевых значений необходимо использовать ISNULL, а не COALESCE. COALESCE не является детерминированной функцией, поэтому выражение всегда будет допускать нулевые значения. ISNULL работает иначе. Он детерминирован, поэтому если вторая часть функции ISNULL является константой или литералом, итоговым значением будет NOT NULL.
> 
> 

Эта рекомендация не только позволяет добиться точности вычислений, но и играет важную роль в переключении между разделами таблицы. Предположим, эта таблица определена как факт:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Поле значения представляет собой расчетное выражение, но при этом не является частью исходных данных.

Чтобы создать набор данных с делением на разделы, используйте следующий код:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Такой запрос выполняется нормально. Проблема возникает при попытке выполнить переключение между разделами. Определения таблиц не совпадают. Чтобы определения таблиц совпадали, необходимо изменить CTAS.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Таким образом, можно сказать, что соблюдать единство типов данных и контролировать свойства допустимости нулевых значений в CTAS всегда полезно. Это обеспечивает правильность вычислений и возможность переключения между разделами.

Обратитесь к документации по [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse). Это один из самых важных операторов в хранилище данных SQL Azure. Научитесь его применять.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).

