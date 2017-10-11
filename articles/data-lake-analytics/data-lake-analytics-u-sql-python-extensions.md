---
title: "Расширение возможностей сценариев U-SQL c Python в Azure Data Lake Analytics | Документы Microsoft"
description: "Узнайте о том, как выполнять код Python в сценариях U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/20/2017
ms.author: saveenr
ms.openlocfilehash: d18ef1f747aee2fa01cef9891432d0461031ee4c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-get-started-with-extending-u-sql-with-python"></a>Руководство. Начало работы с расширением возможностей U-SQL c Python

Расширения Python для U-SQL позволяют разработчикам выполнять код Python с массовым параллелизмом. В следующих примерах представлены основные шаги:

* Используйте инструкцию `REFERENCE ASSEMBLY`, чтобы включить расширения Python для сценария U-SQL
* Использование операции `REDUCE` для разделения входных данных в разделе
* Расширения Python для U-SQL включают встроенный инструмент редукции (`Extension.Python.Reducer`), который выполняет код Python в каждой вершине, назначенный инструменту редукции.
* Сценарий U-SQL содержит встроенный код Python с функцией `usqlml_main`, которая принимает кадр данных Pandas в качестве входных данных и возвращает его в качестве выходных данных.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Интеграция Python c U-SQL

### <a name="datatypes"></a>Типы данных

* Строковые и числовые столбцы из U-SQL преобразовываются без изменений между Pandas и U-SQL.
* Пустые значения U-SQL преобразовываются в значения Pandas `NA` и из них.

### <a name="schemas"></a>Схемы

* Векторы индексов в Pandas не поддерживаются в U-SQL. Все входные кадры данных в функции Python будут всегда содержать 64-разрядный числовой индекс от 0 до соответствующего количества строк минус 1. 
* Наборы данных U-SQL не могут содержать повторяемые имена столбцов.
* Имена столбцов в наборах данных не являются строками. 

### <a name="python-versions"></a>Версии Python
Поддерживается только версия Python 3.5.1 (скомпилированная для Windows). 

### <a name="standard-python-modules"></a>Стандартные модули Python
Включены все стандартные модули Python.

### <a name="additional-python-modules"></a>Дополнительные модули Python
Кроме стандартных библиотек Python, включены несколько часто используемых библиотек Python:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Сообщения об исключении
В настоящее время исключение в коде Python отображается в качестве общего сбоя вершины. В будущем в сообщениях об ошибках заданий U-SQL будут отображаться сообщения об исключении Python.

### <a name="input-and-output-size-limitations"></a>Ограничения размера входных и выходных данных
Каждой вершине назначен ограниченный объем памяти. В настоящее время он составляет 6 ГБ на единицу аналитики. Так как входящие и исходящие кадры данных должны существовать в памяти кода Python, общий размер входных и выходных данных должен находиться в пределах 6 ГБ.

## <a name="see-also"></a>Дополнительные материалы
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Использование оконных функций U-SQL для заданий в службе аналитики озера данных Azure](data-lake-analytics-use-window-functions.md)

