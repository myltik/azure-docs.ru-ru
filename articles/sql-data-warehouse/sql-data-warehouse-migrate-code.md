<properties
   pageTitle="Перенос кода SQL в хранилище данных SQL | Microsoft Azure"
   description="Советы по переносу кода SQL в хранилище данных SQL Azure для разработки решений."
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
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Перенос кода SQL в хранилище данных SQL

Чтобы обеспечить совместимость кода с хранилищем данных SQL, скорее всего, необходимо будет вносить изменения в базу кода. Некоторые функции хранилища данных SQL могут значительно повысить производительность, так как они предназначены для работы непосредственно в распределенной форме. Однако для обеспечения производительности и масштабирования некоторые функции недоступны.

## Изменение кода Transact-SQL

В следующем списке перечислены основные функции, которые не поддерживаются в хранилище данных SQL Azure:

- соединение ANSI join при обновлении
- соединение ANSI join при удалении
- инструкция merge
- соединение join между базами данных
- [инструкции pivot и unpivot][]
- [курсоры][]
- [SELECT..INTO][]
- INSERT..EXEC
- предложение output
- встроенные функции, определяемые пользователем
- функции с несколькими инструкциями
- рекурсивные обобщенные табличные выражения
- обновления с помощью обобщенного табличного выражения
- функции и процедуры среды CLR
- функция $partition
- переменные таблицы
- параметры табличных значений
- распределенные транзакции
- фиксация или откат
- транзакция save
- контекст выполнения EXECUTE AS
- [предложение группирования group by с операторами rollup, cube или grouping][]
- [уровни вложения больше 8][]
- [обновление через представления][]
- [использование инструкции select для назначения переменных][]
- [отсутствует тип данных MAX для динамических строк SQL][]

К счастью, большинство из этих ограничений можно обойти. Пояснения см. в соответствующих разделах по разработке, указанных выше.

Ряд системных функций также не поддерживаются. Ниже приводится список некоторых системных функций, которые могут быть полезны в хранилище данных.

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Опять же, многие из этих ограничений можно обойти.

Например, приведенный ниже код является альтернативным решением для выборки с помощью инструкции @@ROWCOUNT.

```
SELECT  SUM(row_count) AS row_count 
FROM    sys.dm_pdw_sql_requests 
WHERE   row_count <> -1 
AND     request_id IN 
                    (   SELECT TOP 1    request_id 
                        FROM            sys.dm_pdw_exec_requests 
                        WHERE           session_id = SESSION_ID() 
                        ORDER BY end_time DESC
                    )
;
``` 

## Дальнейшие действия
Рекомендации по разработке кода см. в разделе [Общие сведения о разработке][].

<!--Image references-->

<!--Article references-->
[инструкции pivot и unpivot]: sql-data-warehouse-develop-pivot-unpivot.md
[курсоры]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[предложение группирования group by с операторами rollup, cube или grouping]: sql-data-warehouse-develop-group-by-options.md
[уровни вложения больше 8]: sql-data-warehouse-develop-transactions.md
[обновление через представления]: sql-data-warehouse-develop-views.md
[использование инструкции select для назначения переменных]: sql-data-warehouse-develop-variable-assignment.md
[отсутствует тип данных MAX для динамических строк SQL]: sql-data-warehouse-develop-dynamic-sql.md
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->