---
title: Варианты использования предложения GROUP BY в хранилище данных SQL Azure | Документация Майкрософт
description: Советы по реализации параметров предложения Group By в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0548983df23b158385783ac777b23268b5ac7d01
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526052"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Группировка по параметрам в хранилище данных SQL
Советы по реализации параметров предложения Group By в хранилище данных SQL Azure для разработки решений.

## <a name="what-does-group-by-do"></a>Предназначение предложения GROUP BY

Предложение T-SQL [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) используется для объединения данных в сводный набор строк. С GROUP BY можно использовать некоторые параметры, которые не поддерживаются хранилищем данных SQL. Этим параметрам можно найти альтернативу.

Доступны следующие параметры:

* GROUP BY с ROLLUP;
* GROUPING SETS;
* GROUP BY с CUBE.

## <a name="rollup-and-grouping-sets-options"></a>Параметры Rollup и Grouping Sets
Самый простой вариант — это выполнить свертку с помощью оператора UNION ALL, не используя отдельный синтаксис. Результат будет точно таким же.

Пример использования инструкции GROUP BY с оператором ROLLUP.
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

Используя ROLLUP, приведенный выше пример запрашивает следующие объединения.

* Страна и регион
* Страна
* Общий итог

Чтобы получить те же результаты без ROLLUP, можно использовать оператор UNION ALL и явным образом указать требуемые объединения.

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

Для замены GROUPING SETS применяется тот же принцип. Достаточно создать разделы с UNION ALL для требуемых уровней объединения.

## <a name="cube-options"></a>Параметры Cube
Предложение GROUP BY с параметром CUBE можно составить, используя оператор UNION ALL. Проблема в том, что в этом случае код может быстро разрастись и стать слишком громоздким. В этом случае можно прибегнуть к более современному методу.

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

Ниже приведены результаты применения CTAS.

![GROUP BY с CUBE](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

И наконец, результаты можно извлечь, прочитав данные из временной таблицы #Results.

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Если разбить этот код на разделы и создать циклическую конструкцию, он станет более управляем и удобен в обслуживании.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).

