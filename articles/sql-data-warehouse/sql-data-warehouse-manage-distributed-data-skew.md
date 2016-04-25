<properties
   pageTitle="Управление смещением данных для распределенных таблиц в хранилище данных SQL | Microsoft Azure"
   description="Сведения о поиске и устранении смещения данных при неравномерном распределении строк по всем распределениям в таблице хэш-распределения в хранилище данных SQL Azure." 
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
   ms.date="04/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Управление смещением данных для распределенных таблиц в хранилище данных SQL Azure
В этом руководстве рассматривается смещение данных в хэш-распределенных таблицах и приведены советы по устранению этой проблемы.

Если данные таблицы распределяются путем хэш-распределения, существует вероятность, что распределение будет неравномерным, т. е. в некоторых местах будет непропорционально больше данных. Чрезмерное смещение данных может повлиять на производительность запроса, так как выполнение распределенного запроса завершится только после длительного процесса распределения. В таком случае необходимо предпринять меры по устранению проблемы.

Изучив данный учебник, вы научитесь:

- использование метаданных для определения, в каких таблицах смещены данные;
- советы по устранению смещения данных;
- повторное создание таблицы с целью устранения смещения данных.


## Шаг 1. Создание представления, с помощью которого можно определить смещение данных

Создайте это представление, чтобы определить таблицы со смещенными данными.

```sql
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
SELECT 
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
,		([reserved_space_page_count] * 8.0)/1000		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1000000		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1000000000	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1000		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1000000		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1000000000	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1000		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1000000		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1000000000	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1000		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1000000		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1000000000	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

## Шаг 2. Выполнение запроса на представление

После создания представления выполните этот пример запроса, чтобы определить таблицу со смещенными данными.

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

## Шаг 3. Определение необходимости в устранении смещения данных

Чтобы решить, следует ли устранять смещение данных в таблице, нужно узнать как можно больше об объемах данных и запросах в рамках рабочей нагрузки.

В основе распределения данных лежит поиск баланса между уменьшением степени смещения и перемещения данных. Это могут быть противоположные цели, и иногда нужно сохранить смещение данных, чтобы уменьшить степень их перемещения. Например, если столбец распределения используется и для соединения, и для объединения, степень перемещения данных уменьшится. Преимущество минимального перемещения данных может компенсировать последствия смещения данных.

## Шаг 4. Устранение смещения данных

Смещение данных можно устранить двумя способами. Используйте один из них, если вам нужно устранить такую проблему.

### Способ 1. Повторное создание таблицы с другим столбцом распределения

Стандартный способ устранения смещения данных — повторно создать таблицу с другим столбцом распределения. Рекомендации по выбору столбца хэш-распределения см. в статье о [хэш-распределении][]. В примере ниже для повторного создания таблицы с другим столбцом распределения используется оператор \[CTAS\]\[\].

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### Способ 2. Повторное создание таблицы с распределением по принципу циклического перебора

В примере ниже вместо хэш-распределения для повторного создания таблицы используется алгоритм циклического перебора \(ROUND-ROBIN\). В результате применения такого сценария вы получите равномерное распределение данных. Однако обычно это приводит к увеличению степени перемещения данных при запросах.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## Дальнейшие действия
Дополнительные сведения о распределении таблицы см. в следующих статьях:

* [конструктору таблиц;][]
* [Хэш-распределение][]

<!--Image references-->

<!--Article references-->
[конструктору таблиц;]: sql-data-warehouse-develop-table-design.md
[Хэш-распределение]: sql-data-warehouse-develop-hash-distribution-key.md
[хэш-распределении]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->