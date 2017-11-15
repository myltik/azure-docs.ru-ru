---
title: "moreLikeThis в службе \"Поиск Azure\" (предварительная версия) | Документация Майкрософт"
description: "Предварительная документация по компоненту moreLikeThis (предварительная версия), доступному в REST API службы \"Поиск Azure\"."
services: search
documentationCenter: na
authors: mhko
manager: jlembicz
editor: na
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: 20f6a1166d47799f15275cb5b6a9ea4934ae57e2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis в службе "Поиск Azure" (предварительная версия)

`moreLikeThis=[key]` — это параметр запроса в [API поиска](https://docs.microsoft.com/rest/api/searchservice/search-documents). Указав параметр `moreLikeThis` в поисковом запросе, можно выполнить поиск документов, похожих на документ, указанный с помощью ключа. При обработке поискового запроса с параметром `moreLikeThis` создается запрос с условиями поиска, извлеченными из заданного документа, которые лучше всего описывают этот документ. Затем созданный запрос используется для создания поискового запроса. По умолчанию анализируется содержимое всех полей с атрибутом `searchable`, если для ограничения полей не используется параметр `searchFields`. Параметр `moreLikeThis` нельзя использовать с параметром поиска `search=[string]`.

## <a name="examples"></a>Примеры 

Ниже приведен пример запроса moreLikeThis. Запрос выполняет поиск документов, поля описания которых сильнее всего похожи на поле исходного документа, как указано в параметре `moreLikeThis`.

```  
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```  

```  
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {  
      "moreLikeThis": "1002",  
      "searchFields": "description"  
    }  
```  

## <a name="feature-availability"></a>Доступность функций

Сейчас функция moreLikeThis доступна в предварительной версии и поддерживается только предварительными версиями API `2015-02-28-Preview` и `2016-09-01-Preview`. Так как версия API указывается в запросе, можно совмещать общедоступную и предварительную версии API в одном приложении. Тем не менее предварительные версии API не регулируются соглашением об уровне обслуживания, и их возможности могут измениться, поэтому мы не рекомендуем использовать их в рабочих приложениях.