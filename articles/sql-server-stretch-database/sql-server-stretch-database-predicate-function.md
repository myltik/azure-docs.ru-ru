---
title: "Выбор строк для переноса в базу данных Stretch в Azure | Документация Майкрософт"
description: "Узнайте, как выбрать строки для переноса с помощью функции фильтра."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: f5ef79d9-68ef-4394-a057-d7aac5706b72
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: bcb0a66425439522e0c9a353798ac70505b91e39
ms.openlocfilehash: fc27cd6e25de8e5c3e6b50a0d887755f70d634fd


---
# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Выбор строк для переноса с помощью функции фильтра (база данных Stretch)
Если "холодные" данные хранятся в отдельной таблице, то можно настроить базу данных Stretch для переноса всей таблицы. Если таблица содержит как "горячие", так и "холодные" данные, то, с другой стороны, можно указать функцию фильтра, чтобы выбрать строки для переноса. Предикат фильтра представляет собой встроенную функцию с табличным значением. В этой статье описывается, как написать встроенную функцию с табличным значением для выбора строк для переноса.

> [!NOTE]
> Если указать функцию фильтра, которая работает неэффективно, перенос данных также будет выполняться неэффективно. База данных Stretch применяет функцию фильтра к таблице с помощью оператора CROSS APPLY.
>
>

Если не указать функцию фильтра, будет выполнена миграция всей таблицы.

При запуске мастера включения переноса для базы данных можно перенести всю таблицу или указать простую функцию фильтра. Если вы хотите использовать другой тип функции фильтра, чтобы выбрать строки для миграции, выполните одну из следующих процедур.

* Закройте мастер и выполните инструкцию ALTER TABLE, чтобы включить перенос для таблицы и указать функцию фильтра.
* Выполните инструкцию ALTER TABLE, чтобы указать функцию фильтра после выхода из мастера.

Синтаксис ALTER TABLE для добавления функции описывается далее в этом разделе.

## <a name="basic-requirements-for-the-filter-function"></a>Основные требования к функции фильтра
Встроенная функция с табличным значением, необходимая для предиката фильтра базы данных Stretch, выглядит следующим образом.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE <predicate>
```
Параметрами этой функции должны быть идентификаторы столбцов таблицы.

Привязка к схеме необходима для того, чтобы столбцы, используемые в функции фильтра, не были удалены или изменены.

### <a name="return-value"></a>Возвращаемое значение
Если функция возвращает непустой результат, строка пригодна для миграции. В противном случае (то есть если функция не возвращает результат) строки не подходят для миграции.

### <a name="conditions"></a>Условия
&lt;*Предикат*&gt; может содержать одно или несколько условий, объединенных с помощью логического оператора AND.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Каждое условие, в свою очередь, может содержать одно или несколько простых условий, объединенных с помощью логического оператора OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Простые условия
Простое условие может выполнять одно из следующих сравнений.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

* Сравнение параметра функции с константным выражением. Например, `@column1 < 1000`.

  Ниже приведен пример для проверки того, что значение столбца *date* меньше 1/1/2016.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
  GO

  ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```
* Применение оператора IS NULL или IS NOT NULL к параметру функции.
* Использование оператора IN для сравнения параметра функции со списком постоянных значений.

  Ниже приведен пример, который проверяет, равно ли значение столбца *shipment\_status* `IN (N'Completed', N'Returned', N'Cancelled')`.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
  GO

  ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```

### <a name="comparison-operators"></a>Операторы сравнения
Поддерживаются следующие операторы сравнения.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Константные выражения
Константа, используемая в качестве функции фильтра, может быть любым детерминированным выражением, которое можно вычислить при определении функции. Константные выражения могут содержать следующее.

* Литералы. Пример: `N’abc’, 123`.
* Алгебраические выражения. Пример: `123 + 456`.
* Детерминированные функции. Пример: `SQRT(900)`.
* Детерминированные преобразования, использующие CAST или CONVERT. Пример: `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>Другие выражения
Можно использовать операторы BETWEEN и NOT BETWEEN, если полученная функция соответствует описанным здесь правилам после замены операторов BETWEEN и NOT BETWEEN соответствующими выражениями AND и OR.

Вы не можете использовать вложенные запросы или недетерминированные функции, такие как RAND() или GETDATE().

## <a name="add-a-filter-function-to-a-table"></a>Добавление функции фильтра к таблице
Добавьте функцию фильтра в таблицу, выполнив инструкцию **ALTER TABLE** и указав имеющуюся встроенную функцию с табличным значением в качестве значения параметра **FILTER\_PREDICATE**. Например:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Привязка функции к таблице в качестве предиката позволяет добиться следующего.

