<properties
	pageTitle="Выбор строк для переноса с помощью предиката фильтра (база данных Stretch) | Microsoft Azure"
	description="Узнайте, как использовать предикат фильтра для выбора строк для переноса."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Выбор строк для переноса с помощью предиката фильтра (база данных Stretch)

Если исторические данные хранятся в отдельной таблице, можно настроить растяжение базы данных для миграции всей таблицы. С другой стороны, если таблица содержит как исторические, так и текущие данные, можно указать предикат фильтра для выбора строк для миграции. Предикат фильтра должен вызывать встроенную функцию табличных значений. Из этого раздела вы узнаете, как написать встроенную функцию табличных значений для выбора строк для переноса.

В CTP-версии от 3.1 до RC1 включительно возможность указания предиката в мастере включения Stretch для базы данных отсутствует. В этом случае необходимо использовать инструкцию ALTER TABLE для настройки растяжения базы данных. Подробнее см. в разделе [Инструкция ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

Если не указать предикат фильтра, будет выполнена миграция всей таблицы.

    > If you provide a filter predicate that performs poorly, data migration also performs poorly. Stretch Database applies the filter predicate to the table by using the CROSS APPLY operator.

## Основные требования к встроенной функции табличных значений
Встроенная функция табличных значений, необходимая для функции фильтра растяжения базы данных, выглядит следующим образом.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE <predicate>
```
Параметрами этой функции должны быть идентификаторы столбцов таблицы.

Привязка к схеме необходима для того, чтобы столбцы, используемые в предикате фильтра, не были удалены или изменены.

### Возвращаемое значение
Если функция возвращает непустой результат, то строка может быть перенесена; в противном случае, то есть если функция не возвращает ни одной строки, строка не подходит для переноса.

### Условия
&lt;*предикат*&gt; может содержать одно или несколько условий, объединенных с помощью логического оператора AND.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Каждое условие, в свою очередь, может содержать одно или несколько простых условий, объединенных с помощью логического оператора OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### Простые условия
Простое условие может выполнять одно из следующих сравнений.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Сравнение параметра функции с константным выражением. Пример: `@column1 < 1000`.

    Ниже приведен пример, который проверяет, что значение столбца *date* меньше 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Применение оператора IS NULL или IS NOT NULL к параметру функции.

-   Использование оператора IN для сравнения параметра функции со списком постоянных значений.

    Ниже приведен пример, который проверяет, что значение столбца *shipment\_status* равно `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

### Операторы сравнения
Поддерживаются следующие операторы сравнения.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### Константные выражения
Константа, используемая в качестве предиката фильтра, может быть любым детерминированным выражением, которое можно вычислить при определении функции. Константные выражения могут содержать следующее.

-   Литералы. Пример: `N’abc’, 123`.

-   Алгебраические выражения. Пример: `123 + 456`.

-   Детерминированные функции. Пример: `SQRT(900)`.

-   Детерминированные преобразования, использующие CAST или CONVERT. Пример: `CONVERT(datetime, '1/1/2016', 101)`.

### Другие выражения
Можно использовать операторы BETWEEN и NOT BETWEEN, если полученный предикат соответствует описанным здесь правилам после замены операторов BETWEEN и NOT BETWEEN соответствующими выражениями AND и OR.

Нельзя использовать вложенные запросы или недетерминированные функции, такие как RAND() или GETDATE().

## Примеры допустимых функций

-   В следующем примере два простых условия объединены с помощью логического оператора AND.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   В следующем примере используются несколько условий и детерминированное преобразование с помощью оператора CONVERT.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   В следующем примере используются математические операторы и функции.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   В следующем примере используются операторы BETWEEN и NOT BETWEEN. Их использование допустимо, так как полученный предикат соответствует описанным здесь правилам после замены операторов BETWEEN и NOT BETWEEN соответствующими выражениями AND и OR.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
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
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## Примеры функций, которые не являются допустимыми

-   Следующая функция является недопустимой, поскольку она содержит недетерминированное преобразование.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   Следующая функция является недопустимой, поскольку она содержит вызов недетерминированной функции.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   Следующая функция является недопустимой, поскольку она содержит вложенный запрос.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   Следующие функции не являются допустимыми из-за выражений, использующих алгебраические операторы или встроенные функции, которые не вычисляются в константу при определении функции. В алгебраические выражения или вызовы функций нельзя включать ссылки на столбцы.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE SQRT(@column1) = 30
    GO
    ```

-   Следующая функция является недопустимой, поскольку после замены оператора BETWEEN эквивалентным выражением AND она нарушает описанные здесь правила.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    Предыдущая функция эквивалентна следующей функции после замены оператора BETWEEN соответствующим выражением AND. Эта функция является недопустимой, так как в простых условиях можно использовать только логический оператор OR.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## Как предикат фильтра применяется при растяжении базы данных
При растяжении базы данных предикат фильтра применяется к таблице и определяет пригодные для миграции строки с помощью оператора CROSS APPLY. Например:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Если функция возвращает непустой результат для строки, то строка пригодна для миграции.

## Добавление предиката фильтра к таблице
Добавьте предикат фильтра к таблице, выполнив инструкцию ALTER TABLE и указав существующую встроенную функцию табличных значений в качестве значения параметра FILTER\_PREDICATE. Например:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
) )
```
Привязка функции к таблице в качестве предиката позволяет добиться следующего.

-   При следующем переносе данных будут перенесены только те строки, для которых функция возвращает непустое значение.

-   Столбцы, используемые функцией, привязаны к схеме. Эти столбцы невозможно изменить, пока таблица использует функцию в качестве предиката фильтра.

## Удаление предиката фильтра из таблицы
Чтобы перенести таблицу целиком, а не только выбранные строки, удалите существующий предикат фильтра FILTER\_PREDICATE, присвоив ему значение null. Например:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
После удаления предиката фильтра все строки в таблице пригодны для миграции.

## Замена существующего предиката фильтра
Ранее заданный предикат фильтра можно заменить, снова выполнив инструкцию ALTER TABLE и указав новое значение для параметра FILTER\_PREDICATE. Например:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
```
Новые встроенная функция табличных значений имеет следующие требования.

-   Новая функция должна иметь меньшее количество ограничений по сравнению с предыдущей функцией.

-   Все операторы, которые существовали в старой функции, должны существовать в новой функции.

-   Новая функция не может содержать операторы, которых нет в старой функции.

-   Порядок аргументов оператора изменять нельзя.

-   Для уменьшения строгости предиката можно изменять только константные значения, которые являются частью сравнения `<, <=, >, >=`.

### Пример допустимой замены
Предположим, что следующая функция представляет собой текущий предикат фильтра.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
Следующая функция является допустимой заменой, поскольку константа новой даты (указывающая более позднюю дату прекращения) делает предикат менее строгим.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
			AND (@column2 < -50 OR @column2 > 50)
GO
```

### Примеры замен, которые не являются допустимыми
Следующая функция не является допустимой заменой, поскольку константа новой даты (указывающая более раннюю дату прекращения) не делает предикат менее строгим.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
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
RETURN	SELECT 1 AS is_eligible
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
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
			AND (@column2 <> 0)
GO
```

## Удаление предиката фильтра
Удалить встроенную функцию табличных значений нельзя, пока таблица использует эту функцию в качестве предиката фильтра.

## Проверка применения предиката фильтра к таблице
Чтобы проверить предикат фильтра, который применяется к таблице, откройте представление каталога **sys.remote\_data\_archive\_tables** и проверьте значение столбца **filter\_predicate**. Если значение равно null, то может архивироваться вся таблица. Дополнительные сведения см. в разделе [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## См. также

[Инструкция ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0323_2016-->