<properties
	pageTitle="Импорт данных в службу поиска Azure с помощью .NET | Microsoft Azure | Размещенная облачная служба поиска"
	description="Отправка данных в индекс службы поиска Azure с помощью пакета SDK для .NET или библиотеки .NET."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Импорт данных в службу поиска Azure с помощью .NET
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

В этой статье показано, как заполнить индекс с помощью [пакета SDK для .NET службы поиска Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). Содержимое ниже взято из статьи [Использование службы поиска Azure в приложении .NET](search-howto-dotnet-sdk.md). Пошаговые инструкции см. в родительской статье.

Для импорта данных необходимо наличие ранее созданного индекса.

Допустим, у вас есть индекс hotels. Вы можете создать приведенный ниже метод для импорта данных.

Следующий шаг в `Main` — заполнение созданного индекса. Это делается следующим образом:

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(sitecoreItems);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Этот метод состоит из четырех частей. В первой части создается массив объектов `Hotel`, которые будут служить входными данными для отправки в индекс. Эти данные жестко запрограммированы для простоты. В вашем приложении данные скорее всего будут поступать из внешнего источника, например базы данных SQL.

Вторая часть создает `IndexAction` для каждого элемента `Hotel`, а затем группирует их в новый элемент `IndexBatch`. Затем этот пакет отправляется в индекс службы поиска Azure с помощью метода `Documents.Index`.

> [AZURE.NOTE] В этом примере мы просто отправляем документы. Чтобы изменить или удалить существующий документ, вы можете использовать метод `Merge`, `MergeOrUpload` или `Delete` соответственно.

Третья часть метода — это блок catch, который обрабатывает важные ошибки индексирования. Если службе поиска Azure не удается индексировать некоторые документы в пакете, `Documents.Index` вызывает `IndexBatchException`. Это может произойти, если вы индексируете документы службы при интенсивной нагрузке. **Настоятельно рекомендуется явно обрабатывать этот случай в коде.** Вы можете задержать и повторить попытку индексирования  
соответствующих документов либо занести ошибку в журнал и продолжить работу, как в нашем примере, а также выполнить другие действия в зависимости от требований вашего приложения к целостности данных.

Затем метод выполняет задержку на две секунды. Индексирование в службе поиска Azure происходит асинхронно, поэтому образец приложения должен подождать немного, пока документы не станут доступными для поиска. Такие задержки обычно необходимы только в демонстрациях, тестах и примерах приложений.

<!---HONumber=AcomDC_0224_2016-->