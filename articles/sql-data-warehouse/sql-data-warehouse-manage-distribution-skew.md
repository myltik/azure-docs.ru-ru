<properties
   pageTitle="Управление неравномерным распределением таблиц | Microsoft Azure"
   description="Рекомендации, помогающие определить неравномерность распределения данных в таблицах."
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
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Управление неравномерным распределением таблиц
Если данные таблицы распределяются с помощью метода хэш-распределения, существует вероятность, что распределение данных будет "неравномерным".

Если вы определили, что в некоторых распределениях непропорционально больше данных, чем в других, то говорят, что таблица неравномерная. В зависимости от степени неравномерности ее можно устранить. Чрезмерная неравномерность распределения данных повлияет на производительность запросов, так как распределенные вычислительные ресурсы не будут потребляться равномерно.

Эта статья поможет выявить неравномерность данных в хэш-распределенной таблице.

## Поиск неравномерности распределения

Запрос, подобный приведенному ниже представлению, может помочь выявить неравномерные таблицы.

```sql
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
select 
	SUBSTRING(@@version,34,4)															AS  [build_number]
,	GETDATE()																			AS  [execution_time]
,	DB_NAME()																			AS  [database_name]
,	s.name																				AS  [schema_name]
,	t.name																				AS  [table_name]
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)												AS  [two_part_name]
,	nt.[name]																			AS  [node_table_name]
,	ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))					AS  [node_table_name_seq]
,	tp.[distribution_policy_desc]														AS  [distribution_policy_name]
,	nt.[distribution_id]																AS  [distribution_id]
,	nt.[pdw_node_id]																	AS  [pdw_node_id]
,	pn.[type]																			AS	[pdw_node_type]
,	pn.[name]																			AS	[pdw_node_name]
,	nps.[partition_number]																AS	[partition_nmbr]
,	nps.[reserved_page_count]															AS	[reserved_space_page_count]
,	nps.[reserved_page_count] - nps.[used_page_count]									AS	[unused_space_page_count]
,	nps.[in_row_data_page_count] 
	+ nps.[row_overflow_used_page_count] + nps.[lob_used_page_count]					AS  [data_space_page_count]
,	nps.[reserved_page_count] 
	- (nps.[reserved_page_count] - nps.[used_page_count]) 
	- ([in_row_data_page_count]+[row_overflow_used_page_count]+[lob_used_page_count])	AS  [index_space_page_count]
,	nps.[row_count]																		AS  [row_count]
from sys.schemas s
join sys.tables t								ON	s.[schema_id]			= t.[schema_id]
join sys.pdw_table_distribution_properties	tp	ON	t.[object_id]			= tp.[object_id]
join sys.pdw_table_mappings tm					ON	t.[object_id]			= tm.[object_id]
join sys.pdw_nodes_tables nt					ON	tm.[physical_name]		= nt.[name]
join sys.dm_pdw_nodes pn 						ON  nt.[pdw_node_id]		= pn.[pdw_node_id]
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.[object_id]			= nps.[object_id]
												AND nt.[pdw_node_id]		= nps.[pdw_node_id]
												AND nt.[distribution_id]	= nps.[distribution_id]
)
, size
AS
(
SELECT	[build_number]
,		[execution_time]
,		[database_name]
,		[schema_name]
,		[table_name]
,		[two_part_name]
,		[node_table_name]
,		[node_table_name_seq]
,		[distribution_policy_name]
,		[distribution_id]
,		[pdw_node_id]
,		[pdw_node_type]
,		[pdw_node_name]
,		[partition_nmbr]
,		[reserved_space_page_count]
,		[unused_space_page_count]
,		[data_space_page_count]
,		[index_space_page_count]
,		[row_count]
,		([reserved_space_page_count] * 8.0)				AS [reserved_space_KB]
,		([reserved_space_page_count] * 8.0)/1024		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1048576		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1073741824	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1024		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1048576		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1073741824	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1024		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1048576		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1073741824	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1024		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1048576		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1073741824	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

После создания представления мы можем просто запросить его, чтобы проверить неравномерность в таблицах, используя запрос, аналогичный показанному ниже.

```sql
SELECT	[two_part_name]
,		[distribution_id]
,		[row_count]
,		[reserved_space_GB]
,		[unused_space_GB]
,		[data_space_GB]
,		[index_space_GB]
FROM	[dbo].[vDistributionSkew]
WHERE	[table_name] = 'FactInternetSales'
ORDER BY [row_count] DESC
```

>[AZURE.NOTE] Распределенные таблицы ROUND\_ROBIN не должны быть неравномерными. Они спроектированы так, что данные равномерно распределяются между узлами.

## Устранение неравномерности данных
Бывают случаи, когда неравномерность следует сохранить. Обычно это имеет место при присоединении к таблице по общему ключу распределения.

Тем не менее, чтобы устранить неравномерность данных, обычно выбирается другой столбец. Еще одним вариантом является использование ROUND\_ROBIN вместо HASH.

Обратитесь к разделу рекомендаций в статье о [хэш-распределении][], чтобы ознакомиться с инструкциями по выбору другого столбца.

## Дальнейшие действия
Дополнительную информацию о распределении таблицы см. в следующих статьях.

* [конструктору таблиц;][]
* [Хэш-распределение][]

<!--Image references-->

<!--Article references-->
[конструктору таблиц;]: sql-data-warehouse-develop-table-design.md
[Хэш-распределение]: sql-data-warehouse-develop-hash-distribution-key.md
[хэш-распределении]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->