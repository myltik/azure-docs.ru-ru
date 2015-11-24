<properties
	pageTitle="Импорт данных в службу поиска Azure с помощью REST API | Microsoft Azure | Размещенная облачная служба поиска"
	description="Передача данных для индекса в службе поиска Azure с помощью REST API."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Импорт данных в службу поиска Azure с помощью REST API
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

В этой статье показано, как заполнить индекс с помощью [REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Часть содержимого ниже взята из статьи [Добавление, обновление или удаление документов (REST API службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798930.aspx). Дополнительные сведения см. в родительской статье.

Для передачи документов в индекс с помощью REST API следует отправлять HTTP-запросы POST к конечной точке URL-адреса вашей службы.

**Запрос и заголовки запроса**

В URL-адресе необходимо указать имя службы, а также правильную версию API (текущая версия API на момент публикации этого документа — «2015-02-28»).

В заголовках запроса необходимо определить тип содержимого и указать первичный ключ администратора службы.

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Текст запроса**


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
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
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

В данном случае мы используем действие поиска upload. При обновлении и удалении существующих документов можно использовать действия merge, mergeOrUpload и delete.

При успешном обновлении индекса вы получите код состояния «200 OK». Если хотя бы один элемент в запросе не будет успешно проиндексирован, вы получите код состояния «207».

Дополнительные сведения о действиях с документами и ответах на успешное завершение и ошибки см. [на этой странице](https://msdn.microsoft.com/library/azure/dn798930.aspx).

<!---HONumber=Nov15_HO4-->