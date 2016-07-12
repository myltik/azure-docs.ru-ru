<properties
   pageTitle="Параметры предложения Group By в хранилище данных SQL | Microsoft Azure"
   description="Советы по реализации параметров предложения Group By в хранилище данных SQL Azure для разработки решений."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Группировка по параметрам в хранилище данных SQL

Предложение [GROUP BY][] используется для объединения данных в сводную выборку строк. Оно имеет также несколько дополнительных параметров, позволяющих выполнять функции, которые не поддерживаются хранилищем данных SQL Azure напрямую.

Доступны следующие параметры:
- GROUP BY с ROLLUP;
- GROUPING SETS;
- GROUP BY с CUBE.

## Параметры Rollup и Grouping Sets
Самый простой вариант — это выполнить свертку с помощью оператора `UNION ALL`, не используя отдельный синтаксис. Результат будет точно таким же.

Ниже приведен пример применения оператора Group By с параметром `ROLLUP`:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

С помощью параметра ROLLUP мы запросили следующие агрегаты.
- Страна и регион
- Страна
- Общий итог

Вместо этого можно использовать оператор `UNION ALL`, указав, какие группы данных необходимо получить. Результат будет точно такой же.

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Для применения параметра GROUPING SETS используется тот же код, но разделы с оператором UNION ALL создаются только для нужных уровней группирования.

## Параметры Cube
Предложение GROUP BY с параметром CUBE можно составить, используя оператор UNION ALL. Проблема в том, что в этом случае код может быстро разрастись и стать слишком громоздким. Чтобы этого не случилось, можно прибегнуть к более современному методу.

Возьмем приведенный выше пример.

Для начала нужно задать «куб», определяющий все уровни группирования данных, которые нам нужно создать. При этом следует отметить применение оператора CROSS JOIN для двух производных таблиц. Он формирует все необходимые уровни. Остальная часть кода нужна только для форматирования.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Результат применения CTAS см. ниже:

![][1]

Второй шаг — указать целевую таблицу для сохранения промежуточных результатов:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Третий шаг — запустить цикл для куба столбцов, участвующих в группировании данных. Запрос будет выполнен по одному разу для каждой строки временной таблицы #Cube, а результаты сохранятся во временной таблице #Results.

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

В конечном итоге результаты можно извлечь, прочитав данные из временной таблицы #Results.

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Если разбить этот код на разделы и создать циклическую конструкцию, он станет более управляем и удобен в обслуживании.


## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0629_2016-->