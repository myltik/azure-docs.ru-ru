<properties
	pageTitle="Использование приложения Fiddler для оценки и тестирования интерфейсов REST API службы поиска Azure | Microsoft Azure | Размещенная облачная служба поиска"
	description="Приложение Fiddler позволяет проверить доступность службы ";Поиск Azure"; и опробовать интерфейсы API REST, не используя код."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/18/2016"
	ms.author="heidist"/>

# Использование приложения Fiddler для оценки и тестирования интерфейсов API REST "Поиск Azure"
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

В этой статье рассказывается, как использовать приложение Fiddler, которое можно [бесплатно скачать с сайта компании Telerik](http://www.telerik.com/fiddler). Оно позволяет формировать HTTP-запросы и просматривать ответы с помощью REST API службы поиска Azure без необходимости написания кода. Поиск Azure — это полностью управляемая облачная служба поиска в Microsoft Azure с простым программным управлением через API-интерфейсы REST и .NET. На сайте [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx) доступна документация, посвященная интерфейсам REST API службы поиска Azure.

Выполнив указанные ниже действия, вы создадите индекс, загрузите документы, выполните запрос к индексу, а затем запросите у системы служебную информацию.

Для выполнения этих действий вам потребуется подписка на службу "Поиск Azure" и `api-key`. Инструкции о том, как приступить к работе, приведены в [статье, посвященной созданию службы "Поиск Azure" на портале](search-create-service-portal.md) .

## Создание индекса

1. Запустите Fiddler. В меню **File** (Файл) отключите параметр **Capture Traffic** (Захватывать трафик), чтобы не отображать постороннюю активность HTTP, не связанную с текущей задачей.

3. На вкладке **Composer** (Компоновщик) сформулируйте запрос, аналогичный приведенному ниже.

  	![][1]

2. Выберите **PUT**.

3. Введите URL-адрес, который задает URL-адрес службы, атрибуты запроса и версию API. Не забывайте:
   + Используйте префикс HTTPS.
   + Атрибут запроса: "/indexes/hotels". В этом случае Поиск создает индекс с именем hotels.
   + Версия API вводится строчными буквами, в виде "?api-version=2015-02-28". Версии API имеют важное значение, так как обновления Поиска Azure разворачиваются на регулярной основе. В редких случаях обновление службы может привести к нарушению функционирования API. Поэтому службе поиска Azure требуется версия API каждого запроса, чтобы вы могли полностью контролировать используемый запрос.

    Полный URL-адрес должен выглядеть аналогично тому, как показано ниже.

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	Укажите заголовок запроса, заменив адрес узла и ключ API значениями, соответствующими вашей службе.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	В теле запроса скопируйте их в поля, формирующие определение индекса.

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

6.	Нажмите **Execute (Выполнить)**.

Через несколько секунд появится ответ HTTP 201. Это значит, что индекс успешно создан.

Если вы получите код HTTP 504, проверьте, что в URL указан протокол HTTPS. Если возникают сообщения об ответных кодах 400 или 404 протокола HTTP, следует удостовериться в отсутствии ошибок при копировании. Код HTTP 403, как правило, означает, что возникла ошибка с ключом API (либо он недействительный, либо имеется ошибка синтаксиса при указании ключа).

## Загрузка документов

На вкладке **Composer** (Компоновщик) запрос на публикацию документов будет выглядеть примерно так: Тело запроса содержит данные поиска для четырех отелей.

   ![][2]

1. Выберите **POST**.

2.	Введите URL-адрес, начинающийся с HTTPS, далее URL-адрес вашей службы, а затем "/indexes/<'indexname'>/docs/index?api-version=2015-02-28". Полный URL-адрес должен выглядеть аналогично тому, как показано ниже.

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	Заголовок запроса должен остаться таким же, как прежде. Не забывайте, что вы заменили адрес узла и ключ API (строчные буквы) значениями, соответствующими вашей службе.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Тело запроса содержит четыре документа, которые нужно добавить в индекс отелей.

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

8.	Нажмите **Execute (Выполнить)**.

Через несколько секунд в списке сеансов появится ответ HTTP 200. Это означает, что документы были успешно созданы. Код 207 говорит об ошибке загрузки хотя бы одного из документов. Код 404 говорит о синтаксической ошибке в заголовке или в теле запроса.

## Запрос индекса

После загрузки индекса и документов можно формировать запросы к ним. На вкладке **Composer** (Компоновщик) команда **GET**, которая отправляет запросы в вашу службу, будет выглядеть примерно так, как показано на следующем снимке экрана.

   ![][3]

1.	Выберите **GET**.

2.	Введите URL, начинающийся с HTTPS, далее URL вашей службы, далее "/indexes/<'indexname'>/docs?", далее параметры запроса. В качестве примера используйте этот URL, заменив имя узла на имя, соответствующее вашей службе.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Запрос ищет термин "мотель" и возвращает плоский список категорий для рейтинга.

3.	Заголовок запроса должен остаться таким же, как прежде. Не забывайте, что вы заменили адрес узла и ключ API (строчные буквы) значениями, соответствующими вашей службе.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Код ответа должен быть 200 и сам ответ должен выглядеть аналогично тому, как показано на снимке экрана ниже.

   ![][4]

Ниже приведен пример запроса из [Работа с поисковым индексом (API Поиска Azure)](http://msdn.microsoft.com/library/dn798927.aspx) на MSDN. Многие примеры запросов в данном разделе включают пробелы, что является недопустимым в Fiddler. Замените все пробелы символом "+" перед вставкой в строку запроса и его выполнением в Fiddler:

**До замены пробелов:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**После замены пробелов символом +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## Запрос к системе

Также можно формировать запросы к системе для получения количества документов и занятого объема хранилища. На вкладке **Composer** (Компоновщик) ваш запрос будет выглядеть примерно так, как показано ниже. В ответе будет указано количество документов и объем занятого пространства.

 ![][5]

1.	Выберите **GET**.

2.	Введите URL, включающий URL-адрес вашей службы, а затем "/indexes/hotels/stats?api-version=2015-02-28":

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.	Укажите заголовок запроса, заменив адрес узла и ключ API значениями, соответствующими вашей службе.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Не заполняйте тело запроса.

5.	Нажмите **Execute (Выполнить)**. Через несколько секунд в списке сеансов появится ответ HTTP 200. Выберите запись, которая сопровождается вашей командой.

6.	Щелкните вкладку **Inspectors** (Инспекторы), затем вкладку **Headers** (Заголовки) и выберите формат JSON. Вы увидите количество документов и занятый ими размер (в килобайтах).

## Дальнейшие действия

Сведения об управлении службой поиска Azure и ее использовании без написания кода см. в статье [Управление службой поиска в Microsoft Azure](search-manage.md).


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png

<!---HONumber=AcomDC_0224_2016-->