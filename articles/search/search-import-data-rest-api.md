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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Импорт данных в службу поиска Azure с помощью REST API
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

В этой статье показано, как заполнить индекс с помощью [REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Часть содержимого ниже взята из статьи [Добавление, обновление или удаление документов](REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx). Дополнительные сведения см. в родительской статье.

Для импорта необходимо наличие индекса, который может принимать данные.

При использовании интерфейса REST API прием данных выполняется на основе HTTP-запроса Put или Post. Фрагменты кода взяты из [примера профилей повышения](search-get-started-scoring-profiles.md).

        static bool PostDocuments(string fileName)
        {
            // Add some documents to the newly created index
            Uri requestUri = new Uri(serviceUrl + indexName + "/docs/index?" + ApiVersion);

            // Load the json containing the data from an external file
            string json = File.ReadAllText(fileName);

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PostAsync(requestUri,       // To add data use POST
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.OK)
                {
                    Console.WriteLine("Documents posted from file {0}. \r\n", fileName);
                    return true;
                }

                Console.WriteLine("Documents failed to upload: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->