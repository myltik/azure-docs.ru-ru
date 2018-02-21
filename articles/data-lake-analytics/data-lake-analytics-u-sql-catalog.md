---
title: "Начало работы с каталогом U-SQL | Документация Майкрософт"
description: "Сведения об использовании каталога U-SQL для совместного использования кода и данных."
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
ms.openlocfilehash: 08364c6c7bea53807844e3b1cc327dc3742e0487
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-u-sql-catalog"></a>Начало работы с каталогом U-SQL

## <a name="create-a-tvf"></a>Создание функции TVF

В предыдущем скрипте U-SQL мы повторно использовали EXTRACT для чтения из одного и того же исходного файла. В U-SQL функция с табличным значением (TVF) позволяет инкапсулировать данные для повторного использования в будущем.  

Следующий скрипт создает возвращающую табличное значение функцию с именем `Searchlog()` в базе данных и схеме по умолчанию:

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

Следующий скрипт демонстрирует, как использовать функцию TVF, определенную в предыдущем скрипте:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SerachLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Создание представлений

Если у вас есть одно выражение запроса вместо возвращающей табличное значение функции, можно использовать представление U-SQL для инкапсуляции этого выражения.

Следующий скрипт создает представление с именем `SearchlogView` в базе данных и схеме по умолчанию:

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

Следующий скрипт демонстрирует, как использовать определенное представление.

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>создание таблиц.
Как и в случае с таблицами реляционной базы данных, U-SQL позволяет создать таблицу с предварительно определенной схемой или же создать таблицу и определить схему из запроса, который заполняет таблицу (CREATE TABLE AS SELECT или CTAS).

Следующий скрипт создает базу данных и две таблицы:

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Запросы к таблицам
Вы можете отправлять запросы к таблицам (созданным с помощью предыдущего скрипта) так же, как вы отправляете запросы к файлам данных. Чтобы не создавать набор строк с помощью EXTRACT, теперь можно просто ссылаться на имя таблицы.

Для чтения из таблиц нужно изменить скрипт преобразования, который использовался ранее:

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Сейчас выполнять SELECT для таблицы в том же скрипте, где создается таблица, нельзя.

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Разработка сценариев U-SQL с помощью средств озера данных для Visual Studio.](data-lake-analytics-data-lake-tools-get-started.md)
* [Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
