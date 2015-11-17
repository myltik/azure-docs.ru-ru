<properties
	pageTitle="Создание запросов в службе поиска Azure с помощью .NET | Microsoft Azure | Размещенная облачная служба поиска"
	description="В службе поиска Azure можно создать поисковый запрос и использовать параметры поиска для фильтрации, сортировки и уточнения результатов поиска с помощью библиотеки .NET или пакета SDK."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Создание запросов в службе поиска Azure с помощью .NET
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

В этой статье мы расскажем, как создать запрос с помощью [пакета SDK для .NET службы поиска Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). Содержимое ниже взято из статьи [Использование службы поиска Azure в приложении .NET](search-howto-dotnet-sdk.md). Пошаговые инструкции см. в родительской статье.

Для создания запроса необходимо подключение к службе поиска. Обычно оно устанавливается с помощью `SearchServiceClient`.

В следующем фрагменте кода создается метод, который передает входную строку поиска в метод SearchDocuments.

	private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
	{
		// Execute search based on search text and optional filter
		var sp = new SearchParameters();
	
		if (!String.IsNullOrEmpty(filter))
		{
			sp.Filter = filter;
		}
	
		DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
		foreach (SearchResult<Hotel> result in response)
		{
			Console.WriteLine(result.Document);
		}
	}
	
Сначала этот метод создает объект SearchParameters. Это позволяет задать дополнительные параметры запроса, например сортировку, фильтрацию, разбиение на страницы и фасетизацию. В этом примере задается только свойство Filter.

Следующий этап — фактическое выполнение поискового запроса. Для этого используется метод Documents.Search. В этом случае мы передаем текст поиска как строку и созданные ранее параметры поиска. Мы также указываем Hotel как параметр типа для метода Documents.Search, в результате чего пакет SDK будет десериализовать документы в результатах поиска к объектам типа Hotel.

Напоследок этот метод проходит все совпадения в результатах поиска, распечатывая каждый документ в консоли.

Подробнее рассмотрим вызов этого метода:

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

В первом вызове находятся все документы, содержащие термин fancy или wifi. Во втором вызове выполняется поиск текста "*", что означает "найти все". Дополнительные сведения о синтаксисе выражений запросов см. в статье [Синтаксис простых запросов в службе поиске Azure](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Второй вызов использует выражение OData $filter «category eq 'Luxury'». Это ограничивает поиск документами, в которых поле category точно совпадает со строкой Luxury. Дополнительные сведения о синтаксисе OData можно найти в статье [Синтаксис выражений OData в службе поиска Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx).

<!---HONumber=Nov15_HO3-->