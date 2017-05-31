---
title: "Объединение наборов строк с помощью соединений U-SQL в Azure Data Lake Analytics | Документация Майкрософт"
description: "Узнайте, как в U-SQL операторы соединения используются для объединения наборов строк в Azure Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5ffc7116812e9f18f972eac2daff7935b00891f1
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="combine-rowsets-with-u-sql-joins"></a>Объединение наборов строк с помощью соединений U-SQL

U-SQL предоставляет общие операторы объединения, такие как INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN, SEMI JOIN, для объединения не только таблиц, но и любых наборов строк (даже тех, которые создаются из файлов).

Следующий скрипт соединяет ``@searchlog`` с журналом впечатлений объявления, предоставляя объявления для строки запроса для конкретной даты.

```
@adlog =
    EXTRACT UserId int,
            Ad string,
            Clicked int
    FROM "/Samples/Data/AdsLog.tsv"
    USING Extractors.Tsv();

@join =
    SELECT a.Ad, s.Query, s.Start AS Date
    FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                    ON a.UserId == s.UserId
    WHERE a.Clicked == 1;

OUTPUT @join   
    TO "/output/Searchlog-join.csv"
    USING Outputters.Csv();
```

U-SQL поддерживает только совместимый с ANSI синтаксис объединения: предикат Rowset1 JOIN Rowset2 ON. Старый синтаксис предиката FROM Rowset1, Rowset2 WHERE _не_ поддерживается.
Предикат в JOIN должен являться объединением равенства, а не выражением. Чтобы использовать выражение, добавьте его в предыдущее предложение SELECT набора строк. Если требуется выполнить различные сравнения, его можно переместить в предложение WHERE.

### <a name="next-steps"></a>Дальнейшие действия
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Использование оконных функций U-SQL для заданий в службе аналитики озера данных Azure](data-lake-analytics-use-window-functions.md)


