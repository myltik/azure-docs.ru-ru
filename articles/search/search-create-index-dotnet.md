<properties
	pageTitle="Создание индекса для службы поиска Azure с помощью .NET | Microsoft Azure | Размещенная облачная служба поиска"
	description="Индекс можно создать в коде с помощью пакета SDK для .NET или библиотеки службы поиска Azure."
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
	ms.date="02/18/2016"
	ms.author="heidist"/>

# Создание индекса для службы поиска Azure с помощью .NET
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

В этой статье мы расскажем, как создать индекс с помощью [пакета SDK .NET для службы поиска Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). Содержимое ниже взято из статьи [Использование службы поиска Azure в приложении .NET](search-howto-dotnet-sdk.md). Пошаговые инструкции см. в родительской статье.

Для создания индекса необходимо подключение к службе поиска. Обычно оно устанавливается с помощью `SearchServiceClient`. Чтобы обеспечить повторное развертывание без конфликтов, рекомендуется удалить существующий индекс с тем же именем, если он уже существует.

Допустим, у вас есть индекс hotels. Создайте следующий метод.

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Этот метод использует соответствующий `SearchServiceClient`, чтобы проверить наличие индекса, и если он существует, удалить его.

Чтобы создать новый индекс hotels, создайте метод, аналогичный следующему:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Этот метод создает новый объект `Index` со списком объектов `Field`, который определяет схему нового индекса. Каждое поле имеет имя, тип данных и несколько атрибутов, которые определяют его поведение при поиске. Помимо полей, вы также можете добавить в индекс профили оценивания, средства подбора или параметры CORS (для краткости они не включены в пример). Дополнительные сведения об объекте Index и его составных частях см. в справочнике по SDK в библиотеке [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), а также в [справочнике по API REST Поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

<!---HONumber=AcomDC_0224_2016-->