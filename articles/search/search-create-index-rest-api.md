<properties
	pageTitle="Создание индекса службы поиска Azure с помощью REST API | Microsoft Azure | Размещенная облачная служба поиска"
	description="Индекс можно создать в коде с помощью пакета службы поиска Azure и HTTP REST API."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Создание индекса службы поиска Azure с помощью REST API
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

В этой статье мы расскажем, как создать индекс с помощью [REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Часть содержимого ниже взята из статьи [Создание индекса (REST API службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Дополнительные сведения см. в родительской статье.

Для создания индекса необходимо подключение к службе поиска. Обычно оно устанавливается с помощью httpClient.

## Определение схемы индекса

Чтобы создать индекс с помощью REST API, отправьте запрос POST к конечной точке URL-адреса службы поиска Azure. Структура индекса определяется с помощью JSON в тексте запроса.

**Запрос и заголовки запроса**

В примере ниже необходимо использовать конечную точку URL-адреса вашей службы, в частности указать имя службы и правильную версию API (текущая версия API на момент публикации этого документа — 2015-02-28). Кроме того, в заголовках запроса необходимо указать первичный ключ администратора, полученный при [создании службы](https://msdn.microsoft.com/library/azure/dn798941.aspx/).

	POST https://[servicename].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Текст запроса**

Здесь указывается имя индекса (в данном случае — hotels), а также [имена, типы и атрибуты полей](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	{
		"name": "hotels",  
		"fields": [
			{"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
			{"name": "baseRate", "type": "Edm.Double"},
			{"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
			{"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer:"fr.lucene"},
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

При успешном выполнении запроса возвращается код состояния «201 — Создан ресурс». Дополнительные сведения о создании индекса с помощью REST API см. на [этой странице](https://msdn.microsoft.com/library/azure/dn798941.aspx).

<!---HONumber=Nov15_HO4-->