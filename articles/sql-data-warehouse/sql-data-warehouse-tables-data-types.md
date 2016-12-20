---
title: "Типы данных таблиц в хранилище данных SQL | Документация Майкрософт"
description: "Начало работы с типами данных таблиц хранилища данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 616bb450217573ebd45060234313af418f525f89


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Типы данных таблиц в хранилище данных SQL
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

Хранилище данных SQL поддерживает самые распространенные типы данных.  Ниже приведен список типов данных, поддерживаемых хранилищем данных SQL.  Дополнительные сведения о поддержке типов данных см. в статье об [инструкции CREATE TABLE][Статья об инструкции CREATE TABLE].

| **Поддерживаемые типы данных** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>Рекомендации относительно типов данных
 При определении типов столбцов рекомендуется использовать поддерживаемый тип данных с наименьшим размером. Это позволит повысить производительность запросов. Это особенно важно для столбцов CHAR и VARCHAR. Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25). Не рекомендуется использовать по умолчанию длинные значения столбцов. Кроме того, по возможности определяйте столбцы как VARCHAR, а не [NVARCHAR][NVARCHAR].  По возможности используйте NVARCHAR(4000) или VARCHAR(8000) вместо NVARCHAR(MAX) или VARCHAR(MAX).

## <a name="polybase-limitation"></a>Ограничение Polybase
Если для загрузки таблиц используется Polybase, настройте таблицы, чтобы максимальный размер строки, включая полную длину столбцов переменной длины, не превышал 32 767 байт.  Вы можете задать строку с данными переменной длины, размер которой превышает это значение, и загрузить строки с помощью BCP, но вы не сможете использовать Polybase, чтобы загрузить эти данные.  Вскоре в Polybase будет добавлена поддержка широких строк.

## <a name="unsupported-data-types"></a>Неподдерживаемые типы данных
Перенося базу данных из другой платформы SQL, например Базы данных SQL Azure, вы можете обнаружить некоторые типы данных, которые не поддерживаются в хранилище данных SQL.  Ниже приведены неподдерживаемые типы данных, а также альтернативные решения.

| Тип данных | Возможное решение |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Разделите столбец на несколько строго типизированных столбцов. |
| [таблица][таблица] |Преобразуйте во временные таблицы. |
| [Timestamp][Timestamp] |Переработайте код, чтобы использовать [datetime2][datetime2] и функцию `CURRENT_TIMESTAMP`.  По умолчанию поддерживаются только константы, поэтому нельзя использовать current_timestamp как ограничение по умолчанию. Если нужно перенести значения версии строки из типизированного столбца timestamp, используйте [BINARY][BINARY](8) или [VARBINARY][BINARY](8) для значений версии строки NOT NULL или NULL. |
| [xml][xml] |[varchar][varchar] |
| [Пользовательские типы][Пользовательские типы] |Преобразуйте обратно в их исходный тип, если это возможно. |
| Значения по умолчанию |Значения по умолчанию поддерживают только литералы и константы.  Недетерминированные выражения или функции, такие как `GETDATE()` или `CURRENT_TIMESTAMP`, не поддерживаются. |

Приведенный ниже алгоритм SQL может выполняться в текущей базе данных SQL, чтобы определить столбцы, которые не должны поддерживаться в хранилище данных SQL Azure.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Обзор], [распределению][Распределение], [индексированию][Индекс], [секционированию таблиц][Секция], [управлению статистикой таблиц][Статистика] и [временным таблицам][Временные].  Рекомендации по использованию хранилища данных SQL Azure см. в [этой статье][Рекомендации по использованию хранилища данных SQL Azure].

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
[Статья об инструкции CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
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
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[таблица]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[Timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[Пользовательские типы]: https://msdn.microsoft.com/library/ms131694.aspx



<!--HONumber=Nov16_HO3-->


