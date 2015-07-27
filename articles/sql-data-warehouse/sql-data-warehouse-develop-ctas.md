<properties
   pageTitle="Функция Create Table As Select (CTAS) в хранилище данных SQL | Microsoft Azure"
   description="Рекомендации по составлению кода с использованием оператора Create Table as Select (CTAS) в хранилище данных SQL Azure для разработки решений."
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Функция Create Table As Select (CTAS) в хранилище данных SQL
Create Table As Select (CTAS) — одна из самых важных функций T-SQL. Это полностью распараллеленная операция, которая создает новую таблицу на основе выходных данных оператора Select. Если хотите, считайте ее расширенной версией оператора SELECT..INTO.

CTAS можно также использовать для замены некоторых неподдерживаемых функций, указанных ниже. Зачастую такой вариант оказывается даже более удачным, так как позволяет не только соблюсти требования к коду, но и ускорить его выполнение в хранилище данных SQL. Это связано с распараллеленной структурой системы.

> [AZURE.NOTE]Старайтесь всегда начинать с функции CTAS. Если вы считаете, что решить проблему с ее помощью можно, то, скорее всего, этот вариант окажется лучшим, даже если в результате вам придется записывать больше данных.

Сценарии, которые можно обойти с помощью CTAS:

- SELECT..INTO
- Синтаксис соединения ANSI с операторами обновления 
- Синтаксис соединения ANSI с операторами удаления
- Оператор MERGE

Данный документ включает также рекомендации по составлению кода с использованием функции CTAS.

## SELECT..INTO
Оператор SELECT... INTO может встречаться в разных местах вашего решения.

Пример оператора SELECT..INTO:

```
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Преобразовать его в CTAS очень просто:

```
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

Прибегнув к CTAS, вы сможете также указать параметры распределения данных и при желании проиндексировать таблицу.

## Замена синтаксиса соединения ANSI для операторов обновления

Вам может встретится сложное обновление с объединением более двух таблиц, в котором для операций обновления или удаления используется синтаксис соединения ANSI.

Допустим, вам нужно обновить следующую таблицу:

```
CREATE TABLE [dbo].[AnnualCategorySales]
(	[EnglishProductCategoryName]	NVARCHAR(50)	NOT NULL
,	[CalendarYear]					SMALLINT		NOT NULL
,	[TotalSalesAmount]				MONEY			NOT NULL
)
WITH
(
	DISTRIBUTION = ROUND_ROBIN
)
;
```

Исходный запрос мог выглядеть примерно так:

```
UPDATE	acs
SET		[TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM	[dbo].[AnnualCategorySales] 	AS acs
JOIN	(
		SELECT	[EnglishProductCategoryName]
		,		[CalendarYear]
		,		SUM([SalesAmount])				AS [TotalSalesAmount]
		FROM	[dbo].[FactInternetSales]		AS s
		JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
		JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
		JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
		JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
		WHERE 	[CalendarYear] = 2004
		GROUP BY
				[EnglishProductCategoryName]
		,		[CalendarYear]
		) AS fis
ON	[acs].[EnglishProductCategoryName]	= [fis].[EnglishProductCategoryName]
AND	[acs].[CalendarYear]				= [fis].[CalendarYear]
;
```

Поскольку хранилище данных SQL не поддерживает синтаксис соединения ANSI, вы не сможете скопировать этот код, не подвергнув его небольшой корректировке.

Для замены этого кода можно использовать комбинацию CTAS и явного соединения:

```
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT	ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)	AS [EnglishProductCategoryName]
,		ISNULL(CAST([CalendarYear] AS SMALLINT),0) 						AS [CalendarYear]
,		ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)						AS [TotalSalesAmount]
FROM	[dbo].[FactInternetSales]		AS s
JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
WHERE 	[CalendarYear] = 2004
GROUP BY
		[EnglishProductCategoryName]
,		[CalendarYear]
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

## Замена синтаксиса соединения ANSI для операторов удаления
В некоторых случаях для удаления данных лучше использовать CTAS. Вместо удаления данных просто выделите данные, которые нужно сохранить. Это относится, в частности, к операторам DELETE с использованием синтаксиса объединения ANSI, поскольку хранилище данных SQL его не поддерживает.

Пример преобразованного оператора DELETE:

```
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

## Замена операторов объединения
Функция CTAS может (по крайней мере частично) заменить операторы объединения. Операторы `INSERT` и `UPDATE` можно консолидировать. При этом второй оператор должен ликвидировать все удаленные записи.

Пример оператора `UPSERT`:

```
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

## Рекомендация по использованию функции CTAS: прямо указывайте тип данных и допустимость нулевого результата

При переносе кода вы можете встретить следующую схему кодовой комбинации:

```
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

```
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

```
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Значение, сохраненное в качестве результата, будет иным. Поскольку значение, сохраненное в столбце результата, используется в других выражениях, ошибка становится еще более значимой.

![][1]

Это особенно важно при переносе данных. Второй запрос, безусловно, более точен, однако и здесь существует проблема. Полученные данные будут разными в разных системах, что ставит под сомнение достоверность перенесенных данных. Это один из тех редких случаев, когда "неправильный" ответ на деле оказывается верным!

Причина расхождения этих двух результатов связана с явным преобразованием типов данных. В первом примере таблица дает определение столбца. При вставке строки происходит явное преобразование типа данных. Во втором примере явного преобразования типов данных нет, поскольку тип данных в столбце определяется выражением. Обратите внимание, что во втором примере столбец определен как допускающий нулевое значение, а в первом нет. При создании таблицы в первом примере допустимость нулевых значений в столбце была обозначена прямо. Во втором примере указано только выражение, а значит, определение по умолчанию будет нулевым.

Для устранения этих проблем необходимо явно задать преобразование типа данных и допустимость нулевых значений в части SELECT оператора CTAS. Эти свойства нельзя задать в части создания таблицы.

В приведенном ниже примере показано, как исправить код:

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Обратите внимание на следующее: операторы CAST или CONVERT использовать можно; для определения допустимости нулевых значений используется оператор ISNULL, а не COALESCE; ISNULL является внешней функцией; вторая часть оператора ISNULL является константой, т. е. равна 0.

> [AZURE.NOTE]Для правильного задания допустимости нулевых значений необходимо использовать оператор ISNULL, а не COALESCE. COALESCE не является детерминированной функцией, поэтому результат выражения при использовании этого оператора всегда будет допускать нулевые значения. ISNULL действует иначе. Он детерминирован, поэтому, если вторая часть функции ISNULL является константой или литералом, итоговым значением будет NOT NULL.

Эта рекомендация не только позволяет добиться точности вычислений, но и играет важную роль в переключении между разделами таблицы. Предположим, эта таблица определена как факт:

```
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

```
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

```
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

Дополнительные сведения об использовании [CTAS][] см. в MSDN. Это один из самых важных операторов в хранилище данных SQL Azure. Научитесь его применять.

## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CTAS]: https://msdnstage.redmond.corp.microsoft.com/ru-ru/library/mt204041.aspx

<!--Other Web references-->

<!---HONumber=July15_HO3-->