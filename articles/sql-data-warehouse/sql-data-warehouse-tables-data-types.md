---
title: "Типы данных таблиц в хранилище данных SQL | Документация Майкрософт"
description: "Начало работы с типами данных таблиц хранилища данных SQL Azure."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7757de96601c426ff07e94cfa0c12d4dec8f06f5
ms.contentlocale: ru-ru
ms.lasthandoff: 03/22/2017


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Типы данных таблиц в хранилище данных SQL
> [!div class="op_single_selector"]
> * [Обзор][Overview]
> * [Типы данных][Data Types]
> * [Распределение][Distribute]
> * [Индекс][Index]
> * [Секция][Partition]
> * [Статистика][Statistics]
> * [Временные таблицы][Temporary]
> 
> 

Хранилище данных SQL поддерживает самые распространенные типы данных.  Ниже приведен список типов данных, поддерживаемых хранилищем данных SQL.  Дополнительные сведения о поддержке типов данных см. в статье об [инструкции CREATE TABLE][create table].

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
Если для загрузки таблиц используется Polybase, убедитесь, что длина данных не превышает 1 МБ.  Вы можете задать строку с данными переменной длины, размер которой превышает это значение, и загрузить строки с помощью BCP, но вы не сможете использовать Polybase, чтобы загрузить эти данные.  

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
| [table][table] |Преобразуйте во временные таблицы. |
| [timestamp][timestamp] |Переработайте код, чтобы использовать [datetime2][datetime2] и функцию `CURRENT_TIMESTAMP`.  По умолчанию поддерживаются только константы, поэтому нельзя использовать current_timestamp как ограничение по умолчанию. Если нужно перенести значения версии строки из типизированного столбца timestamp, используйте [BINARY][BINARY](8) или [VARBINARY][BINARY](8) для значений версии строки NOT NULL или NULL. |
| [xml][xml] |[varchar][varchar] |
| [Пользовательские типы][user defined types] |Преобразуйте обратно в их исходный тип, если это возможно. |
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
Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах][Overview], [распределению][Distribute], [индексированию][Index] и [секционированию таблиц][Partition], а также [управлению статистикой таблиц][Statistics] и [временным таблицам][Temporary].  Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL Azure][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
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
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx

