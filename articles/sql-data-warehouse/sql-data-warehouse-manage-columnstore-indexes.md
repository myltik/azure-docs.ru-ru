<properties
   pageTitle="Управление индексами columnstore в хранилище данных SQL Azure | Microsoft Azure"
   description="Учебник по управлению индексами columnstore для улучшения сжатия и повышения производительности запросов в хранилище данных SQL Azure."
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

# Управление индексами columnstore в хранилище данных SQL Azure

В этом учебнике содержатся сведения об управлении индексами columnstore для повышения производительности запросов.

Запросы к индексам columnstore выполняются эффективнее, когда индекс сжимает строки в группы строк, состоящие из миллиона строк (а точнее — из 1 048 576 строк). Это обеспечивает лучшее сжатие и максимальную производительность запросов. Однако в некоторых случаях в группах строк может находиться значительно меньшее количество строк. Если группы строк неплотно заполнены, рекомендуется внести изменения.

Из этого учебного курса вы узнаете следующее:

- Использование метаданных для определения среднего количества строк в каждой группе строк.
- Изучение основной причины неплотного заполнения групп строк.
- Перестроение индекса columnstore для повторного сжатия всех строк в новые группы строк. 

Основные сведения об индексах columnstore см. в [руководстве по columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## Шаг 1. Создание представления, отображающего метаданные группы строк

В этом представлении вычисляется среднее количество строк для каждой группы строк. Здесь также находятся дополнительные сведения о группах строк.

```sql
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

## Шаг 2. Выполнение запроса к представлению

После создания представления выполните этот пример запроса, чтобы просмотреть метаданные группы строк для индекса columnstore.

```sql
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

## Шаг 3. Анализ результатов

После выполнения запроса можно начать просмотр данных и анализ результатов. В таблице ниже показано, на какие данные следует обратить внимание в анализе групп строк.


| столбец | Как использовать эти данные |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Если таблица секционирована, то счетчики открытых групп строк могут быть больше. Каждая секция в распределении теоретически может иметь связанную с ней открытую группу строк. Это следует учитывать при анализе. Небольшую секционированную таблицу можно оптимизировать, полностью отменив секционирование, поскольку это улучшит сжатие. |
| [row\_count\_total] | Общее количество строк в таблице. Например, это значение можно использовать для вычисления процента строк в сжатом состоянии. |
| [row\_count\_per\_distribution\_MAX] | Если все строки распределены равномерно, это значение будет целевым числом строк для каждого распределения. Сравните это значение с compressed\_rowgroup\_count. |
| [COMPRESSED\_rowgroup\_rows] | Общее количество строк в формате columnstore для таблицы. |
| [COMPRESSED\_rowgroup\_rows\_AVG] | Если среднее количество строк существенно меньше максимального числа строк для группы строк, возможно, стоит использовать инструкции CTAS или ALTER INDEX REBUILD для повторного сжатия данных. |
| [COMPRESSED\_rowgroup\_count] | Число групп строк в формате columnstore. Очень большое относительно таблицы число указывает на то, что плотность columnstore низка. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | Логически удаленные строки в формате columnstore. Если это число велико относительно размера таблицы, возможно, стоит создать секцию заново или перестроить индекс, поскольку в этом случае они удаляются физически. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | Используйте это значение вместе со столбцами AVG и MAX, чтобы оценить диапазон размеров для групп строк в индексе columnstore. Небольшое значение относительно порогового значения загрузки (102 400 для распределения с выравниванием по секциям) говорит о том, что загрузку данных можно оптимизировать. |
| [COMPRESSED\_rowgroup\_rows\_MAX] | То же, что и выше |
| [OPEN\_rowgroup\_count] | Открытые группы строк допускаются. Можно ожидать наличия одной открытой группы строк на каждое распределение таблиц (60). Очень большие числа говорят о загрузке данных в разные секции. Внимательно проверьте стратегию секционирования, чтобы убедиться в ее эффективности |
| [OPEN\_rowgroup\_rows] | Каждая группа строк может иметь максимум 1 048 576 строк. Используйте это значение для оценки текущей наполненности открытых групп строк |
| [OPEN\_rowgroup\_rows\_MIN] | Наличие открытых групп указывает, что либо данные медленно загружаются в таблицу, либо предыдущая загрузка привела к переносу оставшихся строк в эту группу строк. С помощью столбцов MIN, MAX, AVG можно узнать, сколько данных находится в открытых группах строк. Для небольших таблиц это может быть 100 % всех данных! В этом случае следует использовать инструкцию ALTER INDEX REBUILD, чтобы принудительно перенести данные в columnstore. |
| [OPEN\_rowgroup\_rows\_MAX] | То же, что и выше |
| [OPEN\_rowgroup\_rows\_AVG] | То же, что и выше |
| [CLOSED\_rowgroup\_rows] | Для проверки оцените число строк в закрытых группах строк. |
| [CLOSED\_rowgroup\_count] | Число закрытых групп строк должно быть небольшим, если они есть вообще. Закрытые группы строк можно преобразовать в сжатые группы строк с помощью инструкции ALTER INDEX... REORGANISE. Однако обычно это не требуется. Закрытые группы автоматически преобразуются в группы строк columnstore фоновым процессом "переноса кортежей". |
| [CLOSED\_rowgroup\_rows\_MIN] | Закрытые группы строк должны иметь очень высокий коэффициент заполнения. Если коэффициент заполнения для закрытой группы строк низок, то требуется дальнейший анализ columnstore. |
| [CLOSED\_rowgroup\_rows\_MAX] | То же, что и выше |
| [CLOSED\_rowgroup\_rows\_AVG] | То же, что и выше |


## Шаг 4. Изучение основной причины

Изучение основной причины позволит понять, можно ли изменить разработку, загрузку таблицы или выполнить другие действия, которые помогут повысить плотность строк в группах строк и тем самым улучшить производительность запросов и сжатие.

По приведенным ниже причинам количество строк в каждой группе будет значительно меньше 1 048 576. Кроме того, вместо перехода в сжатую группу строки могут попасть в разностную группу строк.

1. Высокая интенсивность операций DML
2. Небольшой объем операций загрузки или потоковые загрузки
3. Слишком много секций

### Высокая интенсивность операций DML** 

Интенсивные операции DML по обновлению и удалению строк приводят к неэффективной работе columnstore. Это особенно верно в случае изменения большей части строк в группе строк.

- Если строка удаляется из сжатой группы строк, она лишь логически помечается как удаленная. Строка остается в сжатой группе строк до перестроения секции или таблицы.
- При вставке строка добавляется во внутреннюю таблицу rowstore, называемую разностной группой строк. Вставленная строка не преобразуется в columnstore до тех пор, пока разностная группа строк не будет заполнена и помечена как закрытая. Группы строк закрываются после достижения максимального объема в 1 048 576 строк. 
- Обновление строки в формате columnstore обрабатывается как логическое удаление, а затем — как вставка. Вставленная строка будет сохранена в разностном хранилище.

Пакетные операции обновления и вставки, превышающие пороговое значение в 102 400 строк для распределения с выравниванием по секциям, записываются непосредственно в формате columnstore. Чтобы это произошло, при условии равномерного распределения потребуется изменить более 6,144 млн строк в рамках одной операции. Если количество строк для распределения с выравниванием по секциям не превышает 102 400, строки будут отправляться в разностное хранилище и останутся там до тех пор, пока не будет вставлено или изменено количество строк, достаточное для закрытия группы строк, или не будет перестроен индекс.

### Небольшой объем операций загрузки или потоковые загрузки

Небольшие загрузки, поступающие в хранилище данных SQL, иногда называют потоковыми. Обычно они представляют собой практически постоянный поток данных, принимаемых системой. Однако поскольку этот поток является не совсем непрерывным, объем строк невелик. Чаще всего количество данных значительно ниже порогового значения, необходимого для непосредственной загрузки в формате columnstore.

В таких ситуациях рекомендуется сначала разместить данные в хранилище BLOB-объектов Azure, накопить их и лишь затем загрузить. Этот метод часто называют *микропакетной обработкой*.

### Слишком много секций

У вас может быть слишком много секций. В архитектуре вычислений с массовым параллелизмом (MPP) одна определяемая пользователем таблица является распределенной и на самом деле реализована в виде 60 таблиц. Поэтому каждый индекс columnstore представлен в виде 60 индексов columnstore. Аналогично, каждая секция реализована в этих 60 индексах columnstore. В этом заключается **существенное отличие** от SQL Server, имеющего архитектуру симметричной мультипроцессорной обработки (SMP).

При загрузке 1 048 576 строк в одну секцию таблицы SMP SQL Server она будет сжата в columnstore. Однако если вы загружаете 1 048 576 строк в одну секцию таблицы хранилища данных SQL, в каждом из 60 распределений будет размещено только 17 467 строк (при условии равномерного распределения данных). Так как 17 467 меньше порогового значения, составляющего 102 400 строк для каждого распределения, данные в хранилище SQL будут находиться в разностных группах строк. Таким образом, чтобы загрузить строки непосредственно в сжатом формате columnstore, необходимо загрузить более 6,1444 млн строк (60 x 102 400) в одну секцию таблицы хранилища данных SQL.

## Шаг 5. Выделение дополнительных вычислительных ресурсов

При перестроении индексов, особенно в больших таблицах, часто требуются дополнительные ресурсы. Хранилище данных SQL имеет функцию управления рабочими нагрузками, которая позволяет выделять дополнительную память для пользователя. Резервирование памяти для перестроения индекса описывается в разделе об управлении рабочими нагрузками в статье о [параллелизме][].

## Шаг 6. Перестроение индекса для увеличения среднего количества строк в группе строк

Чтобы объединить существующие сжатые группы строк и переместить разностные группы строк в columnstore, необходимо перестроить индекс. Как правило, перестроение всего индекса связано с очень большим объемом данных, поэтому рекомендуется перестроить одну или несколько секций. Существует два способа перестроения индекса в хранилище данных SQL.

1. Использование [ALTER INDEX][] для перестроения секции.
2. Использование [CTAS][] для повторного создания секции в новой таблице и последующее переключение секций для перемещения секции обратно в исходную таблицу.

Какой способ лучше? Для больших объемов данных использование [CTAS][] обычно превосходит по скорости [ALTER INDEX][]. Для небольших объемов данных можно использовать более простой вариант с [ALTER INDEX][].

### Способ 1. Использование инструкции ALTER INDEX для перестроения небольших объемов данных в автономном режиме

В этих примерах показано перестроение всего индекса columnstore и перестроение одной секции. В больших таблицах целесообразно перестроить только одну секцию. Эта операция выполняется в автономном режиме.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

Дополнительные сведения см. в разделе ALTER INDEX REBUILD статьи [Columnstore Indexes Defragmentation (Дефрагментация индексов columnstore)](https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1) и в разделе о синтаксисе [ALTER INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188388.aspx).

### Способ 2. Использование CTAS для перестроения и переключения секций с большими объемами данных в сетевом режиме

В этом примере для перестроения секции таблицы используется [CTAS][] и переключение секций.


```sql
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

Дополнительные сведения о повторном создании секций с помощью `CTAS` см. в статье [Секционирование таблиц][] и статье о [параллелизме][].


## Дальнейшие действия

Более подробные рекомендации по управлению индексами см. в статье об [управлении индексами][].

Дополнительные советы по управлению см. в обзоре [управления][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Секционирование таблиц]: sql-data-warehouse-develop-table-partitions.md
[параллелизме]: sql-data-warehouse-develop-concurrency.md
[управления]: sql-data-warehouse-manage-monitor.md
[управлении индексами]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->