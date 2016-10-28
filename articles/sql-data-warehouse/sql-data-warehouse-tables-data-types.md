<properties
   pageTitle="Типы данных таблиц в хранилище данных SQL | Microsoft Azure"
   description="Начало работы с типами данных таблиц хранилища данных SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Типы данных таблиц в хранилище данных SQL

> [AZURE.SELECTOR]
- [Обзор][]
- [Типы данных][]
- [Распределение][]
- [Индекс][]
- [Секция][]
- [Статистика][]
- [Временные таблицы][]

Хранилище данных SQL поддерживает самые распространенные типы данных. Ниже приведен список типов данных, поддерживаемых хранилищем данных SQL. Дополнительные сведения о поддержке типов данных см. в статье об [инструкции CREATE TABLE][].

|**Поддерживаемые типы данных**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binary][]|[float][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[char][]|[money][]|[time][]|
[date][]|[nchar][]|[tinyint][]|
[datetime][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[real][]|[varbinary][]|
[datetimeoffset][]|[smalldatetime][]|[varchar][]|


## Рекомендации относительно типов данных

 При определении типов столбцов рекомендуется использовать поддерживаемый тип данных с наименьшим размером. Это позволит повысить производительность запросов. Это особенно важно для столбцов CHAR и VARCHAR. Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25). Не рекомендуется использовать по умолчанию длинные значения столбцов. Кроме того, по возможности определяйте столбцы как VARCHAR, а не [NVARCHAR][]. По возможности используйте NVARCHAR(4000) или VARCHAR(8000) вместо NVARCHAR(MAX) или VARCHAR(MAX).

## Ограничение Polybase

Если для загрузки таблиц используется Polybase, настройте таблицы, чтобы максимальный размер строки, включая полную длину столбцов переменной длины, не превышал 32 767 байт. Вы можете задать строку с данными переменной длины, размер которой превышает это значение, и загрузить строки с помощью BCP, но вы не сможете использовать Polybase, чтобы загрузить эти данные. Вскоре в Polybase будет добавлена поддержка широких строк.

## Неподдерживаемые типы данных

Перенося базу данных из другой платформы SQL, например Базы данных SQL Azure, вы можете обнаружить некоторые типы данных, которые не поддерживаются в хранилище данных SQL. Ниже приведены неподдерживаемые типы данных, а также альтернативные решения.

|Тип данных|Возможное решение|
|---|---|
|[geometry][]|[varbinary][]|
|[geography][]|[varbinary][]|
|[hierarchyid][]|[NVARCHAR][](4000)|
|[изображение][ntext,text,image]|[varbinary][]|
|[текст][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql\_variant][]|Разделите столбец на несколько строго типизированных столбцов.|
|[таблица][]|Преобразуйте во временные таблицы.|
|[Timestamp][]|Переработайте код, чтобы использовать [datetime2][] и функцию `CURRENT_TIMESTAMP`. По умолчанию поддерживаются только константы, поэтому нельзя использовать current\_timestamp как ограничение по умолчанию. Если нужно перенести значения версии строки из типизированного столбца timestamp, используйте [BINARY][](8) или [VARBINARY][BINARY](8) для значений версии строки NOT NULL или NULL.|
|[xml][]|[varchar][]|
|[Пользовательские типы][]|Преобразуйте обратно в их исходный тип, если это возможно.|
|Значения по умолчанию|Значения по умолчанию поддерживают только литералы и константы. Недетерминированные выражения или функции, такие как `GETDATE()` или `CURRENT_TIMESTAMP`, не поддерживаются.|

Приведенный ниже алгоритм SQL может выполняться в текущей базе данных SQL, чтобы определить столбцы, которые не должны поддерживаться в хранилище данных SQL Azure.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## Дальнейшие действия

Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Overview], [распределению][Distribute], [индексированию][Index] и [секционированию][Partition] таблиц, а также [управлению статистикой таблиц][Statistics] и [временным таблицам][Temporary]. Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL Azure][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Обзор]: ./sql-data-warehouse-tables-overview.md
[Типы данных]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Распределение]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Индекс]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Секция]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Статистика]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Временные таблицы]: ./sql-data-warehouse-tables-temporary.md
[Рекомендации по использованию хранилища данных SQL Azure]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[инструкции CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql\_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[таблица]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[Пользовательские типы]: https://msdn.microsoft.com/library/ms131694.aspx

<!---HONumber=AcomDC_0706_2016-->