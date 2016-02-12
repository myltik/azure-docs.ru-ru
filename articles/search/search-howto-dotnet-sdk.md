<properties
   pageTitle="Использование Поиска Azure в приложении .NET | Microsoft Azure | Размещенная облачная служба поиска"
   description="Использование Поиска Azure в приложении .NET"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="01/29/2016"
   ms.author="brjohnst"/>

# Использование службы поиска Azure в приложении .NET

В этой статье представлено пошаговое руководство, которое поможет вам приступить к работе с [SDK для Поиска Azure в .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx). Пакет SDK для .NET позволяет реализовать широкие возможности поиска в приложении с помощью службы поиска Azure.

## Из чего состоит SDK для Поиска Azure

Пакет SDK включает клиентскую библиотеку, `Microsoft.Azure.Search`. Это позволяет управлять индексами, источниками данных и индексаторами, а также отправлять документы, управлять ими и выполнять запросы, не вникая в детали HTTP и JSON.

Клиентская библиотека определяет такие классы, как `Index`, `Field` и `Document`, а также такие операции, как `Indexes.Create` и `Documents.Search` в классах `SearchServiceClient` и `SearchIndexClient`. Эти классы упорядочены в следующие подразделы:

- [Microsoft.Azure.Search;](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Текущая версия пакета SDK для .NET службы поиска Azure является предварительной. Если у вас есть предложения, как улучшить первую стабильную версию, посетите нашу [страницу отзывов](https://feedback.azure.com/forums/263029-azure-search/).

Пакет SDK для .NET поддерживает версию `2015-02-28` интерфейса API REST Поиска Azure, описанную в библиотеке [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx). Теперь эта версия включает поддержку анализаторов языка Microsoft. Новые возможности, которые *не* включены в эту версию, например поддержка параметра поиска `moreLikeThis`, находятся в [предварительной версии](search-api-2015-02-28-preview.md) и недоступны в пакете SDK. Об изменениях состояния каждой из этих функций можно узнать на странице [Управление версиями службы поиска](https://msdn.microsoft.com/library/azure/dn864560.aspx) или [Последние обновления службы поиска Azure](search-latest-updates.md).

Другие функции, которые не поддерживается в этом пакете SDK:

  - [Операции управления](https://msdn.microsoft.com/library/azure/dn832684.aspx). Операции управления включают подготовку службы поиска Azure и управление ключами API. В будущем их поддержка будет добавлена в отдельный пакет SDK для управления Поиском Azure Search в .NET.

## Обновление до последней версии пакета SDK

Если вы уже используете старую версию пакета SDK для .NET для службы поиска Azure и хотите выполнить обновление до новой версии 1.0-preview, см. инструкции в [этой статье](search-dotnet-sdk-migration.md).

## Требования для пакета SDK

1. Visual Studio 2013 или Visual Studio 2015.

2. Ваша личная служба поиска Azure. Чтобы использовать пакет SDK, необходимо указать имя службы и один или несколько ключей API. Статья [Создание службы на портале](search-create-service-portal.md) поможет вам это сделать.

3. Скачайте пакет SDK для Поиска Azure Search в .NET в виде [пакета NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) с помощью команды "Управление пакетами NuGet" в Visual Studio. Достаточно найти имя пакета `Microsoft.Azure.Search` на сайте NuGet.org. Обязательно установите флажок "Включить предварительные выпуски", чтобы предварительная версия пакета SDK отображалась в результатах поиска.

Пакет SDK для .NET службы поиска Azure поддерживает приложения, предназначенные для .NET Framework 4.5, а также приложения из Магазина Windows для Windows 8.1 и Windows Phone 8.1. Silverlight не поддерживается.

## Основные сценарии

В приложении поиска необходимо выполнить несколько действий. В данном руководстве мы обсудим эти основные сценарии:

- Создание индекса
- Заполнение индекса документами
- Поиск документов с помощью полнотекстового поиска и фильтров

Следующий пример кода иллюстрирует каждый из этих сценариев. Вы можете использовать фрагменты кода в собственном приложении.

### Обзор

Образец приложения, который мы будем рассматривать, создает новый индекс с именем hotels, заполняет его несколькими документами, а затем выполняет некоторые поисковые запросы. Вот основная программа, показывающая общий поток.

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

Мы рассмотрим ее шаг за шагом. Для начала необходимо создать `SearchServiceClient`. Этот объект позволяет управлять индексами. Чтобы создать его, необходимо указать имя службы поиска Azure и ключ API администратора.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE] Если указать неправильный ключ (например, ключ запроса вместо ключа администратора), `SearchServiceClient` вызовет `CloudException` с сообщением об ошибке "Доступ запрещен" при первом вызове метода операции, например `Indexes.Create`. В этом случае проверьте ключ API еще раз.

Следующие несколько строк вызывают методы для создания индекса под названием hotels, предварительно удалив его, если он уже существует. Мы рассмотрим эти методы немного позже.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

Затем необходимо заполнить индекс. Для этого нам понадобится элемент `SearchIndexClient`. Его можно получить двумя способами: создать его самостоятельно или вызвать метод `Indexes.GetClient` в `SearchServiceClient`. Для удобства мы используем второй способ.

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE] В типичном приложении поиска управление индексами и их заполнение выполняются отдельным компонентом, а не поисковыми запросами. Метод `Indexes.GetClient` удобно использовать для заполнения индекса, так как он избавляет от необходимости дополнительно указывать `SearchCredentials`. Это достигается благодаря передачи ключа администратора, который использовался для создания `SearchServiceClient`, в новый элемент `SearchIndexClient`. Однако в части приложения, которая выполняет запросы, лучше создать `SearchIndexClient` напрямую, чтобы передать ключ запроса вместо ключа администратора. Это согласуется с принципом наименьших прав доступа и поможет сделать приложение более безопасным. Дополнительные сведения о ключах администраторов и запросов см. [здесь](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Теперь, когда у нас есть `SearchIndexClient`, мы можем заполнить индекс. Это делает другой метод, который мы рассмотрим позже.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Наконец, мы выполним несколько поисковых запросов и выведем результаты, снова используя `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Если запустить это приложение с действительными именем службы и ключом API, результат должен иметь следующий вид:

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury	Tags: []
    Complete.  Press any key to end application...

Полный исходный код приложения представлен в конце этой статьи.

Далее мы подробнее рассмотрим каждый из методов, вызываемых элементом `Main`.

### Создание индекса

После создания `SearchServiceClient` `Main` удаляет индекс hotels, если он уже существует. Это делается следующим образом:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Этот метод использует соответствующий `SearchServiceClient`, чтобы проверить наличие индекса, и если он существует, удалить его.

> [AZURE.NOTE] Для простоты пример кода в этой статье использует синхронные методы пакета SDK для Поиска Azure в .NET. Рекомендуем использовать в ваших приложениях асинхронные методы, чтобы сохранить масштабируемость и скорость отклика. Например, в приведенном выше методе можно использовать `ExistsAsync` и `DeleteAsync` вместо `Exists` и `Delete`.

Затем `Main` создает новый индекс hotels, вызывая следующий метод:

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

### Заполнение индекса

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
            var batch = IndexBatch.Upload(documents);
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

Вторая часть создает `IndexBatch`, содержащий документы. Вы указываете операцию, которую необходимо применить к пакету во время его создания, в данном случае путем вызова метода `IndexBatch.Upload`. Затем этот пакет отправляется в индекс службы поиска Azure с помощью метода `Documents.Index`.

> [AZURE.NOTE] В этом примере мы просто отправляем документы. Если требуется внести изменения в существующие документы или удалить документы, можно создать пакеты, вызвав методы `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` или `IndexBatch.Delete`. Можно также смешать различные операции в одном пакете, вызвав метод `IndexBatch.New`, принимающий коллекцию объектов `IndexAction`, каждый из которых указывает службе поиска Azure выполнять определенную операцию с документом. Можно создать каждый `IndexAction` с отдельной операцией, вызвав соответствующий метод, например `IndexAction.Merge`, `IndexAction.Upload` и т. д.

Третья часть метода — это блок catch, который обрабатывает важные ошибки индексирования. Если службе поиска Azure не удается индексировать некоторые документы в пакете, `Documents.Index` вызывает `IndexBatchException`. Это может произойти, если вы индексируете документы службы при интенсивной нагрузке. **Настоятельно рекомендуется явно обрабатывать этот случай в коде.** Вы можете задержать и повторить попытку индексирования  
соответствующих документов либо занести ошибку в журнал и продолжить работу, как в нашем примере, а также выполнить другие действия в зависимости от требований вашего приложения к целостности данных.

Затем метод выполняет задержку на две секунды. Индексирование в службе поиска Azure происходит асинхронно, поэтому образец приложения должен подождать немного, пока документы не станут доступными для поиска. Такие задержки обычно необходимы только в демонстрациях, тестах и примерах приложений.

#### Обработка документов пакетом SDK для .NET

Вас может интересовать, как пакет SDK для Поиска Azure в .NETможет передавать экземпляры пользовательского класса, например `Hotel`, в индекс. Чтобы ответить на этот вопрос, рассмотрим класс `Hotel`:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

Прежде всего следует отметить, что каждое общедоступное свойство `Hotel` соответствует полю в определении индекса, но с одним ключевым отличием: имя каждого поля начинается со строчной буквы (нижнего регистра), в то время как имя каждого общего свойства `Hotel` начинается с прописной буквы (верхнего регистра). Это часто встречается в приложениях .NET, выполняющих привязку данных, если целевая схема не подлежит управлению разработчика приложения. Чтобы не нарушать правил присвоения имен .NET, указывая имена свойств в нижнем регистре, вы можете сделать так, чтобы пакет SDK автоматически сопоставлял имена свойств в нижнем регистре, с помощью атрибута `[SerializePropertyNamesAsCamelCase]`.

> [AZURE.NOTE] Пакет SDK для .NET службы поиска Azure использует библиотеку [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) для сериализации и десериализации настраиваемых объектов модели в JSON и из JSON. При необходимости эту сериализацию можно настроить. Дополнительные сведения см. [здесь](search-dotnet-sdk-migration.md#WhatsNew).

Вторая важная составляющая класса `Hotel` — типы данных общих свойств. Типы .NET этих свойств сопоставляются с эквивалентными типами полей в определении индекса. Например, свойство строки `Category` сопоставляется с полем `category`, которое имеет тип `Edm.String`. Аналогичные сопоставления присутствуют между типами `bool?` и `Edm.Boolean`, `DateTimeOffset?` и `Edm.DateTimeOffset` и т. д. Конкретные правила сопоставления типов указаны в описании метода `Documents.Get` в библиотеке [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Возможность использовать собственные классы как документы работает и в обратную сторону. Вы также можете получать результаты поиска и поручить пакету SDK автоматическую десериализацию к выбранному типу, как показано в следующем разделе.

> [AZURE.NOTE] Пакет SDK для Поиска Azure в .NET также поддерживает документы динамических типов, поддерживающие класс `Document`, то есть сопоставление ключей и значений с именами и значениями полей. Это полезно в тех случаях, когда во время разработки вам неизвестна схема индекса или привязка к конкретным классам моделей нецелесообразна. Все методы пакета SDK, связанные с документами, имеют перегрузки, работающие с классом `Document`, а также строго типизированные перегрузки, принимающие параметр универсального типа. Пример кода в данном руководстве содержит только перегрузки второго типа. Дополнительные сведения о классе `Document` см. [здесь](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Важное примечание о типах данных**

При разработке собственных классов модели для сопоставления с индексом поиска Azure рекомендуется объявлять свойства типов значений, такие как `bool` и `int`, допускающими нулевое значение (например: `bool?` вместо `bool`). При использовании ненулевого свойства необходимо **гарантировать**, что документы в индексе не будут содержать значение NULL для соответствующего поля. Ни пакет SDK, ни служба поиска Azure не помогут вам это обеспечить.

Это не просто гипотетическое соображение. Представьте себе ситуацию, когда вы добавляете новое поле в существующий индекс, тип которого `Edm.Int32`. После обновления определения индекса все документы будут иметь значение null для этого нового поля (поскольку все типы допускают значение NULL в службе поиска Azure). Если затем для этого поля вы используете класс модели со свойством `int`, не допускающим нулевое значение, при попытке получения документов вы получите `JsonSerializationException` следующим образом:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

По этой причине по-прежнему рекомендуется использовать типы, допускающие значения NULL, в классах модели.

### Поиск документов в индексе

Последний шаг в примере приложения — поиск некоторых документов в индексе. Приведенный ниже метод делает следующее:

    private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

Для начала этот метод создает объект `SearchParameters`. Это позволяет задать дополнительные параметры запроса, например сортировку, фильтрацию, разбиение на страницы и фасетизацию. В этом примере задается только свойство `Filter`.

Следующий этап — фактическое выполнение поискового запроса. Это делается с помощью метода `Documents.Search`. В этом случае мы передаем текст поиска как строку и созданные ранее параметры поиска. Мы также указываем `Hotel` как параметр типа `Documents.Search`, в результате чего пакет SDK будет десериализовать документы в результатах поиска к объектам типа `Hotel`.

Напоследок этот метод проходит все совпадения в результатах поиска, распечатывая каждый документ в консоли.

Подробнее рассмотрим вызов этого метода:

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

В первом вызове находятся все документы, содержащие термин fancy или wifi. Во втором вызове выполняется поиск текста "*", что означает "найти все". Дополнительные сведения о синтаксисе выражений запросов см. [здесь](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Второй вызов использует выражение OData `$filter`, `category eq 'Luxury'`. Он ограничивает поиск документами, в которых поле `category` точно совпадает со строкой "Luxury". Дополнительные сведения о синтаксисе OData, поддерживаемом службой поиска Azure, см. [здесь](https://msdn.microsoft.com/library/azure/dn798921.aspx).

Теперь, когда вы знаете, что делают эти два вызова, вам будет понятнее, почему их вывод имеет следующий вид:

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury	Tags: []

Первый поиск возвращает два документа. Первый содержит в имени строку "Fancy", а второй — строку "wifi" в поле `tags`. Второй поиск возвращает единственные два документа в индексе, для которых поле `category` имеет значение "Luxury".

На этом руководство заканчивается, но вам не стоит останавливаться. В разделе **Дальнейшие действия** представлены дополнительные материалы о Поиске Azure.

## Дальнейшие действия

- Найдите ссылки для [пакета SDK для .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) и [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) на сайте MSDN.
- Дополнительные сведения можно получить из [видео, а также других примеров и руководств](search-video-demo-tutorial-list.md).
- Сведения о функциях и возможностях в данной версии пакета SDK для Поиска Azure: [Обзор Поиска Azure](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- Изучите [соглашения о наименовании](https://msdn.microsoft.com/library/azure/dn857353.aspx), чтобы узнать правила именования различных объектов.
- Изучите [поддерживаемые типы данных](https://msdn.microsoft.com/library/azure/dn798938.aspx) в службе поиска Azure.


## Исходный код образца приложения

Ниже приведен полный исходный код образца приложения, используемого в данном пошаговом руководстве. Обратите внимание, что для сборки и запуска примера необходимо заменить имя службы и ключ API в файле Program.cs на собственные значения.

Program.cs:

    using System;
    using System.Configuration;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.SDKHowTo
    {
        class Program
        {
            // This sample shows how to delete, create, upload documents and query an index
            static void Main(string[] args)
            {
                // Put your search service name here. This is the hostname portion of your service URL.
                // For example, if your service URL is https://myservice.search.windows.net, then your
                // service name is myservice.
                string searchServiceName = "myservice";

                string apiKey = "Put your API admin key here."

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteHotelsIndexIfExists(serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateHotelsIndex(serviceClient);

                SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

                Console.WriteLine("{0}", "Uploading documents...\n");
                UploadDocuments(indexClient);

                Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
                SearchDocuments(indexClient, searchText: "fancy wifi");

                Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
                SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists("hotels"))
                {
                    serviceClient.Indexes.Delete("hotels");
                }
            }

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
                    var batch = IndexBatch.Upload(documents);
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

            private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
            {
                // Execute search based on search text and optional filter
                var sp = new SearchParameters();

                if (!String.IsNullOrEmpty(filter))
                {
                    sp.Filter = filter;
                }

                DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
                foreach (SearchResult<Hotel> result in response.Results)
                {
                    Console.WriteLine(result.Document);
                }
            }
        }
    }

Hotel.cs:

    using System;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.SDKHowTo
    {
        [SerializePropertyNamesAsCamelCase]
        public class Hotel
        {
            public string HotelId { get; set; }

            public string HotelName { get; set; }

            public double? BaseRate { get; set; }

            public string Category { get; set; }

            public string[] Tags { get; set; }

            public bool? ParkingIncluded { get; set; }

            public DateTimeOffset? LastRenovationDate { get; set; }

            public int? Rating { get; set; }

            public GeographyPoint Location { get; set; }

            public override string ToString()
            {
                return String.Format(
                    "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                    HotelId,
                    HotelName,
                    Category,
                    (Tags != null) ? String.Join(", ", Tags) : String.Empty);
            }
        }
    }

Полный образец исходного кода также можно найти [на GitHub](http://aka.ms/search-dotnet-howto).

<!---HONumber=AcomDC_0204_2016-->