---
title: Навык когнитивного поиска анализа изображений ("Поиск Azure") | Документы Майкрософт
description: Извлечение семантического текста посредством анализа изображений с помощью когнитивного навыка ImageAnalysis в конвейере обогащения в службе "Поиск Azure".
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: dd26dbe34cd04d1ad3184e2cd62afae5166ac914
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640512"
---
#   <a name="image-analysis-cognitive-skill"></a>Когнитивный навык анализа изображений

Навык **анализа изображений** извлекает полноценный набор визуальных компонентов на основе содержимого изображения. Например, можно создать заголовок из изображения, сформировать теги, а также определить знаменитостей и ориентиры.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра     | ОПИСАНИЕ |
|--------------------|-------------|
| defaultLanguageCode   |  Строка, указывающая язык для возврата данных. Служба возвращает результаты распознавания на указанном языке. Если этот параметр не задан, используется значение по умолчанию "en". <br/><br/>Поддерживаемые языки: <br/>*en* — английский (по умолчанию) <br/> *zh* — китайский (упрощенное письмо)|
|visualFeatures |   Массив строк, указывающих возвращаемые типы визуальных компонентов. Допустимые типы визуальных компонентов:  <ul><li> *categories* — классифицирует содержимое изображения в соответствии с таксономией, определенной в [документации](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy) по Cognitive Services.</li><li> *tags* — помечает изображение подробным списком слов, связанных с содержимым изображения.</li><li>*Description* — описывает содержимое изображения полным предложением на английском языке.</li><li>*Faces* — определяет наличие лиц. При их наличии задает координаты, возраст и пол.</li><li> *ImageType* — определяет, является ли изображение картинкой или чертежом.</li><li>   *Color* — определяет контрастный цвет, преобладающий цвет и то, является ли изображение черно-белым.</li><li>*Adult* — определяет, носит ли изображение порнографический характер (показывает наготу или половой акт). Кроме того, обнаруживаются материалы непристойного содержания.</li></ul> Имена визуальных компонентов зависят от регистра.|
| сведения   | Массив строк, указывающих возвращаемые сведения о домене. Допустимые типы визуальных компонентов: <ul><li>*Celebrities* — определяет знаменитостей, если они обнаружены на изображении.</li><li>*Landmarks* — определяет ориентиры, если они обнаружены на изображении.</li></ul>
 |

## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени      | ОПИСАНИЕ                                          |
|---------------|------------------------------------------------------|
| изображение         | Сложный тип. В настоящее время работает только с полем /document/normalized_images, созданным индексатором большого двоичного объекта Azure, если для ```imageAction``` установлено значение ```generateNormalizedImages```. Дополнительные сведения см. в [этом примере](#sample-output).|



##  <a name="sample-definition"></a>Пример определения

```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
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
                "url": "https://storagesample.blob.core.windows.net/sample-container/image.jpg"
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
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceRectangle": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceRectangle": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBWImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>Варианты ошибок
При указанных ниже ошибках никакие элементы не извлекаются.

| Код ошибки | ОПИСАНИЕ |
|------------|-------------|
| NotSupportedLanguage | Указанный язык не поддерживается. |
| InvalidImageUrl | URL-адрес изображения имеет неправильный формат или недоступен.|
| InvalidImageFormat | Входные данные не являются допустимым изображением. |
| InvalidImageSize | Входное изображение слишком велико. |
| NotSupportedVisualFeature  | Указанный тип компонента недопустим. |
| NotSupportedImage | Неподдерживаемое изображение, например детская порнография. |
| InvalidDetails | Неподдерживаемая доменная модель. |

## <a name="see-also"></a>См. также

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Создание индексатора (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
