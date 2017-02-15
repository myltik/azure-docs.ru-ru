---
title: "Использование меток для инструментирования запросов в хранилище данных SQL | Документация Майкрософт"
description: "Рекомендации по использованию меток для инструментирования запросов в хранилище данных SQL Azure для разработки решений."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5c72cd2c80d9fcee3d9340c23a629451c54c9156


---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Использование меток для инструментирования запросов в хранилище данных SQL
Хранилище данных SQL поддерживает концепцию так называемых меток. Прежде чем углубляться в это понятие, рассмотрим следующий пример.

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Последняя строка помечает запрос словами "Метка моего запроса". Это удобно, поскольку метки можно использовать в запросах в динамических административных представлениях. С помощью меток можно отслеживать проблемные запросы и устанавливать ход выполнения процессов ETL.

В данном случае хорошо помогает продуманный способ именования. Например, имена вида "ПРОЕКТ : ПРОЦЕДУРА : ОПЕРАТОР : КОММЕНТАРИЙ" позволяют уникальным образом идентифицировать запрос в масштабах всего кода исходного элемента управления.

Для поиска по метке можно использовать следующий запрос с применением динамических административных представлений:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Слово Label в запросе необходимо заключать в квадратные скобки или двойные кавычки. Это слово является зарезервированным и в отсутствие разделителей вызывает ошибку.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные советы по разработке см. в статье [общие сведения о разработке][общие сведения о разработке].

<!--Image references-->

<!--Article references-->
[общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


