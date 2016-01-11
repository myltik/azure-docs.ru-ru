<properties
	pageTitle="Создание запросов в службе поиска Azure с помощью вызовов REST | Microsoft Azure | Размещенная облачная служба поиска"
	description="В службе поиска Azure можно создать поисковый запрос и использовать параметры поиска для фильтрации, сортировки и уточнения результатов поиска с помощью библиотеки .NET или пакета SDK."
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

# Создание запросов в службе поиска Azure с помощью вызовов REST
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

В этой статье показано, как создать запрос к индексу с помощью [REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Часть содержимого ниже взята из статьи [Поиск документов (REST API службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Дополнительные сведения см. в родительской статье.

Для импорта необходимо наличие индекса, в который загружены документы с данными для поиска.

Для поиска в индексе с помощью REST API следует отправить HTTP-запрос GET. Параметры запроса будут определены в URL-адресе HTTP-запроса.

**Запрос и заголовки запроса**

В URL-адресе необходимо указать имя службы, имя индекса, а также нужную версию API. Параметры запроса указываются в строке запроса в конце URL-адреса. В одном из параметров в строке запроса должна быть указана нужная версия API (текущая версия API на момент публикации этого документа — 2015-02-28).

В заголовках запроса необходимо определить тип содержимого и указать первичный или вторичный ключ администратора службы.

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Служба поиска Azure предлагает множество параметров для создания расширенных запросов. Дополнительные сведения о различных параметрах строки запроса см. на [этой странице](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Примеры**

Ниже приведено несколько примеров с различными строками запросов. В этих образцах используется фиктивный индекс с именем hotels.

Поиск термина quality по всему индексу:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Поиск по всему индексу:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Поиск по всему индексу и сортировка по определенному полю (lastRenovationDate):

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

При успешном выполнении запроса возвращается код состояния «200 OK», а результаты поиска сохраняются в формате JSON в тексте ответа. Дополнительные сведения см. в разделе «Ответ» на [этой странице](https://msdn.microsoft.com/library/azure/dn798927.aspx).

<!---HONumber=AcomDC_1223_2015-->