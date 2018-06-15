---
title: Отправка данных в службу поиска Azure c помощью .NET | Документация Майкрософт
description: Сведения о том, как отправлять данные в индекс службы поиска Azure с помощью пакета SDK .NET.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/13/2017
ms.openlocfilehash: fb8fb153c7393af0e084cb96ee2f5e82fba5b220
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798059"
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Отправка данных в службу поиска Azure с помощью пакета SDK для .NET
> [!div class="op_single_selector"]
> * [Обзор](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Из этой статьи вы узнаете, как импортировать данные в индекс службы поиска Azure с помощью пакета [SDK .NET для службы поиска Azure](https://aka.ms/search-sdk) .

Прежде чем приступать к выполнению инструкций из этого руководства, вам нужно [создать индекс службы поиска Azure](search-what-is-an-index.md). В статье также предполагается, что вы уже создали объект `SearchServiceClient` , как описано в статье [Создание индекса для службы поиска Azure с помощью .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

> [!NOTE]
> Все приведенные здесь примеры кода написаны на языке C#. Полный исходный код можно найти на сайте [GitHub](http://aka.ms/search-dotnet-howto). Подробные примеры использования кода см. в сведениях о [пакете SDK .NET для службы поиска Azure](search-howto-dotnet-sdk.md).

Чтобы отправить документы в индекс с помощью пакета SDK для .NET, сделайте вот что.

1. Создайте объект `SearchIndexClient` , который будет подключен к индексу поиска.
2. Создайте класс `IndexBatch`, содержащий документы, которые должны быть добавлены, изменены или удалены.
3. Вызовите метод `Documents.Index` в объекте `SearchIndexClient`, чтобы отправить `IndexBatch` в индекс поиска.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Создание экземпляра класса SearchIndexClient
Чтобы импортировать данные в индекс с помощью пакета SDK .NET для службы поиска Azure, нужно создать экземпляр класса `SearchIndexClient` . Этот экземпляр можно создать самостоятельно, но проще использовать экземпляр `SearchServiceClient`, вызывающий метод `Indexes.GetClient`. Например, получить `SearchIndexClient` для индекса с именем hotels из `SearchServiceClient` с именем `serviceClient` можно следующим образом.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> В типичном поисковом приложении управление индексами и заполнение обрабатываются отдельным компонентом из запросов поиска. Элемент `Indexes.GetClient` удобен для заполнения индекса, так как он избавляет от необходимости указывать другой элемент `SearchCredentials`. Это достигается благодаря передачи ключа администратора, который использовался для создания `SearchServiceClient`, в новый элемент `SearchIndexClient`. Однако в части приложения, которая выполняет запросы, лучше создать `SearchIndexClient` напрямую, чтобы передать ключ запроса вместо ключа администратора. Это согласуется с [принципом наименьших прав доступа](https://en.wikipedia.org/wiki/Principle_of_least_privilege) и поможет сделать приложение более безопасным. Дополнительные сведения о ключах администратора и ключах запросов см. справочнике [REST API службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

У класса `SearchIndexClient` есть свойство `Documents`. Это свойство предоставляет все методы, которые необходимы для добавления, изменения, удаления и запроса документов в индексе.

## <a name="decide-which-indexing-action-to-use"></a>Выбор операций индексирования
Чтобы импортировать данные с помощью пакета SDK для .NET, необходимо упаковать данные в объект `IndexBatch`. `IndexBatch` инкапсулирует коллекцию объектов `IndexAction`, каждый из которых содержит документ и свойство. С помощью этого свойства служба поиска Azure определяет, какое действие необходимо выполнить с соответствующим документом (отправка, объединение, удаление и др.). В зависимости от выбранной операции для каждого документа будут включены только определенные поля.

| Действие | ОПИСАНИЕ | Необходимые поля для каждого документа | Заметки |
| --- | --- | --- | --- |
| `Upload` |Операция `Upload` аналогична операции upsert, которая добавляет документ, если он новый, и обновляет либо заменяет его, если он уже существует. |Поле key, а также другие поля, которые вы хотите определить. |При обновлении или замене существующего документа все поля, не указанные в запросе, получат значение `null`. Это происходит, даже если для поля указано непустое значение. |
| `Merge` |Обновляет существующий документ с использованием указанных полей. Если документ не существует в индексе, объединение завершится ошибкой. |Поле key, а также другие поля, которые вы хотите определить. |Поля, указанные в запросе на объединение, заменяют собой существующие поля документа. Это относится и к полям типа `DataType.Collection(DataType.String)`. Например, если документ содержит поле `tags` со значением `["budget"]` и вы выполняете операцию объединения со значением `["economy", "pool"]` для поля `tags`, в итоге поле `tags` примет значение `["economy", "pool"]`, а не `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Эта операция аналогична операции `Merge`, если документ с указанным значением ключа уже существует в индексе. Если документа нет, эта операция выполняется так же, как и операция `Upload` с новым документом. |Поле key, а также другие поля, которые вы хотите определить. |- |
| `Delete` |Удаление указанного документа из индекса. |Только ключ |Все указанные поля, которые отличаются от ключевого поля, будут игнорироваться. Чтобы удалить из документа определенное поле, лучше воспользуйтесь операцией `Merge`, явным образом задав для требуемого поля значение NULL. |

В следующем разделе показано, как можно выбрать действие, которое вы хотите использовать с различными статическими методами классов `IndexBatch` и `IndexAction`.

## <a name="construct-your-indexbatch"></a>Создание класса IndexBatch
Теперь, когда вы знаете, какие операции можно выполнять с документами, давайте создадим `IndexBatch`. В приведенном ниже примере показано, как создать пакет с несколькими разными операциями. Обратите внимание, что в нашем примере используется пользовательский класс с именем `Hotel` , сопоставленный с документом в индексе hotels.

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
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
            }),
        IndexAction.Upload(
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
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

В этом случае в качестве операций поиска мы используем `Upload`, `MergeOrUpload` и `Delete`, что соответствует методам, вызываемым для класса `IndexAction`.

Предположим, что пример индекса с именем hotels уже заполнен несколькими документами. Обратите внимание: нам не понадобилось указывать все поля документа при использовании операции `MergeOrUpload`, а при использовании `Delete` мы указали только ключ документа (`HotelId`).

Также помните о том, что в один запрос на индексирование можно включить не более 1000 документов.

> [!NOTE]
> В нашем примере к разным документами применяются разные действия. Если вы хотите выполнить одинаковые действия со всеми документами в пакете, вместо вызова метода `IndexBatch.New` следует использовать другие статические методы класса `IndexBatch`. Например, можно создать пакеты, вызвав метод `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` или `IndexBatch.Delete`. Эти методы вместо объектов `IndexAction` принимают коллекцию документов (в нашем примере это объекты типа `Hotel`).
> 
> 

## <a name="import-data-to-the-index"></a>Импорт данных в индекс
Теперь, когда у нас есть инициализированный объект `IndexBatch`, мы можем отправить его в индекс, вызвав метод `Documents.Index` для объекта `SearchIndexClient`. В следующем примере показано, как вызвать `Index`, а также выполнить несколько необходимых дополнительных действий.

```csharp
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
```

Обратите внимание на фрагмент `try`/`catch`, окружающий вызов метода `Index`. Блок catch обрабатывает важные ошибки, которые могут возникнуть во время индексирования. Если службе поиска Azure не удается индексировать некоторые документы в пакете, `Documents.Index` вызывает `IndexBatchException`. Это может произойти, если вы индексируете документы службы при интенсивной нагрузке. **Настоятельно рекомендуется явно обрабатывать этот случай в коде.** Вы можете задержать и повторить попытку индексирования соответствующих документов либо занести ошибку в журнал и продолжить работу, как в нашем примере, а также выполнить другие действия в зависимости от требований вашего приложения к целостности данных.

В примере выше в коде указана двухсекундная задержка. Индексирование в службе поиска Azure происходит асинхронно, поэтому образец приложения должен подождать немного, пока документы не станут доступными для поиска. Такие задержки обычно необходимы только в демонстрациях, тестах и примерах приложений.

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>Обработка документов пакетом SDK для .NET
Вас может интересовать, как пакет SDK для Поиска Azure в .NETможет передавать экземпляры пользовательского класса, например `Hotel` , в индекс. Чтобы ответить на этот вопрос, давайте рассмотрим класс `Hotel` , который сопоставлен со схемой индекса, определенной в статье [Создание индекса для службы поиска Azure с помощью .NET](search-create-index-dotnet.md#DefineIndex).

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
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

    // ToString() method omitted for brevity...
}
```

Прежде всего следует отметить, что каждое общедоступное свойство `Hotel` соответствует полю в определении индекса, но с одним ключевым отличием: имя каждого поля начинается со строчной буквы (нижнего регистра), в то время как имя каждого общего свойства `Hotel` начинается с прописной буквы (верхнего регистра). Это часто встречается в приложениях .NET, выполняющих привязку данных, если целевая схема не подлежит управлению разработчика приложения. Чтобы не нарушать правил присвоения имен .NET, указывая имена свойств в нижнем регистре, вы можете сделать так, чтобы пакет SDK автоматически сопоставлял имена свойств в нижнем регистре, с помощью атрибута `[SerializePropertyNamesAsCamelCase]` .

> [!NOTE]
> Пакет SDK службы поиска Azure для .NET использует библиотеку [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) для сериализации и десериализации настраиваемых объектов модели в JSON и обратно. При необходимости эту сериализацию можно настроить. Дополнительные сведения см. в разделе [Пользовательская сериализация с помощью JSON.NET](search-howto-dotnet-sdk.md#JsonDotNet). Например, в приведенном выше примере кода в свойстве `DescriptionFr` можно указать атрибут `[JsonProperty]`.
> 
> 

Вторая важная составляющая класса `Hotel` — типы данных общих свойств. Типы .NET этих свойств сопоставляются с эквивалентными типами полей в определении индекса. Например, свойство строки `Category` сопоставляется с полем `category`, которое имеет тип `DataType.String`. Аналогичные сопоставления присутствуют между типами `bool?` и `DataType.Boolean`, `DateTimeOffset?` и `DataType.DateTimeOffset` и т. д. Конкретные правила сопоставления типов указаны в описании метода `Documents.Get` в [справочнике по SDK для .NET службы поиска Azure](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

Возможность использовать собственные классы как документы работает и в обратную сторону. Вы также можете получать результаты поиска, настроив пакет SDK на автоматическую десериализацию в выбранный тип, как показано в [следующей статье](search-query-dotnet.md).

> [!NOTE]
> Пакет SDK для Поиска Azure в .NET также поддерживает документы динамических типов, поддерживающие класс `Document`, то есть сопоставление ключей и значений с именами и значениями полей. Это полезно в тех случаях, когда во время разработки вам неизвестна схема индекса или привязка к конкретным классам моделей нецелесообразна. Все методы пакета SDK, связанные с документами, имеют перегрузки, работающие с классом `Document`, а также строго типизированные перегрузки, принимающие параметр универсального типа. Пример кода в этой статье содержит перегрузки только второго типа.
> 
> 

**Почему следует использовать типы данных, допускающие значение NULL**

При разработке собственных классов модели для сопоставления с индексом поиска Azure рекомендуется объявлять свойства типов значений, такие как `bool` и `int`, допускающих нулевое значение (например: `bool?` вместо `bool`). При использовании ненулевого свойства необходимо **гарантировать** , что документы в индексе не будут содержать значение NULL для соответствующего поля. Ни пакет SDK, ни служба поиска Azure не помогут вам это обеспечить.

Это не просто гипотетическое соображение. Представьте себе ситуацию, когда вы добавляете новое поле в существующий индекс с типом `DataType.Int32`. После обновления определения индекса все документы будут иметь значение null для этого нового поля (поскольку все типы допускают значение NULL в службе поиска Azure). Если затем для этого поля вы используете класс модели со свойством `int`, не допускающим нулевое значение, при попытке получения документов вы получите `JsonSerializationException` следующим образом:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

По этой причине по-прежнему рекомендуется использовать типы, допускающие значения NULL, в классах модели.

## <a name="next-steps"></a>Дополнительная информация
Заполнив индекс службы поиска Azure, вы можете приступать к отправке запросов на поиск документов. Дополнительные сведения см. в статье [Запросы в службе поиска Azure](search-query-overview.md).

