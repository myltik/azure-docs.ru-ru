---
title: Использование службы поиска Azure в приложении .NET | Документация Майкрософт
description: Использование службы поиска Azure в приложении .NET
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: b50dda3847431299d7a2ffac84ecd89f3c4a586d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Использование службы поиска Azure в приложении .NET
В этой статье представлено пошаговое руководство, которое поможет вам приступить к работе с [SDK для Поиска Azure в .NET](https://aka.ms/search-sdk). Пакет SDK для .NET позволяет реализовать широкие возможности поиска в приложении с помощью службы поиска Azure.

## <a name="whats-in-the-azure-search-sdk"></a>Из чего состоит SDK для Поиска Azure
Пакет SDK включает клиентскую библиотеку, `Microsoft.Azure.Search`. Это позволяет управлять индексами, источниками данных и индексаторами, а также отправлять документы, управлять ими и выполнять запросы, не вникая в детали HTTP и JSON.

Клиентская библиотека определяет такие классы, как `Index`, `Field` и `Document`, а также такие операции, как `Indexes.Create` и `Documents.Search` в классах `SearchServiceClient` и `SearchIndexClient`. Эти классы упорядочены в следующие подразделы:

* [Microsoft.Azure.Search;](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Текущая версия пакета SDK службы поиска Azure для .NET является общедоступной. Если у вас есть предложения, как улучшить следующую версию, посетите нашу [страницу отзывов](https://feedback.azure.com/forums/263029-azure-search/).

Пакет SDK для .NET поддерживает версию `2016-09-01` [интерфейса REST API службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). Эта версия поддерживает пользовательские анализаторы, а также индексаторы больших двоичных объектов Azure и таблиц Azure. Те функции предварительной версии, которые *не* вошли в эту версию, включая поддержку индексирования файлов JSON и CSV, доступны в [предварительной версии](search-api-2016-09-01-preview.md). Их можно найти в версии [4.0.1-preview пакета SDK для .NET](https://aka.ms/search-sdk-preview).

Этот пакет SDK не поддерживает такие [операции управления](https://docs.microsoft.com/rest/api/searchmanagement/), как создание и масштабирование служб поиска или управление ключами API. Если вы хотите управлять ресурсами службы поиска из приложения .NET, можно использовать [пакет SDK управления .NET для службы поиска Azure](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Обновление до последней версии пакета SDK
Если вы уже используете старую версию пакета SDK для .NET для Поиска Azure и хотите выполнить обновление до общедоступной версии, ознакомьтесь с инструкциями в [этой статье](search-dotnet-sdk-migration.md) .

## <a name="requirements-for-the-sdk"></a>Требования для пакета SDK
1. Visual Studio 2017.
2. Ваша личная служба поиска Azure. Чтобы использовать пакет SDK, необходимо указать имя службы и один или несколько ключей API. [Создание службы на портале](search-create-service-portal.md) поможет вам это сделать.
3. Скачайте пакет SDK для Поиска Azure Search в .NET в виде [пакета NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) с помощью команды "Управление пакетами NuGet" в Visual Studio. Достаточно найти имя пакета `Microsoft.Azure.Search` на сайте NuGet.org.

Пакет SDK для .NET для службы поиска Azure поддерживает приложения, предназначенные для .NET Framework 4.6 и .NET Core.

## <a name="core-scenarios"></a>Основные сценарии
В приложении поиска необходимо выполнить несколько действий. В данном руководстве мы обсудим эти основные сценарии:

* Создание индекса
* Заполнение индекса документами
* Поиск документов с помощью полнотекстового поиска и фильтров

Следующий пример кода иллюстрирует каждый из этих сценариев. Вы можете использовать фрагменты кода в собственном приложении.

### <a name="overview"></a>Обзор
Образец приложения, который мы будем рассматривать, создает новый индекс с именем hotels, заполняет его несколькими документами, а затем выполняет некоторые поисковые запросы. Вот основная программа, показывающая общий поток.

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Полный исходный код образца приложения, используемого в этом пошаговом руководстве, см. в репозитории [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Мы рассмотрим ее шаг за шагом. Для начала необходимо создать `SearchServiceClient`. Этот объект позволяет управлять индексами. Чтобы создать его, необходимо указать имя службы поиска Azure и ключ API администратора. Эту информацию можно указать в файле `appsettings.json` из [примера приложения](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Если указать неправильный ключ (например, ключ запроса вместо ключа администратора), `SearchServiceClient` вызовет `CloudException` с сообщением об ошибке "Доступ запрещен" при первом вызове метода операции, например `Indexes.Create`. В этом случае проверьте ключ API еще раз.
> 
> 

Следующие несколько строк вызывают методы для создания индекса под названием hotels, предварительно удалив его, если он уже существует. Мы рассмотрим эти методы немного позже.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Затем необходимо заполнить индекс. Для этого нам понадобится элемент `SearchIndexClient`. Его можно получить двумя способами: создать его самостоятельно или вызвать метод `Indexes.GetClient` в `SearchServiceClient`. Для удобства мы используем второй способ.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> В типичном поисковом приложении управление индексами и заполнение обрабатываются отдельным компонентом из запросов поиска. Элемент `Indexes.GetClient` удобен для заполнения индекса, так как он избавляет от необходимости указывать другой элемент `SearchCredentials`. Это достигается благодаря передачи ключа администратора, который использовался для создания `SearchServiceClient`, в новый элемент `SearchIndexClient`. Однако в части приложения, которая выполняет запросы, лучше создать `SearchIndexClient` напрямую, чтобы передать ключ запроса вместо ключа администратора. Это согласуется с принципом наименьших прав доступа и поможет сделать приложение более безопасным. Дополнительные сведения о ключах администраторов и запросов см. [здесь](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Теперь, когда у нас есть `SearchIndexClient`, мы можем заполнить индекс. Это делает другой метод, который мы рассмотрим позже.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Наконец, мы выполним несколько поисковых запросов и отобразим результаты. На этот раз мы используем другой метод `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Метод `RunQueries` мы позже рассмотрим подробно. Это код для создания нового объекта `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

На этот раз мы воспользуемся ключом запроса, так как доступ на запись к индексу не требуется. Эту информацию можно указать в файле `appsettings.json` из [примера приложения](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Если запустить это приложение с действительными именем службы и ключами API, результат будет выглядеть так:

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
    Search the entire index for the term 'budget' and return only the hotelName field:
    
    Name: Roach Motel
    
    Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:
    
    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river
    
    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:
    
    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
    
    Search the entire index for the term 'motel':
    
    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
    
    Complete.  Press any key to end application...

Полный исходный код приложения представлен в конце этой статьи.

Далее мы подробнее рассмотрим каждый из методов, вызываемых элементом `Main`.

### <a name="creating-an-index"></a>Создание индекса
После создания `SearchServiceClient` `Main` удаляет индекс hotels, если он уже существует. Это делается следующим образом:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Этот метод использует соответствующий `SearchServiceClient` , чтобы проверить наличие индекса, и если он существует, удалить его.

> [!NOTE]
> Для простоты пример кода в этой статье использует синхронные методы пакета SDK для Поиска Azure в .NET. Рекомендуем использовать в ваших приложениях асинхронные методы, чтобы сохранить масштабируемость и скорость отклика. Например, в приведенном выше методе можно использовать `ExistsAsync` и `DeleteAsync` вместо `Exists` и `Delete`.
> 
> 

Затем `Main` создает новый индекс hotels, вызывая следующий метод:

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>()
    };

    serviceClient.Indexes.Create(definition);
}
```

Этот метод создает новый объект `Index` со списком объектов `Field`, который определяет схему нового индекса. Каждое поле имеет имя, тип данных и несколько атрибутов, которые определяют его поведение при поиске. Класс `FieldBuilder` использует отражение, чтобы создать список объектов `Field` для соответствующего индекса. Он проверяет общедоступные свойства и атрибуты полученного класса модели `Hotel`. Класс `Hotel` мы рассмотрим подробно позже.

> [!NOTE]
> При необходимости вы всегда можете создать список объектов `Field` напрямую, а не с помощью `FieldBuilder`. Это полезно, например, если вы не хотите использовать класс модели или используете существующий класс модели, в который не хотите добавлять атрибуты.
>
> 

Помимо полей, вы также можете добавить в индекс профили оценивания, средства подбора или параметры CORS (для краткости они не включены в пример). Дополнительные сведения об объекте Index и его составных частях см. в [справочнике по SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index), а также в [справочнике по REST API службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Заполнение индекса
Следующий шаг в `Main` — заполнение созданного индекса. Это делается следующим образом:

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
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

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Этот метод состоит из четырех частей. В первой части создается массив объектов `Hotel` , которые будут служить входными данными для отправки в индекс. Эти данные жестко запрограммированы для простоты. В вашем приложении данные скорее всего будут поступать из внешнего источника, например базы данных SQL.

Вторая часть создает `IndexBatch` , содержащий документы. Вы указываете операцию, которую необходимо применить к пакету во время его создания, в данном случае путем вызова метода `IndexBatch.Upload`. Затем этот пакет отправляется в индекс службы поиска Azure с помощью метода `Documents.Index` .

> [!NOTE]
> В этом примере мы просто отправляем документы. Если требуется внести изменения в существующие документы или удалить документы, можно создать пакеты, вызвав методы `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` или `IndexBatch.Delete`. Можно также смешать различные операции в одном пакете, вызвав метод `IndexBatch.New`, принимающий коллекцию объектов `IndexAction`, каждый из которых указывает Поиску Azure выполнять определенную операцию с документом. Можно создать каждый `IndexAction` с отдельной операцией, вызвав соответствующий метод, например `IndexAction.Merge`, `IndexAction.Upload` и т. д.
> 
> 

Третья часть метода — это блок catch, который обрабатывает важные ошибки индексирования. Если службе поиска Azure не удается индексировать некоторые документы в пакете, `Documents.Index` вызывает `IndexBatchException`. Это может произойти, если вы индексируете документы службы при интенсивной нагрузке. **Настоятельно рекомендуется явно обрабатывать этот случай в коде.** Вы можете задержать и повторить попытку индексирования соответствующих документов либо занести ошибку в журнал и продолжить работу, как в нашем примере, а также выполнить другие действия в зависимости от требований вашего приложения к целостности данных.

> [!NOTE]
> С помощью метода `FindFailedActionsToRetry` вы можете создать новый пакет, содержащий только те действия, которые не удалось выполнить в предыдущем вызове метода `Index`. Этот метод описан [здесь](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_), а [на сайте StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) опубликовано обсуждение правил его использования.
>
>

И наконец, метод `UploadDocuments` вызывает задержку на две секунды. Индексирование в службе поиска Azure происходит асинхронно, поэтому образец приложения должен подождать немного, пока документы не станут доступными для поиска. Такие задержки обычно необходимы только в демонстрациях, тестах и примерах приложений.

#### <a name="how-the-net-sdk-handles-documents"></a>Обработка документов пакетом SDK для .NET
Вас может интересовать, как пакет SDK для Поиска Azure в .NETможет передавать экземпляры пользовательского класса, например `Hotel` , в индекс. Чтобы ответить на этот вопрос, рассмотрим класс `Hotel` :

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Прежде всего следует отметить, что каждое общедоступное свойство `Hotel` соответствует полю в определении индекса, но с одним ключевым отличием: имя каждого поля начинается со строчной буквы (нижнего регистра), в то время как имя каждого общего свойства `Hotel` начинается с прописной буквы (верхнего регистра). Это часто встречается в приложениях .NET, выполняющих привязку данных, если целевая схема не подлежит управлению разработчика приложения. Чтобы не нарушать правил присвоения имен .NET, указывая имена свойств в нижнем регистре, вы можете сделать так, чтобы пакет SDK автоматически сопоставлял имена свойств в нижнем регистре, с помощью атрибута `[SerializePropertyNamesAsCamelCase]` .

> [!NOTE]
> Пакет SDK службы поиска Azure для .NET использует библиотеку [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) для сериализации и десериализации настраиваемых объектов модели в JSON и обратно. При необходимости эту сериализацию можно настроить. Дополнительные сведения см. в разделе [Пользовательская сериализация с помощью JSON.NET](#JsonDotNet).
> 
> 

Второе, на что нужно обратить внимание, — это атрибуты, дополняющие каждое общедоступное свойство, например `IsFilterable`, `IsSearchable`, `Key` и `Analyzer`. Эти атрибуты сопоставляются непосредственно с [аналогичными атрибутами индекса в службе поиска Azure](https://docs.microsoft.com/rest/api/searchservice/create-index#request). Класс `FieldBuilder` на их основе создает определения полей для индекса.

Третий важный аспект класса `Hotel` — это типы данных общедоступных свойств. Типы .NET этих свойств сопоставляются с эквивалентными типами полей в определении индекса. Например, свойство строки `Category` сопоставляется с полем `category`, которое имеет тип `Edm.String`. Аналогичные сопоставления присутствуют между типами `bool?` и `Edm.Boolean`, `DateTimeOffset?` и `Edm.DateTimeOffset` и т. д. Конкретные правила сопоставления типов указаны в описании метода `Documents.Get` в [справочнике по SDK для .NET службы поиска Azure](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_). Хотя класс `FieldBuilder` выполняет это сопоставление, этот механизм полезно понимать на случай, если вам понадобится устранять неполадки, связанные с сериализацией.

Возможность использовать собственные классы как документы работает и в обратную сторону. Вы также можете получать результаты поиска и поручить пакету SDK автоматическую десериализацию к выбранному типу, как показано в следующем разделе.

> [!NOTE]
> Пакет SDK для Поиска Azure в .NET также поддерживает документы динамических типов, поддерживающие класс `Document`, то есть сопоставление ключей и значений с именами и значениями полей. Это полезно в тех случаях, когда во время разработки вам неизвестна схема индекса или привязка к конкретным классам моделей нецелесообразна. Все методы пакета SDK, связанные с документами, имеют перегрузки, работающие с классом `Document`, а также строго типизированные перегрузки, принимающие параметр универсального типа. Пример кода в данном руководстве содержит только перегрузки второго типа. Класс `Document` наследует от `Dictionary<string, object>`. Дополнительные сведения см. [здесь](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Почему следует использовать типы данных, допускающие значение NULL**

При разработке собственных классов модели для сопоставления с индексом поиска Azure рекомендуется объявлять свойства типов значений, такие как `bool` и `int`, допускающих нулевое значение (например: `bool?` вместо `bool`). При использовании ненулевого свойства необходимо **гарантировать** , что документы в индексе не будут содержать значение NULL для соответствующего поля. Ни пакет SDK, ни служба поиска Azure не помогут вам это обеспечить.

Это не просто гипотетическое соображение. Представьте себе ситуацию, когда вы добавляете новое поле в существующий индекс с типом `Edm.Int32`. После обновления определения индекса все документы будут иметь значение null для этого нового поля (поскольку все типы допускают значение NULL в службе поиска Azure). Если затем для этого поля вы используете класс модели со свойством `int`, не допускающим нулевое значение, при попытке получения документов вы получите `JsonSerializationException` следующим образом:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

По этой причине по-прежнему рекомендуется использовать типы, допускающие значения NULL, в классах модели.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Пользовательская сериализация с помощью JSON.NET
Пакет SDK использует JSON.NET для сериализации и десериализации документов. В случае необходимости можно настроить сериализацию и десериализацию, определив собственные элементы `JsonConverter` или `IContractResolver`. Дополнительные сведения см. в [документации по JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm). Это может быть полезно, если нужно адаптировать существующий класс модели из приложения для использования с поиском Azure и других более сложных сценариев. Например, с помощью пользовательской сериализации можно делать следующее.

* Включить или исключить определенные свойства класса модели с сохранением в виде поля документа.
* Реализовать сопоставление между именами свойств в коде и именами полей в индексе.
* Создать настраиваемые атрибуты, которые можно использовать для сопоставления свойств с полями документов.

Примеры реализации пользовательской сериализации можно найти в модульных тестах к пакету SDK для .NET для поиска Azure на GitHub. Хорошей отправной точкой будет [эта папка](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Она содержит классы, используемые в тестах пользовательской сериализации.

### <a name="searching-for-documents-in-the-index"></a>Поиск документов в индексе
Последний шаг в примере приложения — поиск некоторых документов в индексе. Приведенный ниже метод делает следующее:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "hotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("budget", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "lastRenovationDate desc" },
            Select = new[] { "hotelName", "lastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'motel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);
}
```

При каждом выполнении запроса этот метод прежде всего создает объект `SearchParameters`. Это позволяет задать дополнительные параметры запроса, например сортировку, фильтрацию, разбиение на страницы и фасетизацию. В этом методе мы задаем свойства `Filter`, `Select`, `OrderBy` и `Top` для разных запросов. Все свойства `SearchParameters` описаны [здесь](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Следующий этап — фактическое выполнение поискового запроса. Это делается с помощью метода `Documents.Search` . Для каждого запроса мы передаем текст для поиска в строковом формате (или `"*"`, если текст для поиска отсутствует) и созданные ранее параметры поиска. Мы также указываем `Hotel` как параметр типа `Documents.Search`, в результате чего пакет SDK будет десериализовать документы в результатах поиска к объектам типа `Hotel`.

> [!NOTE]
> Дополнительные сведения о синтаксисе выражений запросов см. [здесь](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

А теперь, после завершения каждого запроса (поиска совпадений в результатах поиска), этот метод последовательно выводит в консоль все удовлетворяющие условиям документы.

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Давайте подробнее рассмотрим каждый из запросов. Ниже приведен код для выполнения первого запроса:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

В этом примере мы ищем гостиницы, отвечающие запросу со словом budget, и выводим только имена этих гостиниц, как определено параметром `Select`. Вот результат выполнения:

    Name: Roach Motel

Теперь нам нужно найти гостиницы с ценой ниже 150 долларов США за ночь, а затем получить идентификаторы и описания этих гостиниц:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Этот запрос использует выражение OData `$filter` (по значению `baseRate lt 150`), чтобы отфильтровать в индексе нужные документы. Дополнительные сведения о синтаксисе OData, который поддерживает служба поиска Azure, см. [здесь](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Вот результат выполнения этого запроса:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

Теперь нам нужно найти две гостиницы с самым свежим ремонтом и отобразить для них название и дату ремонта. Ниже приведен код: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

В этом случае мы снова используем синтаксис OData, присвоив параметру `OrderBy` значение `lastRenovationDate desc`. Мы также указываем значение 2 для параметра `Top`, чтобы получить только два первых документа. Как и ранее, параметр `Select` содержит поля, которые мы хотим получить.

Вот результат выполнения:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

В последнем примере мы найдем все гостиницы, отвечающие запросу со словом motel:

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Ниже представлены результаты этого запроса. Они включают все поля, так как мы не указали свойство `Select`:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

На этом руководство заканчивается, но вам не стоит останавливаться. **Дальнейшие действия** представлены дополнительные материалы о Поиске Azure.

## <a name="next-steps"></a>Дополнительная информация
* Изучите справочную информацию о [пакете SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) и [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Дополнительные сведения можно получить из [видео, а также других примеров и руководств](search-video-demo-tutorial-list.md).
* Изучите [соглашения о наименовании](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) , чтобы узнать правила именования различных объектов.
* Изучите [поддерживаемые типы данных](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) в службе поиска Azure.
