<properties
   pageTitle="Циклы в хранилище данных SQL | Microsoft Azure"
   description="Рекомендации по применению циклов Transact-SQL и замене курсоров в хранилище данных SQL Azure для разработки решений."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Циклы в хранилище данных SQL
Хранилище данных SQL позволяет использовать цикл [WHILE][] для повторяющихся блоков операторов. Цикл продолжается, пока не будут выполнены указанные условия или пока код не прервет цикл с помощью ключевого слова `BREAK`. Циклы особенно полезны для замены курсоров, определенных в коде SQL. К счастью почти все курсоры, записанные в коде SQL, относятся к разряду перемотки и доступности только для чтения. Циклы [WHILE] станут отличной альтернативой, если вам нужно заменить какой-то курсор.

## Применение циклов и замена курсоров в хранилище данных SQL
Прежде чем обращаться к циклам, задайте себе следующий вопрос: "Можно ли переписать этот курсор, задействовав операции, ориентированные на работу с наборами данных?" Во многих случаях можно ответить утвердительно, и часто это будет лучшим вариантом. Операция, ориентированная на работу с набором данных, нередко выполняется намного быстрее, чем итеративный метод построчного перебора.

Курсоры быстрой перемотки, доступные только для чтения, легко заменяются циклической конструкцией. Ниже приведен простой пример. Приведенный в примере код обновляет статистику для каждой таблицы в базе данных. Перебор таблиц в цикле позволяет выполнить каждую команду в последовательности.

Для начала создайте временную таблицу с уникальным номером строки, обозначающим отдельные операторы:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Затем инициализируйте переменные, необходимые для выполнения цикла:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Теперь запустите цикл последовательного выполнения операторов:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

И, наконец, удалите временную таблицу, созданной на первом этапе:

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->

<!--Article references-->
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0629_2016-->