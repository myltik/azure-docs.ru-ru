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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Создание индекса службы поиска Azure с помощью REST API
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

В этой статье мы расскажем, как создать индекс с помощью [REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Часть содержимого ниже взята из статьи [Создание индекса (API REST службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Дополнительные сведения см. в родительской статье.

Для создания индекса необходимо подключение к службе поиска. Обычно оно устанавливается с помощью httpClient.

Создание индекса с помощью интерфейса REST API состоит из трех этапов: подключение, предоставление схемы индекса и выполнение команды, которая создает индекс. Фрагменты кода взяты из [примера профилей повышения](search-get-started-scoring-profiles.md).

##Определение схемы индекса

Интерфейс REST API принимает запросы в формате JSON. Одним из способов создания схемы является включение отдельного файла схемы как документа JSON. Иллюстрация приведена в примере ниже.

В примере, из которого взят этот фрагмент, JSON-код сохраняется в файле с именем schema.json.

	{
	    "name": "musicstoreindex",
	    "fields": [
	        { "name": "key", "type": "Edm.String", "key": true },
	        { "name": "albumTitle", "type": "Edm.String"},
	        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	        { "name": "genre", "type": "Edm.String" },
	        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	        { "name": "artistName", "type": "Edm.String"},
	        { "name": "orderableOnline", "type": "Edm.Boolean" },
	        { "name": "rating", "type": "Edm.Int32" },
	        { "name": "tags", "type": "Collection(Edm.String)" },
	        { "name": "price", "type": "Edm.Double", "filterable": false },
	        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	        { "name": "inventory", "type": "Edm.Int32" },
	        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	    ],
	    "scoringProfiles": [
	        {
	            "name": "boostGenre",
	            "text": {
	                "weights": {
	                    "albumTitle": 5,
	                    "genre": 50,
	                    "artistName": 5
	                }
	            }
	        },
	        {
	            "name": "newAndHighlyRated",
	            "functions": [
	                {
	                    "type": "freshness",
	                    "fieldName": "lastUpdated",
	                    "boost": 10,
	                    "interpolation": "quadratic",
	                    "freshness": {
	                        "boostingDuration": "P365D"
	                    }
	                },
	                {
	                    "type": "magnitude",
	                    "fieldName": "rating",
	                    "boost": 10,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 1,
	                        "boostingRangeEnd": 5,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        },
	        {
	            "name": "boostMargin",
	            "functions": [
	
	                {
	                    "type": "magnitude",
	                    "fieldName": "margin",
	                    "boost": 50,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 50,
	                        "boostingRangeEnd": 100,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        }
	    ]
	}

##Настройка HTTP-запроса для подключения и создания индекса

Интерфейсы REST API создают HTTP-подключение при каждом запросе. Каждый запрос всегда определяет URL-адрес службы, используемую версию API и ключ администратора для доступа к операциям чтения и записи в службе (ниже он называется первичным ключом — так же, как и на портале).

Фрагмент кода использует в качестве входных данных статические значения, в частности имя индекса, указанное в файле app.config (не показано). Пример создан именно так, чтобы упростить код.

        private const string ApiVersion = "api-version=2015-02-28";
        private static string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];
        private static string indexName = ConfigurationManager.AppSettings["indexName"];
        private static string primaryKey = ConfigurationManager.AppSettings["primaryKey"];

В следующем примере показан HTTP-запрос PUT к URL-адресу службы, а также версия `api-version` и имя для создаваемого индекса.

        static bool CreateIndex()
        {
            // Create an index using an index name
            Uri requestUri = new Uri(serviceUrl + indexName + "?" + ApiVersion);

            // Load the json containing the schema from an external file
            string json = File.ReadAllText("schema.json");

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PutAsync(requestUri,        // To create index use PUT
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.Created)   // For Posts we want to know response had no content
                {
                    Console.WriteLine("Index created. \r\n");
                    return true;
                }

                Console.WriteLine("Index creation failed: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->