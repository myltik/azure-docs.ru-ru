---
title: "Создание суррогатных ключей с использованием свойства IDENTITY | Документация Майкрософт"
description: "Узнайте, как использовать свойство IDENTITY для создания суррогатных ключей для таблиц."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: e10b58743fad5f7c2c4f00b51f06d4ec9bcb6768
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>Создание суррогатных ключей с использованием свойства IDENTITY
> [!div class="op_single_selector"]
> * [Обзор][Overview]
> * [Типы данных][Data Types]
> * [Распределение][Distribute]
> * [Индекс][Index]
> * [Секция][Partition]
> * [Статистика][Statistics]
> * [Временные таблицы][Temporary]
> * [Удостоверение][Identity]
> 
> 

Многие разработчики моделей данных создают суррогатные ключи для таблиц, когда проектируют модели хранилища данных. Чтобы просто и эффективно достичь этой цели, не оказывая влияния на производительность загрузки, можно использовать свойство IDENTITY. 

## <a name="get-started-with-identity"></a>Приступая к работе со свойством IDENTITY
Свойство IDENTITY можно определить для таблицы при ее создании, используя синтаксис, как в приведенной ниже инструкции.

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Затем можно использовать `INSERT..SELECT` для заполнения таблицы.

## <a name="behavior"></a>Поведение
Свойство IDENTITY позволяет выполнять развертывание по всем распределениям в хранилище данных, не влияя на производительность загрузки. Поэтому реализация IDENTITY ориентирована на достижение этих целей. В этом разделе описываются особенности этой реализации, что поможет вам лучше понять их.  

### <a name="allocation-of-values"></a>Распределение значений
Свойство IDENTITY не гарантирует порядок, в котором распределяются суррогатные значения, что отражает поведение SQL Server и базы данных SQL Azure. Тем не менее в хранилище данных SQL Azure отсутствие подобной гарантии более заметно. 

Ниже приведен характерный пример.

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

В приведенном выше примере две строки попали в распределение 1. У первой строки есть суррогатное значение 1 в столбце `C1`, а у второй строки есть суррогатное значение 61. Оба эти значения были созданы свойством IDENTITY. Тем не менее распределение этих значений не является связанным. В этом весь замысел.

### <a name="skewed-data"></a>Неравномерные данные 
Диапазон значений определенного типа данных равномерно размещается в распределениях. Если распределенная таблица содержит неравномерные данные, то диапазон значений, доступных для типа данных, может быть преждевременно исчерпан. Например, если все данные попадают в отдельное распределение, фактически таблица имеет доступ к только одной шестидесятой части значений этого типа данных. По этой причине свойство IDENTITY ограничено следующими типами данных: `INT` и `BIGINT`.

### <a name="selectinto"></a>SELECT..INTO
При выборке существующего столбца IDENTITY в новую таблицу новый столбец наследует свойство IDENTITY, если только не выполняется одно из следующих условий:
- инструкция SELECT содержит операцию соединения;
- несколько инструкций SELECT соединены с помощью инструкции UNION;
- столбец IDENTITY более одного раза указан в списке инструкции SELECT;
- столбец IDENTITY является частью выражения.
    
Если выполнено одно из этих условий, создается столбец NOT NULL и свойство IDENTITY не наследуется.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) выполняется SQL Server так же, как и SELECT..INTO. Тем не менее невозможно указать свойство IDENTITY в определении столбца в части `CREATE TABLE` инструкции. Невозможно также использовать функцию IDENTITY в части `SELECT` инструкции CTAS. Для заполнения таблицы необходимо использовать `CREATE TABLE`, чтобы определить таблицу, а затем указать `INSERT..SELECT`, чтобы ее заполнить.

## <a name="explicitly-insert-values-into-an-identity-column"></a>Явная вставка значений в столбец IDENTITY 
Хранилище данных SQL поддерживает синтаксис `SET IDENTITY_INSERT <your table> ON|OFF`. Этот синтаксис позволяет явно вставить значения в столбец IDENTITY.

