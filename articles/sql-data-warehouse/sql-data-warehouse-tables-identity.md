---
title: Использование свойства IDENTITY для создания суррогатных ключей в хранилище данных SQL Azure | Документация Майкрософт
description: Рекомендации и примеры использования свойства IDENTITY для создания суррогатных ключей для таблиц в хранилище данных SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ab028705f5af7c37017d2e697240b7d3436f5f71
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526990"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Использование свойства IDENTITY для создания суррогатных ключей в хранилище данных SQL Azure
Рекомендации и примеры использования свойства IDENTITY для создания суррогатных ключей для таблиц в хранилище данных SQL Azure.

## <a name="what-is-a-surrogate-key"></a>Что такое суррогатный ключ?
Суррогатный ключ таблицы представляет собой столбец с уникальным идентификатором каждой строки. Ключ не генерируется из данных таблицы. Разработчики моделей данных создают суррогатные ключи для таблиц, когда проектируют модели хранилища данных. Чтобы просто и эффективно достичь этой цели, не оказывая влияния на производительность загрузки, можно использовать свойство IDENTITY.  

## <a name="creating-a-table-with-an-identity-column"></a>Создание таблицы со столбцом IDENTITY
Свойство IDENTITY позволяет выполнять развертывание по всем распределениям в хранилище данных, не влияя на производительность загрузки. Поэтому реализация IDENTITY ориентирована на достижение этих целей. 

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

В оставшейся части этого раздела описываются особенности этой реализации, что поможет вам лучше понять их.  

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

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Вставка значений в столбец IDENTITY явным образом 
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

## <a name="loading-data"></a>Загрузка данных

Наличие свойства IDENTITY определенным образом отражается на коде для загрузки данных. В этом разделе описываются некоторые основные схемы загрузки данных в таблицы с использованием свойства IDENTITY. 

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
> В настоящее время невозможно использовать инструкцию CREATE TABLE AS SELECT при загрузке данных в таблицу со столбцом IDENTITY.
> 

Дополнительные сведения о загрузке данных см. в статьях [Разработка процесса извлечения, загрузки и преобразования (ELT) для хранилища данных SQL Azure](design-elt-data-loading.md) и [Рекомендации по загрузке данных в хранилище данных SQL Azure](guidance-for-loading-data.md).


## <a name="system-views"></a>Системные представления
Можно использовать представление каталога [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) для поиска столбца со свойством IDENTITY.

Для лучшего понимания схемы базы данных в этом примере показано, как интегрировать sys.identity_column с остальными представлениями системных каталогов.

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
Свойство IDENTITY не может быть использовано:
- если тип данных столбца не является INT или BIGINT;
- если столбец также является ключом распределения;
- если таблица является внешней. 

В хранилище данных SQL не поддерживаются следующие связанные функции:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)
- [DBCC CHECK_IDENT()](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

## <a name="common-tasks"></a>Стандартные задачи

Этот раздел содержит пример кода, который можно использовать для выполнения распространенных задач при работе со столбцами IDENTITY. 

Столбец C1 является столбцом IDENTITY во всех следующих задачах.
 
 
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

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о разработке таблиц см. в разделе с [обзором таблиц][Обзор].  

