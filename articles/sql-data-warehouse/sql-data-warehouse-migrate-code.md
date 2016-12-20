---
title: "Перенос кода SQL в хранилище данных SQL | Документация Майкрософт"
description: "Советы по переносу кода SQL в хранилище данных SQL Azure для разработки решений."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 19c252a3-0e41-4eec-9d3e-09a68c7e7add
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0ff5ad648d429da433170301205eafb850be5d81


---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Перенос кода SQL в хранилище данных SQL
При переносе кода в хранилище данных SQL из другой базы данных, скорее всего, необходимо будет вносить изменения в базу кода. Некоторые функции хранилища данных SQL могут значительно повысить производительность, так как они предназначены для работы в распределенной форме. Однако для обеспечения производительности и масштабирования некоторые функции недоступны.

## <a name="common-t-sql-limitations"></a>Общие ограничения T-SQL
В следующем списке перечислены основные функции, которые не поддерживаются в хранилище данных SQL Azure. Предоставленные ссылки позволят вам ознакомиться со способами обхода для этой неподдерживаемой функции:

* [соединение ANSI join при обновлении][соединение ANSI join при обновлении];
* [соединение ANSI join при удалении][соединение ANSI join при удалении];
* [инструкция merge][инструкция merge];
* соединение join между базами данных
* [курсоры][курсоры];
* [SELECT..INTO][SELECT..INTO];
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
* [предложение группирования group by с операторами rollup, cube или grouping][предложение группирования group by с операторами rollup, cube или grouping];
* [уровни вложения больше 8][уровни вложения больше 8];
* [обновление через представления][обновление через представления];
* [использование инструкции select для назначения переменных][использование инструкции select для назначения переменных];
* [отсутствует тип данных MAX для динамических строк SQL][отсутствует тип данных MAX для динамических строк SQL].

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
Рекурсивные CTE в хранилище данных SQL не поддерживаются.  Перенос рекурсивных CTE может быть частично завершен, и оптимальным решением станет разделение этой процедуры на несколько этапов. Обычно используется цикл для заполнения временной таблицы по мере выполнения рекурсивных промежуточных запросов. После заполнения временной таблицы можно возвратить данные как единый результирующий набор. Похожий подход использовался для решения `GROUP BY WITH CUBE` в статье [предложение группирования group by с операторами rollup, cube или grouping][предложение группирования group by с операторами rollup, cube или grouping].

## <a name="unsupported-system-functions"></a>Неподдерживаемые системные функции
Ряд системных функций также не поддерживаются. Ниже приводится список некоторых системных функций, которые могут быть полезны в хранилище данных.

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Некоторые из этих проблем можно обойти.

## <a name="rowcount-workaround"></a>Обходной путь для @@ROWCOUNT
Чтобы обойти отсутствие поддержки @@ROWCOUNT,, создайте хранимую процедуру, которая будет извлекать значение счетчика последней строки из sys.dm_pdw_request_steps и затем выполнять `EXEC LastRowCount` после инструкции DML.

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

## <a name="next-steps"></a>Дальнейшие действия
Полный список всех поддерживаемых инструкций T-SQL приведен в [темах, связанных с языком Transact-SQL][разделах о Transact-SQL].

<!--Image references-->

<!--Article references-->
[соединение ANSI join при обновлении]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[соединение ANSI join при удалении]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[инструкция merge]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[разделах о Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[курсоры]: ./sql-data-warehouse-develop-loops.md
[SELECT..INTO]: ./sql-data-warehouse-develop-ctas.md#selectinto
[предложение группирования group by с операторами rollup, cube или grouping]: ./sql-data-warehouse-develop-group-by-options.md
[уровни вложения больше 8]: ./sql-data-warehouse-develop-transactions.md
[обновление через представления]: ./sql-data-warehouse-develop-views.md
[использование инструкции select для назначения переменных]: ./sql-data-warehouse-develop-variable-assignment.md
[отсутствует тип данных MAX для динамических строк SQL]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


