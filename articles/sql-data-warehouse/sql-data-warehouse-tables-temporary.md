---
title: Временные таблицы в хранилище данных SQL | Документация Майкрософт
description: Важные рекомендации по использованию временных таблиц и основные концепции временных таблиц уровня сеанса.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a3e06a4074bc7b5cd8612162a624718107a50656
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31518405"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Временные таблицы в хранилище данных SQL
В этой статье содержатся важные рекомендации по использованию временных таблиц и приводятся основные концепции временных таблиц уровня сеанса. На основе сведений, содержащихся в этой статье, вы сможете разбить код на модули, чтобы улучшить его повторное использование и повысить удобство управления.

## <a name="what-are-temporary-tables"></a>Что собой представляют временные таблицы
Временные таблицы удобны при обработке данных — особенно во время преобразования, где промежуточные результаты являются временными. В хранилище данных SQL временные таблицы существуют на уровне сеанса.  Их можно просмотреть только в сеансе, в котором они были созданы. После выхода из сеанса они автоматически удаляются.  Временные таблицы позволяют оптимизировать производительность, так как их результаты записываются в локальное, а не удаленное хранилище.  Временные таблицы в хранилище данных SQL Azure немного отличаются от таких таблиц в Базе данных SQL Azure, потому что доступ к ним можно осуществлять из любого места в сеансе: как изнутри, так и извне хранимой процедуры.

## <a name="create-a-temporary-table"></a>Создание временной таблицы
Для создания временной таблицы к имени таблицы добавляется префикс `#`.  Например: 

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
> `CTAS` — мощная команда, которая особенно эффективна при использовании пространства журнала транзакций. 
> 
> 

## <a name="dropping-temporary-tables"></a>Удаление временных таблиц
При создании сеанса не должно быть ни одной временной таблицы.  Впрочем, если вы вызываете одну и ту же хранимую процедуру, в которой создается временная таблица с тем же именем, чтобы обеспечить успешное выполнение инструкции `CREATE TABLE`, можно использовать простую проверку на наличие с помощью `DROP`, как показано в следующем примере:

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
Так как временные таблицы можно просмотреть где угодно в сеансе пользователя, эту возможность можно использовать для модульной организации кода приложения.  Например, в представленной ниже хранимой процедуре создается DDL для обновления всей статистики в базе данных по имени статистического показателя.

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

На этом этапе единственное выполненное действие заключается в создании хранимой процедуры, в которой создается временная таблица #stats_ddl с использованием инструкций DDL.  В этой хранимой процедуре удаляется таблица #stats_ddl, если она уже существует. Это обеспечивает бесперебойную работу таблицы в случае ее повторного запуска на протяжении сеанса.  Но так как в конце хранимой процедуры нет команды `DROP TABLE`, после выполнения этой процедуры созданная таблица сохранится и ее можно будет читать вне хранимой процедуры.  В отличие от других баз данных SQL Server, в хранилище данных SQL временную таблицу можно использовать вне процедуры, в которой она была создана.  Временные таблицы хранилища данных SQL можно использовать **где угодно** внутри сеанса. Это может улучшить модульность и управляемость кода, как показано в следующем примере:

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

## <a name="next-steps"></a>Дополнительная информация
См. [общие сведения о проектировании таблиц](sql-data-warehouse-tables-overview.md).

