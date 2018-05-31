---
title: Использование когнитивных возможностей U-SQL в Azure Data Lake Analytics | Документация Майкрософт
description: Использование аналитики когнитивных возможностей в U-SQL
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: cd06e1ae56efdfdcfcd4fec5b2c17ee843d9e9dd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311120"
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Руководство по началу работы с когнитивными возможностями U-SQL

## <a name="overview"></a>Обзор
Когнитивные возможности U-SQL позволяют разработчикам использовать аналитику в программах, которые работают с большими данными. 

Доступны следующие когнитивные возможности:
* обработка изображений: распознавание лиц;
* обработка изображений: распознавание эмоций;
* обработка изображений: распознавание объектов (добавление тегов);
* обработка изображений: распознавание текста (OCR);
* обработка текста: извлечение ключевых фраз;
* обработка текста: анализ тональности.

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>Как использовать когнитивные возможности в скрипте U-SQL

В целом этот процесс прост:

* Включите когнитивные функции для сценария U-SQL, вызвав инструкцию `REFERENCE ASSEMBLY`.
* Создайте выходной набор строк, вызвав операцию `PROCESS` для входного набора строк, используя определяемый пользователем оператор для когнитивных возможностей.

### <a name="detecting-objects-in-images"></a>Распознавание объектов на изображениях

В следующем примере показано, как использовать когнитивные возможности для распознавания объектов на изображениях.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
Дополнительные примеры см. на странице **Примеры использования когнитивных возможностей в U-SQL** в разделе **Дальнейшие действия**.

## <a name="next-steps"></a>Дополнительная информация
* [Примеры использования когнитивных возможностей в U-SQL](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Разработка сценариев U-SQL с помощью средств озера данных для Visual Studio.](data-lake-analytics-data-lake-tools-get-started.md)
* [Использование оконных функций U-SQL для заданий в службе аналитики озера данных Azure](data-lake-analytics-use-window-functions.md)
