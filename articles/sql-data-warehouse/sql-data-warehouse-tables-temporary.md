---
title: "Временные таблицы в хранилище данных SQL | Документация Майкрософт"
description: "Начало работы с временными таблицами в хранилище данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 9b1119eb-7f54-46d0-ad74-19c85a2a555a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 83b12c6daf5422039f3dd95eb9b177b972fad840


---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Временные таблицы в хранилище данных SQL
> [!div class="op_single_selector"]
> * [Обзор][Обзор]
> * [Типы данных][Типы данных]
> * [Распределение][Распределение]
> * [Индекс][Индекс]
> * [Секция][Секция]
> * [Статистика][Статистика]
> * [Временные][Временные]
> 
> 

Временные таблицы очень удобны при обработке данных — особенно во время преобразования, где промежуточные результаты являются временными. В хранилище данных SQL временные таблицы существуют на уровне сеанса.  Их можно просмотреть только в сеансе, в котором они были созданы. После выхода из сеанса они автоматически удаляются.  Временные таблицы позволяют оптимизировать производительность, так как их результаты записываются в локальное, а не удаленное хранилище.  Временные таблицы в хранилище данных SQL Azure немного отличаются от таких таблиц в Базе данных SQL Azure, потому что доступ к ним можно осуществлять из любого места в сеансе: как изнутри, так и извне хранимой процедуры.

В этой статье содержатся важные рекомендации по использованию временных таблиц и приводятся основные концепции временных таблиц уровня сеанса. На основе сведений, содержащихся в этой статье, вы сможете разбить код на модули, чтобы улучшить его повторное использование и повысить удобство управления.

## <a name="create-a-temporary-table"></a>Создание временной таблицы
Временные таблицы создаются простым добавлением к имени таблицы префикса `#`.  Например:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Временные таблицы можно также создать с помощью `CTAS` точно таким же образом:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

> [!NOTE]
> `CTAS` — очень мощная команда, которая также очень эффективна при использовании с журналом транзакций. 
> 
> 

## <a name="dropping-temporary-tables"></a>Удаление временных таблиц
При создании сеанса не должно быть ни одной временной таблицы.  Впрочем, если вы вызываете одну и ту же хранимую процедуру, которая создает временную таблицу с одним именем, чтобы обеспечить успешное выполнение инструкции `CREATE TABLE`, можно использовать простую проверку на наличие с помощью `DROP`, как показано в приведенном ниже примере.

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Для согласованности кода целесообразно использовать этот шаблон как для обычных, так и для временных таблиц.  Рекомендуется также удалить временные таблицы с помощью `DROP TABLE` , когда вы закончите работу с ними в коде.  При разработке хранимой процедуры команды удаления обычно помещаются вместе в конце процедуры, чтобы гарантировать, что объекты удалены.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Разделение кода на модули
Так как временные таблицы можно просмотреть где угодно в сеансе пользователя, эту возможность можно использовать для модульной организации кода приложения.  Например, представленная ниже хранимая процедура объединяет в себе приведенные выше рекомендуемые методы, чтобы создать инструкцию DDL, которая будет обновлять всю статистику в базе данных в соответствии с именем статистики.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

На этом этапе единственное совершенное действие заключается в создании хранимой процедуры, которая просто создаст временную таблицу #stats_ddl с использованием инструкций DDL.  Эта хранимая процедура удалит таблицу #stats_ddl, если она уже существует, что обеспечит работу таблицы без сбоев в случае ее повторного запуска на протяжении сеанса.  Но так как в конце хранимой процедуры нет команды `DROP TABLE` , после выполнения этой процедуры созданная таблица сохранится и ее можно будет читать за пределами хранимой процедуры.  В отличие от других баз данных SQL Server, в хранилище данных SQL временную таблицу можно использовать вне процедуры, в которой она была создана.  Временные таблицы хранилища данных SQL можно использовать **где угодно** внутри сеанса. Это может привести к большей модульности и управляемости кода, как показано в следующем примере.

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Ограничения временной таблицы
В хранилище данных SQL есть несколько ограничений, касающихся реализации временных таблиц.  В настоящее время поддерживаются временные таблицы, которые можно просмотреть только в сеансе.  Глобальные временные таблицы не поддерживаются.  Кроме того, для временных таблиц нельзя создавать представления.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Обзор], [типам данных таблиц][Типы данных], [распределению][Распределение], [индексированию][Индекс], [секционированию][Секция] и [управлению статистикой таблиц][Статистика].  Рекомендации по использованию хранилища данных SQL Azure см. в [этой статье][Рекомендации по использованию хранилища данных SQL Azure].

<!--Image references-->

<!--Article references-->
[Обзор]: ./sql-data-warehouse-tables-overview.md
[Типы данных]: ./sql-data-warehouse-tables-data-types.md
[Распределение]: ./sql-data-warehouse-tables-distribute.md
[Индекс]: ./sql-data-warehouse-tables-index.md
[Секция]: ./sql-data-warehouse-tables-partition.md
[Статистика]: ./sql-data-warehouse-tables-statistics.md
[Временные]: ./sql-data-warehouse-tables-temporary.md
[Рекомендации по использованию хранилища данных SQL Azure]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


