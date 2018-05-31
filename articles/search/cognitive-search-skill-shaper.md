---
title: Навык когнитивного поиска формирователя ("Поиск Azure") | Документы Майкрософт
description: Извлечение метаданных и структурированных сведений из неструктурированных данных и их формирование в виде сложного типа в конвейере обогащения службы "Поиск Azure".
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 311f4bd67081de567763783a9d86540eda36d9f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786773"
---
#   <a name="shaper-cognitive-skill"></a>Когнитивный навык формирователя

Навык **формирователя** создает сложный тип для поддержки составных полей (также называемых многокомпонентными). Поле сложного типа состоит из нескольких частей, но обрабатывается как один элемент в индексе Поиска Azure. К примерам объединенных полей, которые удобно использовать в сценариях поиска, относится объединение в одном поле имени и фамилии, города и области, имени и даты рождения, призванное создать уникальный идентификатор.

Навык формирователя фактически позволяет создать структуру, определить имя ее членов и присвоить значения каждому из них.

По умолчанию этот метод поддерживает объекты с глубиной в один уровень. Для более сложных объектов можно соединить в цепочку несколько шагов формирователя.

В отклике выходные данные всегда имеют имя "output". На внутреннем уровне конвейер может сопоставить с "output" другое имя, например "analyzedText", как в приведенных ниже примерах, однако сам навык формирователя возвращает в отклике имя "output". Это может оказаться важным при обнаружении несоответствия имен во время отладки обогащенных документов или при создании пользовательского навыка с самостоятельным структурированием отклика.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Пример 1: сложные типы

Рассмотрим сценарий, где требуется создать структуру *analyzedText* с двумя членами: *text* и *sentiment*, соответственно. Поддерживающее поиск составное поле в службе "Поиск Azure" называется *сложным типом* и пока не поддерживается без дополнительной настройки. В этой предварительной версии навык формирователя можно использовать для создания полей сложного типа в индексе. 

Приведенный ниже пример предоставляет имена членов в качестве входных данных. Структура выходных данных (составное поле в Поиске Azure) указано с помощью *targetName*. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Пример ввода
Документ JSON, предоставляющий полезные входные данные для этого навыка формирователя, может иметь следующий вид:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="sample-output"></a>Пример выходных данных
Навык формирователя создает элемент *analyzedText* с объединенными элементами *text* и *sentiment*. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="sample-2-input-consolidation"></a>Пример 2: консолидация входных данных

В другом примере предположим, что на различных этапах обработки конвейера вы извлекли название книги и заголовки глав на разных страницах книги. Теперь вы можете создать единую структуру, состоящую из этих разнородных входных данных.

Определение навыка формирователя для этого сценария может выглядеть, как в следующем примере:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>Пример выходных данных
В этом случае формирователь преобразует все заголовки глав в плоскую структуру, чтобы создать один массив. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>См. также

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)

