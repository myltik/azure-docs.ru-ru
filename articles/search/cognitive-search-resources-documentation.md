---
title: Ресурсы документации по когнитивному поиску (Поиск Azure) | Документация Майкрософт
description: Аннотированный перечень статей, руководств, примеров и записей блога, связанных с рабочими нагрузками когнитивного поиска в службе "Поиск Azure".
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: cee479b81cb5d8b6cd3306e3735d72c9e64e6045
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640359"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Ресурсы документации для рабочих нагрузок когнитивного поиска

Когнитивный поиск теперь находится в общедоступной предварительной версии. Это новый информационный слой индексирования в службе "Поиск Azure", который находит скрытые сведения в нетекстовых источниках и неразделенном тексте, преобразуя их в текстовое содержимое с полными возможностями поиска в службе "Поиск Azure".

Далее представлена полная документация со статьями по когнитивному поиску.

## <a name="getting-started"></a>Приступая к работе
+ [What is cognitive search?](cognitive-search-concept-intro.md) (Что такое когнитивный поиск?)
+ [Краткое руководство по созданию конвейера когнитивного поиска с использованием навыков и примера данных](cognitive-search-quickstart-blob.md)
+ [Руководство по вызову API-интерфейсов когнитивного поиска (предварительная версия)](cognitive-search-tutorial-blob.md)
+ [Пример создания пользовательского навыка с помощью API перевода текста](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Практические руководства
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [How to reference annotations in a skillset](cognitive-search-concept-annotations-syntax.md) (Создание ссылок на аннотации в наборе навыков когнитивного поиска)
+ [Сопоставление обогащенных полей с индексом, поддерживающим поиск](cognitive-search-output-field-mapping.md)
+ [How to process and extract information from images in cognitive search scenarios](cognitive-search-concept-image-scenarios.md) (Обработка и извлечение информации из изображений в сценариях когнитивного поиска)
+ [How to rebuild an Azure Search index](search-howto-reindex.md) (Как перестроить индекс Поиска Azure)
+ [How to add a custom skill to a cognitive search pipeline](cognitive-search-custom-skill-interface.md) (Добавление пользовательского навыка в конвейер когнитивного поиска)
+ [Советы по устранению неполадок](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Справочные материалы

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [Предварительная версия REST API](search-api-2017-11-11-preview.md)
  + [Создание набора квалификационных навыков (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Создание индексатора (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>См. также

+ [REST API службы "Поиск Azure"](https://docs.microsoft.com/rest/api/searchservice/)
+ [Индексаторы в службе поиска Azure](search-indexer-overview.md)
+ [Что такое поиск Azure?](search-what-is-azure-search.md)