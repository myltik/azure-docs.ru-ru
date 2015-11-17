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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Создание запросов в службе поиска Azure с помощью вызовов REST 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

В этой статье показано, как создать запрос к индексу с помощью [REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Часть содержимого ниже взята из статьи [Поиск документов (REST API службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Дополнительные сведения см. в родительской статье.

Для импорта необходимо наличие индекса, в который загружены документы с данными для поиска.

При использовании интерфейса REST API запросы строятся на основе HTTP-запроса GET. Фрагменты кода взяты из [примера профилей повышения](search-get-started-scoring-profiles.md).

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->