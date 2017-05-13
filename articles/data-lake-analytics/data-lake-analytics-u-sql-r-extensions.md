---
title: "Расширение возможностей скриптов U-SQL c R в Azure Data Lake Analytics | Документация Майкрософт"
description: "Узнайте о том, как выполнять код R в скриптах U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: saveenr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: ea837a735404d11b087cd552d1fce64184e88e00
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---

# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>Руководство. Начало работы с расширением возможностей U-SQL c R

В следующих примерах представлены основные шаги по развертыванию кода R:
* Включение расширений R для скрипта U-SQL с помощью инструкции REFERENCE ASSEMBLY.
* Разделение входных данных для ключа с помощью операции REDUCE.
* Расширения R для U-SQL включают встроенное средство редукции (Extension.R.Reducer), которое выполняет код R в каждой назначенной ему вершине. 
* Передача данных между U-SQL и R с использованием выделенных именованных таблиц данных inputFromUSQL и outputToUSQL соответственно. Имена идентификаторов входной и выходной таблицы данных исправлены (т. е. эти предопределенные имена идентификаторов входной и выходной таблицы данных невозможно изменить).


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

## <a name="how-r-integrates-with-u-sql"></a>Интеграция R c U-SQL

### <a name="datatypes"></a>Типы данных
* Строковые и числовые столбцы из U-SQL преобразовываются без изменений между таблицей данных на R и U-SQL [поддерживаемые типы: double, string, bool, integer, byte].
* Тип данных коэффициента не поддерживается в U-SQL.
* byte[] должен быть сериализован в виде строки с кодировкой Base64.
* После создания в U-SQL входной таблицы данных R или задания для параметра средства редукции stringsAsFactors значения true строки U-SQL можно преобразовать в коэффициенты кода R.

### <a name="schemas"></a>Схемы
* Наборы данных U-SQL не могут содержать повторяемые имена столбцов.
* Имена столбцов в наборах данных U-SQL должны быть строками.
* Имена столбцов в скриптах U-SQL и R должны совпадать.
* Столбцы с доступом только для чтения не могут быть частью выходной таблицы данных, так как они автоматически вставляются обратно в таблицу U-SQL (если она является частью выходной схемы определяемых пользователем объектов).

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Использование оконных функций U-SQL для заданий в службе аналитики озера данных Azure](data-lake-analytics-use-window-functions.md)

