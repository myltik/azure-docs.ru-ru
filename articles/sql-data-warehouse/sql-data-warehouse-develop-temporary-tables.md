<properties
   pageTitle="Временные таблицы в хранилище данных SQL | Microsoft Azure"
   description="Советы по использованию временных таблиц в хранилище данных SQL Azure для разработки решений."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Временные таблицы в хранилище данных SQL
Временные таблицы существуют в хранилище данных SQL на уровне сеанса. Они определяются как локальные временные таблицы и, в отличие от таблиц SQL Server, к ним можно обращаться откуда угодно внутри сеанса.

Временные таблицы очень удобны при обработке данных — особенно во время преобразования, где промежуточные результаты являются временными.

В данной статье описываются некоторые специальные методы использования временных таблиц, помогающие разделять свой код на модули.

## Разделение кода на модули

Тот факт, что временные таблицы можно просмотреть где угодно в сеансе пользователя, можно использовать для модульной организации кода приложения.

Создадим рабочий пример.

Представленная ниже хранимая процедура формирует DDL, необходимый для обновления статистики по каждому столбцу в базе данных:

```
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

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
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

Обратите внимание, что с самими данными никакие операции не выполняются. Процедура просто создала DDL и сохранила во временной таблице.

Тем не менее, хранилище данных SQL может использовать эту временную таблицу вне процедуры, в которой она была создана. В этом отличие от SQL Server. На самом деле временную таблицу можно использовать **где угодно** внутри сеанса.

Это может привести к большей модульности и управляемости кода.

```
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

В некоторых случаях с помощью этого метода можно заменить встраиваемые функции и функции с несколькими инструкциями.

> [AZURE.NOTE]Также можно расширить это решение. Например, если необходимо обновить только одну таблицу, достаточно будет просто отфильтровать таблицу #stats\_ddl.

## Ограничения временной таблицы
В хранилище данных SQL есть несколько ограничений, касающихся реализации временных таблиц.

Ниже перечислены основные ограничения:

- глобальные временные таблицы не поддерживаются;
- для временных таблиц нельзя создавать представления.


## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->

<!--Article references-->
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->