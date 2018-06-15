---
title: Использование циклов T-SQL в хранилище данных SQL Azure | Документация Майкрософт
description: Рекомендации по применению циклов Transact-SQL и замене курсоров в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8d51c8f18d7c00d21fcc057efcda73e2a6b46cc7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598971"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Использование циклов T-SQL в хранилище данных SQL
Рекомендации по применению циклов Transact-SQL и замене курсоров в хранилище данных SQL Azure для разработки решений.

## <a name="purpose-of-while-loops"></a>Назначение циклов WHILE

Хранилище данных SQL позволяет использовать цикл [WHILE](/sql/t-sql/language-elements/while-transact-sql) для повторяющихся блоков операторов. Цикл WHILE продолжается, пока не будут выполнены указанные условия или пока код не прервет цикл с помощью ключевого слова BREAK. Циклы полезны для замены курсоров, определенных в коде SQL. К счастью, почти все курсоры, записанные в коде SQL, относятся к разряду перемотки и доступности только для чтения. Таким образом, циклы [WHILE] — отличная альтернатива для замены курсоров.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Замена курсоров в хранилище данных SQL
Прежде чем обращаться к циклам, задайте себе следующий вопрос: "Можно ли переписать этот курсор, задействовав операции, ориентированные на работу с наборами данных?" Во многих случаях можно ответить утвердительно, и часто это будет лучшим вариантом. Операция, ориентированная на работу с набором данных, нередко выполняется быстрее, чем итеративный метод построчного перебора.

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

## <a name="next-steps"></a>Дополнительная информация
Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).

