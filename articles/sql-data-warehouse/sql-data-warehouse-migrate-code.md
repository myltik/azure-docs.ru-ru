---
title: Перенос кода SQL в хранилище данных SQL | Документация Майкрософт
description: Советы по переносу кода SQL в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: b17e8e306c01bef4c58658b35f3a67d0e721633c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527459"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Перенос кода SQL в хранилище данных SQL
В этой статье поясняются изменения кода, которые, вероятно, потребуется внести при переносе кода в хранилище данных SQL из другой базы данных. Некоторые функции хранилища данных SQL могут значительно повысить производительность, так как они предназначены для работы в распределенной форме. Однако для обеспечения производительности и масштабирования некоторые функции недоступны.

## <a name="common-t-sql-limitations"></a>Общие ограничения T-SQL
В следующем списке перечислены наиболее распространенные функции, которые не поддерживаются хранилищем данных SQL. Предоставленные ссылки позволят вам ознакомиться со способами обхода для соответствующей неподдерживаемой функции:

* [соединение ANSI при обновлении][ANSI joins on updates];
* [соединение ANSI при удалении][ANSI joins on deletes];
* [инструкция merge][merge statement];
* соединение join между базами данных
* [курсоры][cursors];
* [INSERT..EXEC][INSERT..EXEC];
* предложение output
* встроенные функции, определяемые пользователем
* функции с несколькими инструкциями
* [обобщенные табличные выражения](#Common-table-expressions)
* [рекурсивные обобщенные табличные выражения (CTE)](#Recursive-common-table-expressions-(CTE)
* функции и процедуры среды CLR
* функция $partition
* переменные таблицы
* параметры табличных значений
* распределенные транзакции
* фиксация или откат
* транзакция save
* контекст выполнения EXECUTE AS
* [предложение group by с параметрами rollup, cube или grouping sets][group by clause with rollup / cube / grouping sets options];
* [уровни вложенности больше 8][nesting levels beyond 8];
* [обновление через представления][updating through views];
* [использование инструкции select для назначения переменных][use of select for variable assignment];
* [отсутствие типа данных MAX для динамических строк SQL][no MAX data type for dynamic SQL strings].

К счастью, большинство из этих ограничений можно обойти. Пояснения см. в соответствующих разделах по разработке, указанных выше.

## <a name="supported-cte-features"></a>Поддерживаемые функции CTE
Хранилище данных SQL частично поддерживает обобщенные табличные выражения (CTE).  В настоящее время поддерживаются следующие функции CTE:

* Выражение CTE можно указать в инструкции SELECT.
* Выражение CTE можно указать в инструкции CREATE VIEW.
* Выражение CTE можно указать в инструкции CREATE TABLE AS SELECT (CTAS).
* Выражение CTE можно указать в инструкции CREATE REMOTE TABLE AS SELECT (CRTAS).
* Выражение CTE можно указать в инструкции CREATE EXTERNAL TABLE AS SELECT (CETAS).
* Из выражения CTE можно сослаться на удаленную таблицу.
* Из выражения CTE можно сослаться на внешнюю таблицу.
* В CTE можно определить несколько запросов CTE.

## <a name="cte-limitations"></a>Ограничения СTE
В хранилище данных SQL обобщенные табличные выражения имеют следующие ограничения:

* За CTE должна следовать одиночная инструкция SELECT. Инструкции INSERT, UPDATE, DELETE и MERGE не поддерживаются.
* Обобщенные табличные выражения, которые включают ссылки на самих себя (рекурсивные обобщенные табличные выражения), не поддерживаются (см. раздел ниже).
* Указание более одного предложения WITH в выражении CTE не допускается. Например, если определение запроса CTE содержит вложенный запрос, этот вложенный запрос не может содержать предложение WITH, определяющее другое выражение CTE.
* Предложение ORDER BY не может использоваться в определении запроса CTE, если не указано предложение TOP.
* Если выражение CTE используется в инструкции, которая является частью пакета, то после выражения необходимо указать точку с запятой.
* При использовании инструкций, подготовленных с помощью sp_prepare, выражения CTE будут вести себя так же, как другие инструкции SELECT в PDW. Тем не менее, если CTE используются в инструкции CETAS, подготовленной процедурой sp_prepare, поведение может переопределяться SQL Server и другими инструкциями PDW из-за способа реализации привязки в sp_prepare. Если инструкция SELECT, ссылающаяся на CTE, использует несуществующий в CTE столбец, процедура sp_prepare будет выполнена без ошибок. Ошибка возникнет при выполнении sp_execute.

## <a name="recursive-ctes"></a>Рекурсивные CTE
Рекурсивные CTE в хранилище данных SQL не поддерживаются.  Перенос рекурсивных CTE может быть сложен, и оптимальным решением станет разделение этой процедуры на несколько этапов. Обычно используется цикл для заполнения временной таблицы по мере выполнения рекурсивных промежуточных запросов. После заполнения временной таблицы можно возвратить данные как единый результирующий набор. Похожий подход использовался для решения `GROUP BY WITH CUBE` в статье [Группировка по параметрам в хранилище данных SQL][group by clause with rollup / cube / grouping sets options].

## <a name="unsupported-system-functions"></a>Неподдерживаемые системные функции
Ряд системных функций также не поддерживаются. Ниже приводится список некоторых системных функций, которые могут быть полезны в хранилище данных.

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Некоторые из этих проблем можно обойти.

## <a name="rowcount-workaround"></a>Возможное решение для замены @@ROWCOUNT
Чтобы обойти отсутствие поддержки @@ROWCOUNT, создайте хранимую процедуру, которая будет извлекать значение счетчика последней строки из sys.dm_pdw_request_steps и затем выполнять `EXEC LastRowCount` после инструкции DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Дополнительная информация
Полный список всех поддерживаемых инструкций T-SQL приведен в [разделах о Transact-SQL][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
