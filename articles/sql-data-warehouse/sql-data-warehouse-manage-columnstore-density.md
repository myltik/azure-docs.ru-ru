<properties
   pageTitle="Управление неравномерным распределением таблиц | Microsoft Azure"
   description="Рекомендации, помогающие определить неравномерность распределения таблиц"
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

# Управление индексами columnstore
Индексы columnstore — это фундамент хранилища данных SQL Azure. Поддержание индексов в оптимальном состоянии обеспечит максимальную производительность системы.

В этой статье объясняется, как запросить метаданные индекса columnstore для таблиц, которые помогают диагностировать проблемы и быстро их устранять.

## Запрос метаданных columnstore
Для оценки плотности индекса columnstore нужно выполнить запрос системных метаданных. Ниже приведен пример того, сведения какого рода можно обнаружить.

```
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

После создания представления можно легко анализировать метаданные columnstore. Ниже приводится пример запроса.

```
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## Повышение плотности индекса columnstore
После выполнения запроса можно приступить к просмотру данных и анализу результатов.

Рассмотрим несколько моментов.

| столбец | Как использовать эти данные |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Если таблица секционирована, то счетчики открытых групп строк могут быть больше. Каждая секция в распределении теоретически может иметь связанную с ней открытую группу строк. Это следует учитывать при анализе. Небольшую секционированную таблицу можно оптимизировать, полностью отменив секционирование, поскольку это улучшит сжатие. |
| [row\_count\_total] | Общее число строк в таблице. Это значение можно использовать, например, для расчета процента строк в сжатом состоянии. |
| [row\_count\_per\_distribution\_MAX] | Если все строки распределены равномерно, это значение будет целевым числом строк для каждого распределения. Сравните это значение с compressed\_rowgroup\_count. |
| [COMPRESSED\_rowgroup\_rows] | Общее число строк в формате columnstore для таблицы |
| [COMPRESSED\_rowgroup\_rows\_AVG] | Если среднее количество строк существенно меньше максимального числа строк для группы строк, возможно, стоит использовать инструкции CTAS или ALTER INDEX REBUILD для повторного сжатия данных. |
| [COMPRESSED\_rowgroup\_count] | Число групп строк в формате columnstore. Очень большое относительно таблицы число указывает на то, что плотность columnstore низка. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | Логически удаленные строки в формате columnstore. Если это число велико относительно размера таблицы, возможно, стоит создать секцию заново или перестроить индекс, поскольку в этом случае они удаляются физически. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | Используйте это значение вместе со столбцами AVG и MAX, чтобы оценить диапазон размеров для групп строк в индексе columnstore. Небольшое значение относительно порогового значения загрузки (102 400 для распределения с выравниванием по секциям) говорит о том, что загрузку данных можно оптимизировать. |
| [COMPRESSED\_rowgroup\_rows\_MAX] | То же, что и выше |
| [OPEN\_rowgroup\_count] | Открытые группы строк допускаются. Можно ожидать наличия одной открытой группы строк на каждое распределение таблиц (60). Очень большие числа говорят о загрузке данных в разные секции. Внимательно проверьте стратегию секционирования, чтобы убедиться в ее эффективности |
| [OPEN\_rowgroup\_rows] | Каждая группа строк может иметь максимум 1 048 576 строк. Используйте это значение для оценки текущей наполненности открытых групп строк |
| [OPEN\_rowgroup\_rows\_MIN] | Наличие открытых групп указывает, что либо данные медленно загружаются в таблицу, либо предыдущая загрузка привела к переносу оставшихся строк в эту группу строк. С помощью столбцов MIN, MAX, AVG можно узнать, сколько данных находится в открытых группах строк. Для небольших таблиц это может быть 100 % всех данных! В этом случае следует использовать инструкцию ALTER INDEX REBUILD, чтобы принудительно перенести данные в columnstore. |
| [OPEN\_rowgroup\_rows\_MAX] | То же, что и выше |
| [OPEN\_rowgroup\_rows\_AVG] | То же, что и выше |
| [CLOSED\_rowgroup\_rows] | Для проверки оцените число строк в закрытых группах строк. Если |
| [CLOSED\_rowgroup\_count] | Число закрытых групп строк должно быть небольшим, если они есть вообще. Закрытые группы строк можно преобразовать в сжатые группы строк с помощью инструкции ALTER INDEX... REORGANISE. Однако обычно это не требуется. Закрытые группы автоматически преобразуются в группы строк columnstore фоновым процессом "переноса кортежей". |
| [CLOSED\_rowgroup\_rows\_MIN] | Закрытые группы строк должны иметь очень высокий коэффициент заполнения. Если коэффициент заполнения для закрытой группы строк низок, то требуется дальнейший анализ columnstore. |
| [CLOSED\_rowgroup\_rows\_MAX] | То же, что и выше |
| [CLOSED\_rowgroup\_rows\_AVG] | То же, что и выше |

## Управление индексами
Повторно сжать группы строк можно, создав секции с помощью инструкции [CTAS][] или перестроив сам индекс с помощью инструкции [ALTER INDEX][]. Инструкция [CTAS][] обычно работает быстрее, чем [ALTER INDEX][] — особенно для больших таблиц или секций. Однако для небольших таблиц или секций инструкция [ALTER INDEX][] чаще всего оказывается намного удобнее.

>[AZURE.NOTE] ALTER INDEX...REBUILD выполняется в автономном режиме. ALTER INDEX...REORGANISE выполняется в сети. Однако REORGANISE не затрагивает открытые группы строк. Чтобы включить открытые группы строк, нужно использовать инструкцию ALTER INDEX...REBUILD.

При перестроении индексов, особенно больших таблиц, часто помогает подключение дополнительных ресурсов. Хранилище данных SQL Azure имеет функцию управления рабочими нагрузками, которая позволяет выделять дополнительную память для пользователя. Резервирование памяти для перестроения индекса описывается в разделе об управлении рабочими нагрузками в статье о [параллелизме][].

## Дальнейшие действия
Дополнительные сведения о повторном создании секций с помощью `CTAS` см. в следующих статьях:

* [Секционирование таблиц][]
* [параллелизм;][]

Более подробные рекомендации по управлению индексами см. в статье об [управлении индексами][].

Дополнительные советы по управлению см. в обзоре по [управлению][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Секционирование таблиц]: sql-data-warehouse-develop-table-partitions.md
[параллелизм;]: sql-data-warehouse-develop-concurrency.md
[параллелизме]: sql-data-warehouse-develop-concurrency.md
[управлению]: sql-data-warehouse-manage-monitor.md
[управлении индексами]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/ru-RU/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->