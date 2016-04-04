<properties
   pageTitle="Управление индексами | Microsoft Azure"
   description="Рекомендации, помогающие пользователям управлять индексами."
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
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Управление индексами
В этой статье демонстрируются некоторые основные методики управления индексами.

## Оптимизация перестроений индекса
Можно оптимизировать перестроение индекса одном из двух способов:

1. Секционировать таблицу и выполнять перестроения индекса на уровне секций.
2. Использовать [CTAS][], чтобы повторно создать секцию данных в новой таблице, и переключить секции в новой таблице.

## Перестроения секционированного индекса

Ниже приведен пример того, как можно перестроить одну секцию.

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

ALTER INDEX..REBUILD лучше всего подходит для небольших объемов данных, особенно для индексов columnstore. Открытые, закрытые и сжатые rowgroup включаются в перестроение. Тем не менее, если секция довольно велика, вы обнаружите, что более эффективна операция `CTAS`. Ниже приведен пример полного перестроения индекса.

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

Ознакомьтесь со статьей [ALTER INDEX][], чтоб больше узнать об этом синтаксисе.

## Использование CTAS для перестроения секции

Ниже приведен пример того, как можно перестроить секцию с помощью CTAS.

```
-- Step 01. Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;

```

## Дальнейшие действия
Дополнительные рекомендации о том, как создавать секции и задавать их размер см. в статье о [секционировании таблиц][]. Также статья содержит пример, который поможет определить границы секций.

Дополнительные советы по управлению см. в обзоре [управления][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[секционировании таблиц]: sql-data-warehouse-develop-table-partitions.md
[управления]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/ru-RU/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->