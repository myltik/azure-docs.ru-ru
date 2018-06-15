---
title: Создание индекса с помощью REST API службы поиска Azure | Документация Майкрософт
description: Программное создание индекса с помощью HTTP REST API службы поиска Azure.
author: chaosrealm
manager: jlembicz
ms.author: eugenesh
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.openlocfilehash: 86260e1929e8bd5c31682ef7f59cacae19a66020
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182037"
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Создание индекса службы поиска Azure с помощью REST API
> [!div class="op_single_selector"]
>
> * [Обзор](search-what-is-an-index.md)
> * [Портал](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Эта статья поможет вам создать [индекс](https://docs.microsoft.com/rest/api/searchservice/Create-Index) службы поиска Azure с помощью REST API службы поиска Azure.

Перед выполнением инструкций, приведенных в этом руководстве, и созданием индекса следует [создать службу поиска Azure](search-create-service-portal.md).

Чтобы создать индекс службы поиска Azure с помощью REST API, вам нужно отправить один HTTP-запрос POST к конечной точке службы поиска Azure по определенному адресу. Определение индекса будет содержаться в тексте запроса как JSON-содержимое правильного формата.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Определение ключа API администратора службы поиска Azure
Подготовив службу поиска Azure, вы можете отправлять HTTP-запросы к конечной точке вашей службы по определенному URL-адресу, используя REST API. *Все* запросы API должны содержать ключ API, который был создан для подготовленной службы поиска. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

1. Чтобы найти ключи API своей службы, войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к колонке службы поиска Azure.
3. Щелкните значок "Ключи".

Ваша служба получит *ключи администратора* и *ключи запросов*.

* Первичные и вторичные *ключи администратора* предоставляют полный доступ ко всем операциям, включая возможность управлять службой, создавать и удалять индексы, индексаторы и источники данных. Ключей два, поэтому вы можете и дальше использовать вторичный ключ, если решите повторно создать первичный ключ, и наоборот.
* *Ключи запросов* предоставляют только разрешение на чтение индексов и документов; обычно они добавляются в клиентские приложения, которые создают запросы на поиск.

Для создания индекса можно использовать первичный или вторичный ключ администратора.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Определение индекса службы поиска Azure с помощью JSON-содержимого правильного формата
Создать индекс можно с помощью одного HTTP-запроса POST к службе. Текст HTTP-запроса POST должен содержать один объект JSON, определяющий индекс службы поиска Azure.

1. Первое свойство этого объекта JSON — имя индекса.
2. Второе свойство — массив JSON с именем `fields`, содержащий отдельный объект JSON для каждого поля в индексе. Каждый из этих объектов JSON содержит несколько пар "имя — значение" для каждого из атрибутов поля, включая name, type и т. д.

При проектировании индекса важно помнить о бизнес-потребностях и удобстве работы с поиском, поэтому каждому полю необходимо назначить [правильные атрибуты](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Эти атрибуты контролируют, какие функции поиска (фильтрация, фасетная навигация, сортировка полнотекстового поиска и т. д.) применяются к каждому полю. Если атрибут не указан, по умолчанию будет использоваться соответствующая функция поиска, если вы специально не отключите ее.

В нашем примере мы присвоили индексу имя hotels и определили поля следующим образом:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Мы тщательно выбрали атрибуты индекса для каждого поля в зависимости от того, как мы планируем использовать их в приложении. Например, `hotelId` — это уникальный ключ, который, скорее всего, не будет известен пользователям, ищущим гостиницы. Поэтому мы отключаем полнотекстовый поиск для этого поля, задав для `searchable` значение `false`, что экономит место в индексе.

Обратите внимание, что только одно поле в индексе типа `Edm.String` должно быть назначено как поле key.

В приведенном выше определении индекса используется языковой анализатор для поля `description_fr`, так как оно предназначено для текста на французском языке. Дополнительные сведения об анализаторах языка см. в статье [Language support (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Language-support) (Поддержка языков (REST API службы поиска Azure)), а также в соответствующей [записи блога](https://azure.microsoft.com/blog/language-support-in-azure-search/).

## <a name="issue-the-http-request"></a>Отправка HTTP-запроса
1. Используя определение индекса в качестве текста запроса, отправьте HTTP-запрос POST в конечную точку службы поиска Azure по URL-адресу. В URL-адресе в качестве имени узла следует использовать имя службы. Также важно правильно указать `api-version` как параметр строки запроса (текущая версия API на момент публикации этого документа — `2017-11-11`).
2. В заголовках запроса укажите `Content-Type` в качестве `application/json`. Необходимо также указать ключ администратора службы, который мы определили в заголовке `api-key` (см. шаг 1).

Вы должны указать собственные имя службы и ключ API, чтобы выполнить приведенный ниже запрос.

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [api-key]


При успешном выполнении запроса возвращается код состояния 201 (индекс создан). Дополнительные сведения о создании индекса с помощью REST API см. в [справочнике по API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Дополнительные сведения о других кодах состояния HTTP, которые могут быть возвращены в случае сбоя, см. в статье [Коды состояния HTTP (поиск в Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

Если вы завершили работу с индексом и хотите удалить его, просто отправьте HTTP-запрос DELETE. Например, индекс hotels удаляется так:

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11
    api-key: [api-key]


## <a name="next-steps"></a>Дополнительная информация
Создав индекс службы поиска Azure, вы сможете [передать в него содержимое](search-what-is-data-import.md) и искать нужные вам данные.
