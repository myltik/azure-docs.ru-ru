---
title: Фильтры безопасности для усечения результатов в службе "Поиск Azure" | Документация Майкрософт
description: Управление доступом к содержимому службы "Поиск Azure" с помощью фильтров безопасности и удостоверений пользователей.
ms.service: search
ms.topic: conceptual
services: search
ms.date: 08/07/2017
author: revitalbarletz
ms.author: revitalb
manager: jlembicz
ms.openlocfilehash: dd26676b74431566b3631b8a79cd06bcf3022518
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792806"
---
# <a name="security-filters-for-trimming-results-in-azure-search"></a>Фильтры безопасности для усечения результатов в службе "Поиск Azure"

Фильтры безопасности можно применять для усечения результатов поиска в службе "Поиск Azure" на основе удостоверения пользователя. В рамках этого поиска, как правило, требуется сравнить личность инициатора поискового запроса с указанными в поле субъектами, у которых есть разрешения на документ. Если совпадение обнаружено, пользователь или субъект (например, группа или роль) имеет доступ к этому документу.

Один из способов реализации фильтров безопасности — через логическое сложение выражений равенства, например, `Id eq 'id1' or Id eq 'id2'` и т. д. Это ненадежный подход, его трудно поддерживать, а в тех случаях, когда список содержит сотни или тысячи значений, замедляется время ответа на запрос (в секундах). 

Использование функции `search.in` является более надежным и простым способом. При использовании `search.in(Id, 'id1, id2, ...')` вместо выражения равенства можно ожидать ответа со временем отклика менее секунды.

В этой статье показано, как реализовать фильтр безопасности, выполнив следующие действия:
> [!div class="checklist"]
> * Создание поля, содержащего идентификаторы субъектов. 
> * Принудительная отправка или обновление имеющихся документов с соответствующими идентификаторами субъектов.
> * Отправка поискового запроса с помощью `search.in` `filter`.

>[!NOTE]
> В этом документе не рассматривается процесс получения идентификаторов субъектов. Его следует узнать у поставщика службы идентификации.

## <a name="prerequisites"></a>предварительным требованиям

В этой статье предполагается наличие [подписки Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), [службы поиска Azure](https://docs.microsoft.com/azure/search/search-create-service-portal) и [индекса поиска Azure](https://docs.microsoft.com/azure/search/search-create-index-portal).  

## <a name="create-security-field"></a>Создание поля безопасности

Документы должны включать поле со списком групп, у которых есть доступ. Эта информация выступает условием фильтрации, по которому документы выбираются или отклоняются из результирующего набора, возвращаемого инициатору запроса.
Предположим, что у нас есть индекс защищенных файлов, и каждый файл доступен разным наборам пользователей.
1. Добавьте поле `group_ids` (здесь можно выбрать любое имя) в качестве `Collection(Edm.String)`. Задайте для атрибута `filterable` этого поля значение `true`. В таком случае результаты поиска фильтруются на основе прав доступа пользователя. Например, если в поле `group_ids` задать значение `["group_id1, group_id2"]` для документа со свойством `file_name` "secured_file_b", разрешение на чтение файла имеют только пользователи, принадлежащие к группам "group_id1" или "group_id2".
   Убедитесь, что для атрибута поля `retrievable` задано значение `false`, чтобы пользователи не могли извлечь файлы в рамках поискового запроса.
2. Кроме того, добавьте поля `file_id` и `file_name` для данного примера.  

```JSON
{
    "name": "securedfiles",  
    "fields": [
        {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "file_name", "type": "Edm.String"},
        {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
    ]
}
```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Принудительная отправка данных в индекс с использованием REST API
  
Отправьте запрос HTTP POST к конечной точке URL-адреса индекса. Текст HTTP-запроса является объектом JSON, содержащим документы, которые нужно добавить.

```
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=[api-version]  
Content-Type: application/json
api-key: [admin key]
```

В тексте запроса укажите содержимое документов:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Если нужно обновить для имеющегося документа список групп, можно использовать действие `merge` или `mergeOrUpload`.

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Полные сведения о добавлении или обновлении документов см. в статье [об изменении документов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).
   
## <a name="apply-the-security-filter"></a>Применение фильтра безопасности

Чтобы сократить количество документов на основе доступа по `group_ids`, выполните поисковой запрос с использованием фильтра `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` (где "group_id1 group_id2,..." — это группы, к которым принадлежит инициатор запроса).
Этот фильтр соответствует всем документам, для которых поле `group_ids` содержит один из заданных идентификаторов.
Дополнительные сведения о поиске документов с помощью службы поиска Azure см. в статье [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Поиск документов (REST API службы поиска Azure)).
Обратите внимание, что в этом примере показано, как осуществлять поиск документов с помощью запроса POST.

Выполните запрос HTTP POST:

```
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=[api-version]  
Content-Type: application/json  
api-key: [admin or query key]
```

В тексте запроса укажите фильтр:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Будут возвращены документы, в поле `group_ids` которых содержится "group_id1" или "group_id2". Другими словами, вы получаете документы, к которым у инициатора запроса есть доступ на чтение.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```
## <a name="conclusion"></a>Заключение

Вы узнали о том, как можно фильтровать результаты на основе удостоверения пользователя с использованием функции `search.in()` службы поиска Azure. При применении этой функции идентификаторы субъектов для запрашивающего пользователя сопоставляются с идентификаторами субъектов, связанными с каждым целевым документом. Во время обработки поискового запроса функция `search.in` отфильтровывает результаты поиска, отклоняя файлы, для которых ни один из субъектов-пользователей не имеет доступа на чтение. Идентификаторы субъектов могут представлять группы безопасности, роли или даже удостоверение пользователя.
 
## <a name="see-also"></a>См. также

+ [Security filters for trimming Azure Search results using Active Directory identities](search-security-trimming-for-azure-search-with-aad.md) (Фильтры безопасности для обрезки результатов Поиска Azure с использованием удостоверений Active Directory)
+ [Фильтры в службе "Поиск Azure"](search-filters.md)
+ [Безопасность данных и управляемый доступ в операциях службы "Поиск Azure"](search-security-overview.md)