Многие разработчики моделей данных используют в измерениях предопределенные отрицательные значения для определенных строк. Например, значение -1 или строка "неизвестный элемент". 

Приведенный ниже сценарий показывает, как явным образом добавить эту строку с помощью инструкции SET IDENTITY_INSERT.

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>Загрузка данных в таблицу со свойством IDENTITY

Наличие свойства IDENTITY определенным образом отражается на коде для загрузки данных. В этом разделе описываются некоторые основные схемы загрузки данных в таблицы с использованием свойства IDENTITY. 

### <a name="load-data-with-polybase"></a>Загрузка данных с помощью PolyBase
Чтобы загрузить данные в таблицу и создать суррогатный ключ с помощью свойства IDENTITY, создайте таблицу и выполните инструкцию INSERT..SELECT или INSERT..VALUES для загрузки данных.

В следующем примере представлена базовая схема.
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> В настоящее время невозможно использовать `CREATE TABLE AS SELECT` при загрузке данных в таблицу со столбцом IDENTITY.
> 

Дополнительные сведения о загрузке данных с помощью инструмента массового копирования (BCP) см. в следующих статьях:

- [Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL (PolyBase)][]
- [Руководство по использованию PolyBase в хранилище данных SQL][]

### <a name="load-data-with-bcp"></a>Загрузка данных с помощью BCP
BCP — программа командной строки, которую можно использовать для загрузки данных в хранилище данных SQL. Один из ее параметров (-E) управляет поведением BCP при загрузке данных в таблицу со столбцом IDENTITY. 

Если указан параметр -E, сохраняются значения, хранящихся во входном файле для столбца со свойством IDENTITY. Если параметр -E *не* указан, то значения в этом столбце игнорируются. Если столбец идентификаторов не включен, данные загружается в обычном режиме. Значения создаются в соответствии с политикой шага приращения и начального значения свойства.

Дополнительные сведения о загрузке данных с помощью BCP см. в следующих статьях:

- [Загрузка данных с помощью bcp][]
- [bcp Utility][] (Служебная программа BCP)

## <a name="catalog-views"></a>Представления каталога
Хранилище данных SQL поддерживает представление каталога `sys.identity_columns`. Это представление можно использовать для определения столбца со свойством IDENTITY.

Чтобы лучше понять схему базы данных, в этом примере показано, как интегрировать `sys.identity_columns` с другими системными представлениями каталога.

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Ограничения
Свойство IDENTITY невозможно использовать в следующих случаях:
- когда типом данных столбца не является INT или BIGINT;
- когда у столбец является также ключом распределения;
- когда таблица является внешней. 

В хранилище данных SQL не поддерживаются следующие связанные функции:

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>Задачи

Этот раздел содержит пример кода, который можно использовать для выполнения распространенных задач при работе со столбцами IDENTITY.

> [!NOTE] 
> Столбец C1 является столбцом IDENTITY во всех следующих задачах.
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Поиск максимального распределенного значения в таблице
Используйте функцию `MAX()`, чтобы определить максимальное значение в распределенной таблице.

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Поиск начального значения и шага приращения для свойства IDENTITY
Представления каталога можно использовать для обнаружения значения шага приращения идентификатора и начального значения конфигурации для таблицы. Для этого можно выполнить приведенный ниже запрос. 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о разработке таблиц см. в статьях, посвященных [общим сведениям о таблицах][Overview], [табличным типам данных][Data Types], [распределению][Distribute], [индексированию][Index] и [секционированию таблиц][Partition], а также [временным таблицам][Temporary]. 
* Дополнительные рекомендации см. в статье [Рекомендации по использованию хранилища данных SQL][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[Загрузка данных с помощью bcp]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL (PolyBase)]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[Руководство по использованию PolyBase в хранилище данных SQL]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[bcp Utility]: https://msdn.microsoft.com/library/ms162802.aspx (Служебная программа BCP)
  

<!--Other Web references-->  
