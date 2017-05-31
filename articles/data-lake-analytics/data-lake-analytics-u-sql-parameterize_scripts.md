---
title: "Параметризация скриптов U-SQL в Azure Data Lake Analytics | Документация Майкрософт"
description: "Сведения о том, как параметризовать скрипты U-SQL в Azure Data Lake Analytics."
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
ms.date: 05/10/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6318fa8b14d8bb7d650522e2d96a5b1417afe3df
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="parameterizing-u-sql-scripts"></a>Параметризация скриптов U-SQL

При работе со скриптами U-SQL можно воспользоваться параметризацией. При использовании параметров основной текст скрипта остается неизменным. При этом они управляют выполнением скрипта, передавая отдельные значения. В типичном сценарии скрипт обрабатывает данные за определенный промежуток времени, а разработчику может потребоваться параметризовать скрипт на дату начала и дату окончания. 

Существует два способа параметризации скриптов:
* Скрипт разрешает безопасное добавление параметров в верхней части. U-SQL поддерживает этот метод.
* API отправки задания позволяет вызывающим объектам отправлять параметры. U-SQL не поддерживает этот метод.

## <a name="example-scripts"></a>Примеры скриптов

Ниже приведен простой исходный скрипт.

```
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

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    
   
В этом сценарии мы параметризуем ``Region``, добавив инструкцию `DECLARE EXTERNAL`.

```
DECLARE EXTERNAL @TargetRegion = "en-us";

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

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    

По умолчанию этот скрипт выполняет фильтрацию, отображая строки, относящиеся только к региону `en-us`. Мы уже определили параметр в скрипте. Ключевое слово `EXTERNAL` указывает, что пользователь может переопределить значение `@TargetRegion`, добавив инструкцию DECLARE, определяющую параметр `@TargetRegion`.

В следующем сценарии показано, как выглядит параметризованный скрипт, когда для параметра предоставлено значение. Теперь этот скрипт выполняет фильтрацию до строк, содержащих регион `en-gb`.

```
DECLARE @TargetRegion = "en-gb";

DECLARE EXTERNAL @TargetRegion = "en-us";

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

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    


## <a name="next-steps"></a>Дальнейшие действия
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

