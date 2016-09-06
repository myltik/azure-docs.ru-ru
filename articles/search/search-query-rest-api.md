.<properties
    pageTitle="Отправка запросов в индекс службы поиска Azure с помощью REST API | Microsoft Azure | Размещенная облачная служба поиска"
    description="В службе поиска Azure можно создавать поисковые запросы и с помощью параметров поиска фильтровать, сортировать и уточнять результаты."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Отправка запросов в индекс службы поиска Azure с помощью REST API
> [AZURE.SELECTOR]
- [Обзор](search-query-overview.md)
- [Портал](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

В этой статье показано, как отправлять запросы в индекс с помощью [REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Прежде чем приступать к выполнению инструкций из этого руководства, необходимо [создать индекс службы поиска Azure](search-what-is-an-index.md) и [заполнить его данными](search-what-is-data-import.md).

## 1\. Определение ключа API запроса службы поиска Azure
Ключевым компонентом каждой операции поиска с REST API службы поиска Azure является *ключ API*, созданный для службы, которую вы подготовили. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

1. Чтобы найти ключи API своей службы, войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к колонке службы поиска Azure.
3. Щелкните значок "Ключи".

Ваша служба получит *ключи администратора* и *ключи запросов*.

 - Первичные и вторичные *ключи администратора* предоставляют полный доступ ко всем операциям, включая возможность управлять службой, создавать и удалять индексы, индексаторы и источники данных. Ключей два, поэтому вы можете и дальше использовать вторичный ключ, если решите повторно создать первичный ключ, и наоборот.
 - *Ключи запросов* предоставляют только разрешение на чтение индексов и документов; обычно они добавляются в клиентские приложения, которые создают запросы на поиск.

Для отправки запросов в индекс можно использовать один из ключей запросов. Для запросов можно использовать также ключи администратора, но в коде приложения следует использовать ключ запроса, так как этот вариант лучше соответствует [принципу предоставления минимальных прав](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## 2\. Формулировка запроса
Существует два способа [поиска в индексе с помощью REST API](https://msdn.microsoft.com/library/azure/dn798927.aspx). Один из них — отправка HTTP-запроса POST с текстом, содержащим объект JSON, в котором определены параметры запроса. Другой способ — отправка HTTP-запроса GET, содержащего URL-адрес, в котором определены параметры запроса. Обратите внимание, что в POST [менее жесткие ограничения](https://msdn.microsoft.com/library/azure/dn798927.aspx) на размер параметров запроса, чем в GET. Поэтому мы рекомендуем использовать запрос POST, за исключением особых случаев, когда удобнее использовать запрос GET.

Как для POST, так и для GET в URL-адресе запроса необходимо указать *имя службы*, *имя индекса*, а также правильную *версию API* (текущая версия API на момент публикации этого документа — `2015-02-28`). Если вы используете GET, параметры запроса указываются в *строке запроса* в конце URL-адреса. Ниже приведен формат URL-адреса.

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

Формат для POST такой же, но в параметрах в строке запроса указывается только версия API.



#### Примеры запросов

Вот несколько примеров запросов к индексу с именем hotels. Эти запросы отображаются в формате GET и POST.

Здесь выполняется поиск термина budget по всему индексу и возвращается только поле `hotelName`.

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Примените фильтр к индексу, чтобы найти гостиницы с номерами дешевле 150 долларов США за ночь, и получите значения `hotelId` и `description`.

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Здесь выполняются поиск по всему индексу и сортировка по полю `lastRenovationDate` в порядке убывания, выбираются два первых результата и отображаются только значения `hotelName` и `lastRenovationDate`.

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## 3\. Отправка HTTP-запроса
Теперь, когда вы сформулировали запрос как часть URL-адреса HTTP-запроса (для GET) или основного текста (для POST), можно определить заголовки запроса и отправить запрос.

#### Запрос и заголовки запроса
Необходимо определить два заголовка запроса для GET или три — для POST:
1. В заголовке `api-key` должно быть задано значение ключа запроса, определенное на шаге 1 выше. Обратите внимание, что в качестве заголовка `api-key` также можно использовать ключ администратора, но рекомендуется использовать ключ запроса, так как он предоставляет доступ к индексам и документам только с правами на чтение.
2. В заголовке `Accept` должно быть задано значение `application/json`.
3. Только для запросов POST: в заголовке `Content-Type` тоже должно быть задано значение `application/json`.

Ниже приведен HTTP-запрос GET для поиска по индексу hotels с помощью REST API службы поиска Azure и простого запроса, который ищет термин motel.

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Вот тот же пример запроса, но на этот раз используется HTTP-запрос POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

При успешном выполнении запроса возвращается код состояния `200 OK`, а результаты поиска возвращаются в формате JSON. Вот так выглядят результаты приведенного выше запроса при условии, что индекс hotels заполнен примерами данных из статьи [Импорт данных в службе поиска Azure с помощью REST API](search-import-data-rest-api.md) (код JSON отформатирован для наглядности).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Дополнительные сведения см. в статье [Поиск документов (REST API службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) в разделе "Ответ". Дополнительные сведения о других кодах состояния HTTP, которые могут быть возвращены в случае сбоя, см. в статье [Коды состояния HTTP (поиск Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

<!---HONumber=AcomDC_0831_2016-->