---
title: Работа с REST API в Fiddler или Postman (REST службы "Поиск Azure") | Документация Майкрософт
description: Как использовать Fiddler или Postman для отправки HTTP-запросов и вызовов REST API в службу "Поиск Azure".
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: eba41086da645c2ff5cee65f9395267227cb1c11
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190191"
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Работа с REST API службы "Поиск Azure" с помощью Fiddler или Postman

Проще всего изучить [REST API службы "Поиск Azure"](https://docs.microsoft.com/rest/api/searchservice) с помощью Fiddler или Postman для формирования HTTP-запросов и изучения ответов. С помощью соответствующих средств и этих инструкций вы сможете отправлять запросы и просматривать ответы без написания кода.

> [!div class="checklist"]
> * Скачивание средства тестирования веб-API
> * Получение ключа API и конечной точки для службы поиска.
> * Настройка заголовков запроса
> * Создание индекса
> * Загрузка индекса.
> * Поиск в индексе

Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) перед началом, а затем [зарегистрируйтесь в службе "Поиск Azure"](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Скачивание и установка инструментов

Следующие инструменты широко используются в веб-разработке, но если вы знакомы с другим инструментом, инструкции в этой статье по-прежнему будут полезны.

+ [Классическое приложение Postman](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Получение ключа API и конечной точки

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. На портале Azure откройте страницу службы поиска на панели мониторинга или [найдите свою службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в списке служб.
2. Получите конечную точку, выбрав **Обзор** > **Основное** > **URL-адрес**. Пример конечной точки может выглядеть так: `https://my-service-name.search.windows.net`.
3. Получите ключ API в разделе **Параметры** > **Ключи**. Существует два ключа администратора для обеспечения избыточности, если требуется смена ключей. Ключи администратора предоставляют разрешения на запись для службы, необходимые для создания и загрузки индексов. Вы можете использовать первичный или вторичный ключ для операций записи.

## <a name="configure-request-headers"></a>Настройка заголовков запроса

Каждое средство сохраняет сведения заголовка запроса для сеанса. Это означает, что URL-адрес конечной точки, версию и ключ API, а также тип содержимого нужно ввести только один раз.

Полный URL-адрес должен выглядеть как в следующем примере, только в вашем адресе имя в заполнителе **`my-app`** нужно заменить: `https://my-app.search.windows.net/indexes/hotels?api-version=2017-11-11`

Композиция URL-адреса службы включает следующие элементы:

+ Префикс HTTPS.
+ URL-адрес службы, полученный с портала.
+ Ресурс (операция, создающая объект в службе). На этом шаге это индекс с именем hotels.
+ Версия API, необходимая строка в нижнем регистре (?api-version=2017-11-11) для текущей версии. [Версии API](search-api-versions.md) регулярно обновляются. Включая версию API для каждого запроса дает полный контроль над используемой версией.  

Композиция заголовка запроса содержит два элемента, тип содержимого и ключ API, описанные в предыдущем разделе:

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Сформулируйте запрос, который выглядит, как на следующем снимке экрана. Выберите **PUT** в качестве команды. Fiddler добавит `User-Agent=Fiddler`. Вы можете вставить два дополнительных заголовка запроса в новых строках под ней. Добавьте тип содержимого и ключ API для своей службы с помощью ключа доступа администратора службы.

![Заголовок запроса Fiddler][1]

> [!Tip]
> Вы можете отключить веб-трафик, чтобы скрыть лишние действия HTTP, не связанные с выполняемыми задачами. В Fiddler перейдите к меню **Файл** и выключите параметр **Capture Traffic** (Запись трафика). 

### <a name="postman"></a>postman

Сформулируйте запрос, который выглядит, как на следующем снимке экрана. Выберите **PUT** в качестве команды. 

![Заголовок запроса Postman][6]

## <a name="create-the-index"></a>Создание индекса

Текст запроса содержит определение индекса. При добавлении текста запрос, создающий индекс, завершается.

Помимо имени индекса самым важным компонентом в запросе является коллекция полей. Коллекция полей определяет схему индекса. Укажите тип для каждого поля. Строковые поля используются в полнотекстовом поиске, поэтому может потребоваться привести числовые данные как строки, если требуется, чтобы содержимое было доступно для поиска.

Атрибуты поля определяют допустимое действие. Интерфейсы REST API по умолчанию позволяют выполнять множество действий. Например, по умолчанию все строки доступны для поиска, извлечения, фильтрования и применения аспектов. Часто для отключения поведения нужно просто установить атрибуты. Дополнительные сведения об атрибутах см. в статье [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индекса (REST API службы "Поиск Azure")).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }


При отправке запроса вы должны получить ответ HTTP 201, указывающий, что индекс создан успешно. Это действие можно проверить на портале, однако обратите внимание, что на странице портала настроены интервалы обновления, поэтому изменения отразятся через минуту или две.

Если вы получите код HTTP 504, проверьте, что в URL указан протокол HTTPS. Если возникают сообщения об ответных кодах 400 или 404 протокола HTTP, следует удостовериться в отсутствии ошибок при копировании. Код HTTP 403, как правило, означает, что возникла ошибка с ключом API (либо он недействительный, либо имеется ошибка синтаксиса при указании ключа).

### <a name="fiddler"></a>Fiddler

Скопируйте определение индекса в текст запроса, как на следующем снимке экрана, а затем щелкните **Выполнить** в верхней правой части страницы для отправки выполненного запроса.

![Текст запроса Fiddler][7]

### <a name="postman"></a>postman

Скопируйте определение индекса в текст запроса, как на следующем снимке экрана, а затем щелкните **Отправить** в верхней правой части страницы для отправки выполненного запроса.

![Текст запроса Postman][8]

## <a name="load-documents"></a>Загрузка документов

Создание и заполнение индекса выполняется по отдельности. В службе "Поиск Azure" индекс содержит все данные с возможностью поиска, которые можно указать в виде документов JSON. Сведения о просмотре этой операции в API см. в статье [Add, Update or Delete Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Добавление, обновление или удаление документов (REST API службы "Поиск Azure")).

+ Изменить команду на **POST** для этого шага.
+ Измените конечную точку для включения `/docs/index`. Полный URL-адрес должен выглядеть так: `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`.
+ Сохраните заголовки запроса без изменений. 

Тело запроса содержит четыре документа, которые нужно добавить в индекс отелей.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Через несколько секунд в списке сеансов появится ответ HTTP 200. Это означает, что документы были успешно созданы. 

Код 207 говорит об ошибке загрузки хотя бы одного из документов. Если вы получаете код 404, текст или заголовок запроса содержат синтаксические ошибки. Убедитесь, что вы изменили конечную точку, чтобы включить `/docs/index`.

> [!Tip]
> Для выбранных источников данных можно выбрать альтернативный подход *индексатора*, упрощающий работу и уменьшающий объем кода, необходимого для индексирования. Дополнительные сведения см. в статье [Indexer operations (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Операции индексатора (REST API службы "Поиск Azure")).

### <a name="fiddler"></a>Fiddler

Изменить команду на **POST**. Измените URL-адрес для включения `/docs/index`. Скопируйте документы в текст запроса, как на следующем снимке экрана, а затем выполните запрос.

![Полезные данные запроса Fiddler][9]

### <a name="postman"></a>postman

Изменить команду на **POST**. Измените URL-адрес для включения `/docs/index`. Скопируйте документы в текст запроса, как на следующем снимке экрана, а затем выполните запрос.

![Полезные данные запроса Postman][10]

## <a name="query-the-index"></a>Запрос индекса
После загрузки индекса и документов можно формировать запросы к ним. Дополнительные сведения об этом API см. в статье [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Поиск документов (REST API службы "Поиск Azure")).  

+ Изменить команду на **GET** для этого шага.
+ Измените конечную точку, чтобы добавить параметры запроса, включая строки поиска. Запрос URL-адреса может выглядеть так: `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`.
+ Сохраните заголовки запроса без изменений.

Этот запрос выполняет поиск слова motel и возвращает число документов в результатах поиска. После того, как вы щелкнете **Отправить**, запрос и ответ для Postman будет выглядеть как на следующем снимке экрана. Код состояния должен быть 200.

 ![Ответ на запрос Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Советы по выполнению наших примеров запросов в Fiddler

Ниже приведен пример запроса из статьи [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Поиск документов (REST API службы "Поиск Azure")). Многие примеры запросов в этой статье включают пробелы, что является недопустимым в Fiddler. Замените все пробелы символом "+" перед вставкой в строку запроса и его выполнением в Fiddler:

**Перед заменой пробелов (в части lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**После замены пробелов на + (в части lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

## <a name="query-index-properties"></a>Запрос свойств индекса
Вы также можете запрашивать системную информацию для получения количества документов и занятого объема хранилища: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`.

В Postman запрос должен выглядеть следующим образом, а ответ включает число документов и используемое пространство в байтах.

 ![Системный запрос Postman][12]

Обратите внимание, что синтаксис api-version отличается. Для этого запроса используйте `?` для добавления api-version. Знак вопроса отделяет URL-адрес от строки запроса, а знак & отделяет каждую пару name=value в строке запроса. Для этого запроса api-version — первый и единственный элемент в строке запроса.

Дополнительные сведения об этом API см. в статье [Get Index Statistics (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) (Получение статистики индекса (REST API службы "Поиск Azure")).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Советы по просмотру статистики индекса в Fiddler

В Fiddler откройте вкладку **Inspectors** (Инспекторы), затем вкладку **Headers** (Заголовки) и выберите формат JSON. Вы увидите количество документов и занятый ими размер (в килобайтах).

## <a name="next-steps"></a>Дополнительная информация

Клиенты REST являются очень полезными для незапланированного исследования, но теперь, когда вы знаете, как работают REST API, можно приступить к написанию кода. Следующие шаги см. в статьях по следующим ссылкам:

+ [Создание индекса службы поиска Azure с помощью REST API](search-create-index-rest-api.md)
+ [Отправка данных в службу поиска Azure с помощью REST API](search-import-data-rest-api.md)
+ [Отправка запросов в индекс службы поиска Azure с помощью REST API](search-query-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png