* При следующем переносе данных будут перенесены только те строки, для которых функция возвращает непустое значение.
* Столбцы, используемые функцией, привязаны к схеме. Эти столбцы невозможно изменить, пока таблица использует функцию в качестве предиката фильтра.

Удалить встроенную функцию с табличным значением невозможно, пока таблица использует эту функцию в качестве предиката фильтра.

> [!NOTE]
> Чтобы повысить производительность функции фильтрации, создайте индекс столбцов, используемых этой функцией.
>
>

### <a name="passing-column-names-to-the-filter-function"></a>Передача имен столбцов в функцию фильтра
При назначении таблице функции фильтрации укажите имена столбцов, передаваемых в функцию фильтрации, состоящие из одной части. Если при передаче указать имена столбцов, состоящие из трех частей, то это приведет к сбою последующих запросов к таблице с поддержкой Stretch.

Например, если указать имя столбца из трех частей, как показано в следующем примере, инструкция будет выполнена успешно, но последующие запросы к таблице завершатся сбоем.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

Вместо этого укажите функцию фильтрации с именем столбца из одной части, как показано в следующем примере.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="a-nameaddafterwizaadd-a-filter-function-after-running-the-wizard"></a><a name="addafterwiz"></a>Добавление функции фильтра после запуска мастера
Если вы хотите использовать функцию, которую нельзя создать в мастере **включения Stretch для базы данных**, то после выхода из мастера вы можете выполнить инструкцию ALTER TABLE, чтобы указать эту функцию. Тем не менее перед применением функции необходимо остановить текущий перенос данных и вернуть перенесенные данные. (Дополнительные сведения о том, почему это необходимо, см. в разделе [Замена существующей функции фильтров](#replacePredicate).)  

1. Измените направление переноса и верните уже перенесенные данные. После запуска эту операцию нельзя отменить. Кроме того, за исходящую передачу данных (\(исходящий трафик\)) в Azure взимается плата. Дополнительные сведения см. на [этой странице](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  
2. Дождитесь завершения переноса. Вы можете проверить состояние **монитора базы данных Stretch** в SQL Server Management Studio или запросить представление **sys.dm_db_rda_migration_status**. Дополнительные сведения см. в статье [Monitor and troubleshoot data migration](sql-server-stretch-database-monitor.md) (Мониторинг и устранение неполадок переноса данных (база данных Stretch)) или [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  
3. Создайте функцию фильтра, которую требуется применить к таблице.  
4. Добавьте функцию в таблицу и перезапустите перенос данных в Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Фильтрация строк по дате
Ниже приведен пример, который переносит строки, для которых столбец **date** содержит значение, предшествующее 1 января 2016 года.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Фильтрация строк по значению в столбце состояния
Ниже приведен пример, который переносит строки, для которых столбец **status** содержит одно из указанных значений.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Фильтрация строк с помощью скользящего окна
Чтобы отфильтровать строки с помощью скользящего окна, необходимо учитывать следующие требования для функции фильтра.

* Функция должна быть детерминированной. Поэтому невозможно создать функцию, которая автоматически пересчитывает скользящее окно по истечении времени.
* Функция использует привязку к схеме. Поэтому для переноса скользящего окна нельзя просто ежедневно обновлять функцию на месте путем вызова инструкции **ALTER FUNCTION** .

Начните с функции фильтра, аналогичной приведенной в примере, переносящей строки, в которых столбец **systemEndTime** содержит значение, предшествующее 1 января 2016 года.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Примените функцию фильтра к таблице.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Если нужно обновить скользящее окно, выполните указанные далее действия.

1. Создайте функцию, которая задает новое скользящее окно. В примере ниже вместо 1 января 2016 г. выбираются даты, предшествующие 2 января 2016 г.
2. Замените предыдущую функцию фильтра новой, вызвав **ALTER TABLE**, как показано в примере ниже.
3. При необходимости удалите предыдущую, уже не используемую функцию фильтра, вызвав **DROP FUNCTION**. (Это действие не показано в примере.)

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Дополнительные примеры допустимых функций фильтра
* В следующем примере два простых условия объединены с помощью логического оператора AND.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
    WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
  GO

  ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```
* В следующем примере используются несколько условий и детерминированное преобразование с помощью оператора CONVERT.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
      WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
  GO
  ```
* В следующем примере используются математические операторы и функции.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < SQRT(400) + 10
  GO
  ```
* В следующем примере используются операторы BETWEEN и NOT BETWEEN. Их использование допустимо, так как полученная функция соответствует описанным здесь правилам после замены операторов BETWEEN и NOT BETWEEN соответствующими выражениями AND и OR.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 BETWEEN 0 AND 100
              AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
  GO
  ```
  Предыдущая функция эквивалентна следующей функции после замены операторов BETWEEN и NOT BETWEEN соответствующими выражениями AND и OR.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
  GO
  ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Примеры функций фильтра, которые не являются допустимыми
* Следующая функция недопустимая, так как она содержит недетерминированное преобразование.

  ```tsql
  CREATE FUNCTION dbo.fn_example5(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < CONVERT(datetime, '1/1/2016')
  GO
  ```
* Следующая функция недопустимая, так как она содержит вызов недетерминированной функции.

  ```tsql
  CREATE FUNCTION dbo.fn_example6(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < DATEADD(day, -60, GETDATE())
  GO
  ```
* Следующая функция является недопустимой, поскольку она содержит вложенный запрос.

  ```tsql
  CREATE FUNCTION dbo.fn_example7(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
  GO
  ```
* Следующие функции недопустимые из-за выражений, использующих алгебраические операторы или встроенные функции, которые не вычисляются в константу при определении функции. В алгебраические выражения или вызовы функций нельзя включать ссылки на столбцы.

  ```tsql
  CREATE FUNCTION dbo.fn_example8(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 % 2 =  0
  GO

  CREATE FUNCTION dbo.fn_example9(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE SQRT(@column1) = 30
  GO
  ```
* Следующая функция недопустимая, поскольку после замены оператора BETWEEN эквивалентным выражением AND она нарушает описанные здесь правила.

  ```tsql
  CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
  GO
  ```
  Предыдущая функция эквивалентна следующей функции после замены оператора BETWEEN соответствующим выражением AND. Эта функция является недопустимой, так как в простых условиях можно использовать только логический оператор OR.

  ```tsql
  CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
  GO
  ```

## <a name="how-stretch-database-applies-the-filter-function"></a>Как база данных Stretch применяет функцию фильтра
База данных Stretch применяет функцию фильтра к таблице и определяет пригодные для переноса строки с помощью оператора CROSS APPLY. Например:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Если функция возвращает непустой результат для строки, то строка пригодна для миграции.

## <a name="a-namereplacepredicateareplace-an-existing-filter-function"></a><a name="replacePredicate"></a>Замена существующей функции фильтра
Ранее заданную функцию фильтра можно заменить, снова выполнив инструкцию **ALTER TABLE** и указав новое значение для параметра **FILTER\_PREDICATE**. Например:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
Новая встроенная функция с табличным значением имеет следующие требования.

* Новая функция должна иметь меньшее количество ограничений по сравнению с предыдущей функцией.
* Все операторы, которые существовали в старой функции, должны существовать в новой функции.
* Новая функция не может содержать операторы, которых нет в старой функции.
* Порядок аргументов оператора изменять нельзя.
* Чтобы уменьшить строгость функции, можно изменять только константные значения, которые являются частью сравнения `<, <=, >, >=`.

### <a name="example-of-a-valid-replacement"></a>Пример допустимой замены
Предположим, что следующая функция представляет собой текущий предикат фильтра.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
Следующая функция является допустимой заменой, так как новая константа даты (указывающая более позднюю дату прекращения) делает функцию менее строгой.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Примеры замен, которые не являются допустимыми
Следующая функция не является допустимой заменой, так как новая константа даты (указывающая более раннюю дату прекращения) не делает функцию менее строгой.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
Следующая функция не является допустимой заменой, поскольку один из операторов сравнения был удален.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
Следующая функция не является допустимой заменой, поскольку новое условие было добавлено с логическим оператором AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Удаление функции фильтра из таблицы
Чтобы перенести таблицу целиком, а не только выбранные строки, удалите имеющуюся функцию фильтра, присвоив **FILTER\_PREDICATE** значение NULL. Например:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
После удаления функции фильтра все строки в таблице пригодны для переноса. В результате, если вы сначала не вернете все удаленные данные для таблицы из Azure, то не сможете позднее указать функцию фильтра для той же таблицы. Это ограничение позволяет избежать ситуации, в которой при предоставлении новой функции фильтра неподходящие для переноса строки уже были бы перенесены в Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Проверка функции фильтра, примененной к таблице
Чтобы проверить функцию фильтра, которая применена к таблице, откройте представление каталога **sys.remote\_data\_archive\_tables** и проверьте значение столбца **filter\_predicate**. Если значение равно null, то может архивироваться вся таблица. Дополнительные сведения см. в статье [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Заметки о безопасности для функций фильтра
Скомпрометированная учетная запись с привилегиями db_owner может выполнять следующие действия.  

* Создание и применение функции с табличным значением, которая потребляет большой объем ресурсов сервера или ожидает в течение длительного времени, что приводит к отказу в обслуживании.  
* Создание и применение функции с табличным значением, позволяющей вывести содержимое таблицы, для которой пользователю был явно запрещен доступ для чтения.  

## <a name="see-also"></a>Дополнительные материалы
[Инструкция ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)



<!--HONumber=Jan17_HO4-->


