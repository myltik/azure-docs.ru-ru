---
title: 'Навык когнитивного поиска: разделение текста (Поиск Azure) | Документация Майкрософт'
description: Разделение текста на блоки или страницы текста в зависимости от длины в конвейере обогащения службы "Поиск Azure".
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: dbb9261cfce0a8437cfe76121fa16aa87c4b3393
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786803"
---
#   <a name="text-split-cognitive-skill"></a>Когнитивный навык разделения текста

Навык **Разделение текста** разделяет текст на блоки текста. Можете указать, хотите ли вы разбить текст на предложения или на страницы определенной длины. Этот навык особенно полезен при наличии требований к максимальной длине текста в последующих навыках. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра     | ОПИСАНИЕ |
|--------------------|-------------|
| textSplitMode      | Страницы или приложения | 
| maximumPageLength | Если textSplitMode имеет значение "pages", это относится к максимальной длине страницы, определяемой `String.Length`. Минимальное значение — 100. | 
| defaultLanguageCode   | (Дополнительно.) Один из следующих кодов языка: `da, de, en, es, fi, fr, it, ko, pt`. По умолчанию — английский (en). Необходимо учитывать следующее:<ul><li>При передаче формата languagecode-countrycode используется только часть languagecode формата.</li><li>Если язык отсутствует в предыдущем списке, навык разделения разбивает текст на границах символов.</li><li>Предоставление кода языка помогает избежать разделения слова на несколько частей в языках без разделителей, таких как китайский, японский или корейский.</li></ul>  |


## <a name="skill-inputs"></a>Входные данные навыков

| Имя параметра       | ОПИСАНИЕ      |
|----------------------|------------------|
| текст  | Текст, который будет разбит на подстроки. |
| languageCode  | (Необязательно.) Код языка документа.  |

## <a name="skill-outputs"></a>Выходные данные навыка 

| Имя параметра     | ОПИСАНИЕ |
|--------------------|-------------|
| textItems | Массив извлеченных подстрок. |


##  <a name="sample-definition"></a>Пример определения

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Пример ввода

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Пример выходных данных

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Варианты ошибок
Если язык не поддерживается, создается предупреждение и текст разделяется на границах символов.

## <a name="see-also"></a>См. также

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
