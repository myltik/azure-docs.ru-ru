---
title: "Обновление пакета SDK службы поиска Azure для .NET до версии 1.1 | Документация Майкрософт"
description: "Обновление пакета SDK службы поиска Azure для .NET до версии 1.1"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 66f89958-a320-4a24-87f9-69315848909f
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 3e2ad6b466ba4885ae14576b83d4c0f3010bab67
ms.openlocfilehash: 9782454e3bfc697b63cde8aa28a14be0c393c36b
ms.lasthandoff: 01/13/2017


---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Обновление пакета SDK службы поиска Azure для .NET до версии 3
Если вы используете версию 2.0-preview [пакета SDK службы поиска Azure для .NET](https://aka.ms/search-sdk) или более раннюю, то эта статья поможет вам обновить приложение для использования версии 3.

Более общее пошаговое руководство по пакету SDK, включая примеры, см. в разделе [Использование службы поиска Azure в приложении .NET](search-howto-dotnet-sdk.md).

Версия 3 пакета SDK службы поиска Azure для .NET содержит некоторые отличия от более ранних версий. Эти отличия в основном незначительные, поэтому изменение кода потребует минимальных усилий. В разделе [Действия по обновлению](#UpgradeSteps) вы найдете инструкции о том, как изменить код для использования новой версии пакета SDK.

> [!NOTE]
> Если вы используете версию 1.0.2-preview или более раннюю, то сначала следует установить версию 1.1, и только затем обновить ее до версии 3. Ознакомьтесь с инструкциями в разделе [Приложение. Инструкции по обновлению до версии 1.1](#UpgradeStepsV1) .
>
> Экземпляр службы поиска Azure поддерживает несколько версий REST API, включая последнюю. Можно продолжать использовать версию, которая больше не является последней, но рекомендуется выполнить перенос кода, чтобы использовать последнюю версию. При использовании REST API необходимо указывать версию API в каждом запросе с помощью параметра api-version. При использовании пакета SDK для .NET версия пакета SDK, которую вы используете, определяет соответствующую версию REST API. Если вы используете устаревшую версию пакета SDK, можно продолжать выполнять этот код без изменений, даже если служба обновлена для поддержки более новой версии API.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Новые возможности в версии 3
Версия 3 пакета SDK службы поиска Azure для .NET предназначена для последней общедоступной версии интерфейса REST API службы поиска Azure, в частности версии 2016-09-01. Это дает возможность использовать различные новые функции службы поиска Azure из приложений для .NET, включая следующие:

* [Пользовательские анализаторы](https://aka.ms/customanalyzers)
* поддержка индексатора [хранилища BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md) и [хранилища таблиц Azure](search-howto-indexing-azure-tables.md);
* настройка индексатора посредством [сопоставления полей](search-indexer-field-mappings.md)
* поддержка тегов eTag, обеспечивающих безопасное одновременное обновление определений индекса, индексаторов и источников данных;
* поддержка декларативного создания определений полей индекса через дополнение класса модели и с помощью нового класса `FieldBuilder`;
* поддержка .NET Core и .NET Portable Profile 111.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Действия по обновлению
Прежде всего обновите справочник NuGet для `Microsoft.Azure.Search` , воспользовавшись консолью диспетчера пакетов NuGet или щелкнув правой кнопкой мыши ссылки проекта и выбрав "Управление пакетами NuGet" в Visual Studio.

После того как NuGet загрузит новые пакеты и их зависимости, перестройте проект. Сборка может быть выполнена успешно — в зависимости от того, как структурирован ваш код. Если она выполнена, то теперь все готово.

В противном случае отобразится сообщение об ошибке сборки следующего вида.

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Далее необходимо устранить эту ошибку сборки. Сведения о причинах ошибки и способах ее устранения см. в разделе [Критические изменения в версии 3](#ListOfChanges).

Могут появиться дополнительные предупреждения о сборке, связанные с устаревшими методами или свойствами. В предупреждениях будет указано, что следует использовать вместо устаревшей функции. Например, если приложение использует `IndexingParameters.Base64EncodeKeys`, то отобразится предупреждение `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`.

После устранения ошибки сборки при необходимости можно внести изменения в приложение, чтобы воспользоваться преимуществами новых функциональных возможностей. Новые возможности в пакете SDK описаны в разделе [Новые возможности в версии 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Критические изменения в версии 3
В версии 3 содержится небольшое количество критических изменений, для которых может потребоваться изменение кода и выполнение повторной сборки приложения.

### <a name="indexesgetclient-return-type"></a>Тип возвращаемого значения Indexes.GetClient
Метод `Indexes.GetClient` имеет новый тип возвращаемого значения. Раньше он возвращал `SearchIndexClient`, но в версии 2.0-preview значение было изменено на `ISearchIndexClient`, и это изменение переносится в версию 3. Это необходимо для поддержки клиентов, которые желают макетировать метод `GetClient` для модульных тестов, возвращая реализацию макета `ISearchIndexClient`.

#### <a name="example"></a>Пример
Если код выглядит следующим образом:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Можно изменить его на следующий для устранения ошибок сборки:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>Типы AnalyzerName, DataType и другие больше не преобразуются в строки неявным образом
В пакете SDK службы поиска Azure для .NET существует много типов, которые являются производными от `ExtensibleEnum`. Ранее все они неявно преобразовывались в тип `string`. Однако в реализации `Object.Equals` была обнаружена ошибка для этих классов, и для исправления данной ошибки потребовалось отключить неявное преобразование. При этом явное преобразование в тип `string` по-прежнему доступно.

#### <a name="example"></a>Пример
Если код выглядит следующим образом:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Можно изменить его на следующий для устранения ошибок сборки:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Удалены устаревшие члены

Вы можете столкнуться с ошибками сборки, связанными с методами или свойствами, которые были отмечены в версии 2.0-preview как устаревшие, а в версии 3 были удалены. При возникновении таких ошибок воспользуйтесь описанными ниже способами их устранения.

- Если вы использовали конструктор `ScoringParameter(string name, string value)`, то вместо него воспользуйтесь этим: `ScoringParameter(string name, IEnumerable<string> values)`.
- Если вы использовали свойство `ScoringParameter.Value`, то вместо него воспользуйтесь свойством `ScoringParameter.Values` или методом `ToString`.
- Если вы использовали свойство `SearchRequestOptions.RequestId`, то вместо него воспользуйтесь свойством `ClientRequestId`.

### <a name="removed-preview-features"></a>Удалены функции предварительной версии

При обновлении с версии 2.0-preview до версии 3 учитывайте, что поддержка анализа JSON и CSV для индексаторов больших двоичных объектов была удалена, так как эта функция все еще находится на стадии предварительной версии. В частности, удалены следующие методы класса `IndexingParametersExtensions`:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Если ваше приложение имеет жесткую зависимость от этих функций, то вы не сможете обновить пакет SDK службы поиска Azure для .NET до версии 3. Вы можете и далее использовать версию 2.0-preview. Однако необходимо учитывать, что **использовать предварительные версии пакетов SDK в рабочих приложениях не рекомендуется**. Предварительные версии функций предназначены исключительно для оценки и могут изменяться.

## <a name="conclusion"></a>Заключение
Если вам нужны дополнительные сведения об использовании пакета SDK для .NET для Поиска Azure, то ознакомьтесь с нашим недавно обновленным [пошаговым руководством](search-howto-dotnet-sdk.md).

Будем рады вашим отзывам о пакете SDK. Если вы столкнулись с проблемами, то всегда можете обратиться за помощью на [форуме по Поиску Azure на сайте MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). При обнаружении ошибки можно зарегистрировать проблему в [репозитории GitHub пакета SDK .NET для Azure](https://github.com/Azure/azure-sdk-for-net/issues). Добавьте в название вашей проблемы префикс "пакет SDK для поиска:".

Благодарим вас за использование поиска Azure!

<a name="UpgradeStepsV1"></a>

## <a name="appendix-steps-to-upgrade-to-version-11"></a>Приложение. Инструкции по обновлению до версии 1.1
> [!NOTE]
> Этот раздел относится только к пользователям версии 1.0.2-preview пакета SDK службы поиска Azure для .NET и более ранних версий.
> 
> 

Прежде всего обновите справочник NuGet для `Microsoft.Azure.Search` , воспользовавшись консолью диспетчера пакетов NuGet или щелкнув правой кнопкой мыши ссылки проекта и выбрав "Управление пакетами NuGet" в Visual Studio.

После того как NuGet загрузит новые пакеты и их зависимости, перестройте проект.

Если ранее вы пользовались предварительной версией 1.0.0, 1.0.1 или 1.0.2, построение будет выполнено успешно.

Если ранее вы пользовались предварительной версией 0.13.0 или более ранней, вы должны увидеть примерно такие ошибки построения:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Далее необходимо устранить ошибки построения одну за другой. Большинство требует изменения некоторых имен классов и методов, которые были переименованы в пакете SDK. [Список критических изменений в версии 1.1](#ListOfChangesV1) содержит список этих изменений имен.

Если вы используете настраиваемые классы для моделирования документов и эти классы имеют свойства примитивных типов, не допускающие нулевого значения (например `int` или `bool` в C#), в версии пакета SDK 1.1 существует исправление ошибки, которое следует иметь в виду. Дополнительные сведения см. в разделе [Исправления в версии 1.1](#BugFixesV1).

Наконец, после устранения ошибки сборки при необходимости можно внести изменения в приложение, чтобы воспользоваться преимуществами новых функциональных возможностей.

<a name="ListOfChangesV1"></a>

### <a name="list-of-breaking-changes-in-version-11"></a>Список критических изменений в версии 1.1
Следующий список упорядочен по вероятности того, повлияет ли изменение на код приложения.

#### <a name="indexbatch-and-indexaction-changes"></a>Изменения IndexBatch и IndexAction
`IndexBatch.Create` был переименован в `IndexBatch.New` и больше не имеет аргумента `params`. Можно использовать `IndexBatch.New` для пакетов, сочетающих в себе различные типы действий (слияние, удаление и т. д.). Кроме того, существуют новые статические методы для создания пакетов, в которых все действия одинаковы: `Delete`, `Merge`, `MergeOrUpload` и `Upload`.

`IndexAction` больше не имеет открытых конструкторов, и его свойства теперь являются неизменяемыми. Следует использовать новые статические методы для создания действий для различных целей: `Delete`, `Merge`, `MergeOrUpload` и `Upload`. `IndexAction.Create` был удален. Если используется перегрузка, которая принимает только документ, обязательно используйте вместо нее `Upload` .

##### <a name="example"></a>Пример
Если код выглядит следующим образом:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Можно изменить его на следующий для устранения ошибок сборки:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Если требуется, можно упростить его так:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Изменения IndexBatchException
Свойство `IndexBatchException.IndexResponse` было переименовано в `IndexingResults`, и теперь его тип `IList<IndexingResult>`.

##### <a name="example"></a>Пример
Если код выглядит следующим образом:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Можно изменить его на следующий для устранения ошибок сборки:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

#### <a name="operation-method-changes"></a>Изменение метода операции
Каждая операция в пакете SDK для .NET для поиска Azure представляется как набор перегрузок метода для синхронных и асинхронных вызовов. В версии 1.1 были изменены подписи и разложение этих перегрузок метода.

Например, операция "Получить статистику индекса" в более старых версиях пакета SDK предоставляла эти подписи:

В `IIndexOperations`добавьте:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

В `IndexOperationsExtensions`добавьте:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Подписи метода для той же операции в версии 1.1 выглядят следующим образом.

В `IIndexesOperations`добавьте:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

В `IndexesOperationsExtensions`добавьте:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Начиная с версии 1.1, пакет SDK для .NET для поиска Azure распределяет методы операции иначе:

* Необязательные параметры теперь моделируются как параметры по умолчанию, а не как дополнительные перегрузки метода. Это уменьшает количество перегрузок метода, порой радикально.
* Методы расширения теперь скрывают много лишних сведений HTTP от вызывающего объекта. Например, более старые версии пакета SDK возвращали объект ответа с кодом состояния HTTP, который часто не нужно проверять, так как методы операции создают исключение `CloudException` для любого кода состояния, который указывает на ошибку. Новые методы расширения просто возвращают объекты модели, что устраняет необходимость развертывать их в коде.
* И наоборот, основные интерфейсы теперь демонстрируют методы, которые предоставляют более полный контроль на уровне HTTP при необходимости. Теперь можно передавать пользовательские заголовки HTTP для добавления в запросы, и новый возвращаемый тип. Теперь можно передавать пользовательские заголовки HTTP для добавления в запросы, и новый возвращаемый тип `AzureOperationResponse<T>` предоставляет прямой доступ к `HttpRequestMessage` и `HttpResponseMessage` для операции. `AzureOperationResponse` определяется в пространстве имен `Microsoft.Rest.Azure` и заменяет `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Изменения в параметрах оценки
В последнюю версию пакета SDK был добавлен класс `ScoringParameter` для упрощения передачи параметров профилям оценки в поисковом запросе. Ранее свойство `ScoringProfiles` класса `SearchParameters` имело тип `IList<string>`. Теперь оно имеет тип `IList<ScoringParameter>`.

##### <a name="example"></a>Пример
Если код выглядит следующим образом:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Можно изменить его на следующий для устранения ошибок сборки: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Изменение классов модели
Из-за изменений подписи, описанных в разделе [Изменение метода операции](#OperationMethodChanges), многие классы в пространстве имен `Microsoft.Azure.Search.Models` были переименованы или удалены. Например:

* `IndexDefinitionResponse` был заменен на `AzureOperationResponse<Index>`.
* `DocumentSearchResponse` был переименован в `DocumentSearchResult`.
* `IndexResult` был переименован в `IndexingResult`.
* `Documents.Count()` теперь возвращает `long` с количеством документов вместо `DocumentCountResponse`.
* `IndexGetStatisticsResponse` был переименован в `IndexGetStatisticsResult`.
* `IndexListResponse` был переименован в `IndexListResult`.

Подводя итог, классы, производные от `OperationResponse`, которые существовали только для оборачивания объектов модели, были удалены. В остальных классах суффикс изменен с `Response` на `Result`.

##### <a name="example"></a>Пример
Если код выглядит следующим образом:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Можно изменить его на следующий для устранения ошибок сборки:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Классы ответа и IEnumerable
Дополнительное изменение, которое может повлиять на код, состоит в том, что классы ответов, содержащие коллекции, больше не реализуют `IEnumerable<T>`. Вместо этого вы можете получать прямой доступ к свойству коллекции. Например, если код выглядит следующим образом:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Можно изменить его на следующий для устранения ошибок сборки:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="special-case-for-web-applications"></a>Исключение, касающееся веб-приложений
Если у вас есть веб-приложение, которое сериализует `DocumentSearchResponse` непосредственно для отправки результатов поиска в браузер, необходимо будет внести изменения в код, иначе результаты не будут сериализованы правильно. Например, если код выглядит следующим образом:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Его можно изменить путем получения свойства `.Results` для ответа поиска, чтобы исправить отрисовку результата поиска.

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Вам придется найти такие случаи в коде самостоятельно. **Компилятор не предупредит вас**, так как `JsonResult.Data` имеет тип `object`.

#### <a name="cloudexception-changes"></a>Изменения CloudException
Класс `CloudException` перемещен из пространства имен `Hyak.Common` в пространство имен `Microsoft.Rest.Azure`. Кроме того, его свойство `Error` было изменено на `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Изменения SearchServiceClient и SearchIndexClient
Тип свойства `Credentials` был изменен с `SearchCredentials` на базовый класс `ServiceClientCredentials`. Если необходимо получить доступ к `SearchCredentials` из `SearchIndexClient` или `SearchServiceClient`, используйте новое свойство `SearchCredentials`.

В более старых версиях пакета SDK у `SearchServiceClient` и `SearchIndexClient` были конструкторы, которые принимали параметр `HttpClient`. Они были заменены конструкторами, принимающими `HttpClientHandler` и массив объектов `DelegatingHandler`. При необходимости это упрощает установку пользовательских обработчиков для предварительной обработки HTTP-запросов.

Наконец, изменились конструкторы, которые принимали `Uri` и `SearchCredentials`. Например, если у вас есть код, который выглядит следующим образом:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Можно изменить его на следующий для устранения ошибок сборки:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Также обратите внимание, что тип параметра учетных данных изменился на `ServiceClientCredentials`. Это вряд ли повлияет на код, так как `SearchCredentials` является производным от `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Передача идентификатора запроса
В более старых версиях пакета SDK можно было задать идентификатор запроса в `SearchServiceClient` или `SearchIndexClient`, и он включался в каждый запрос к REST API. Это полезно для устранения неполадок в службе поиска, если необходимо обратиться в службу поддержки. Однако более полезно присвоить уникальный идентификатор запроса для каждой операции, а не использовать тот же идентификатор для всех операций. По этой причине методы `SetClientRequestId` из `SearchServiceClient` и `SearchIndexClient` были удалены. Вместо этого вы можете передать идентификатор запроса для каждого метода операции через необязательный параметр `SearchRequestOptions` .

> [!NOTE]
> В будущем выпуске пакета SDK мы добавим новый механизм для глобальной настройки идентификатора запроса на объектах клиента, который согласован с подходом, используемым другим пакетом SDK Azure.
> 
> 

#### <a name="example"></a>Пример
Если у вас есть код, который выглядит следующим образом:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Можно изменить его на следующий для устранения ошибок сборки:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Изменение имени интерфейса
Имена интерфейсов группы операций изменены, чтобы согласовать их с соответствующими именами свойств:

* Тип `ISearchServiceClient.Indexes` был переименован из `IIndexOperations` в `IIndexesOperations`.
* Тип `ISearchServiceClient.Indexers` был переименован из `IIndexerOperations` в `IIndexersOperations`.
* Тип `ISearchServiceClient.DataSources` был переименован из `IDataSourceOperations` в `IDataSourcesOperations`.
* Тип `ISearchIndexClient.Documents` был переименован из `IDocumentOperations` в `IDocumentsOperations`.

Это изменение вряд ли повлияет на код, если вы не создали макеты этих интерфейсов для тестирования.

<a name="BugFixesV1"></a>

### <a name="bug-fixes-in-version-11"></a>Исправления в версии 1.1
В более старых версиях пакета SDK для .NET для поиска Azure была ошибка, связанная с сериализацией классов пользовательской модели. Ошибка могла произойти, если вы создавали класс пользовательской модели со свойством типа значения, не допускающего нуля.

#### <a name="steps-to-reproduce"></a>Шаги для воспроизведения
Создайте класс пользовательской модели со свойством типа значения, не допускающего нуля. Например, добавьте открытое свойство `UnitCount` типа `int` вместо `int?`.

При индексировании документа со значением по умолчанию этого типа (например 0 для `int`) поле будет иметь значение NULL в Поиске Azure. Если впоследствии выполняется поиск этого документа, вызов `Search` выдаст `JsonSerializationException` с сообщением о том, что невозможно преобразовать `null` в `int`.

Кроме того, фильтры могут не работать, как ожидалось, поскольку в индекс был записано значение null вместо ожидаемого значения.

#### <a name="fix-details"></a>Сведения об исправлении
Эта проблема устранена в пакете SDK версии 1.1. Теперь, если имеется такой класс модели:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

и вы устанавливаете для `IntValue` значение 0, это значение теперь правильно сериализуется как 0 по сети и сохраняется как 0 в индексе. Циклы обработки также работают ожидаемым образом.

Существует одна потенциальная проблема, которую следует учитывать при таком подходе: при использовании типа модели со свойством, не допускающим значения NULL, необходимо **гарантировать** , что документы в индексе не будут содержать значение NULL для соответствующего поля. Ни пакет SDK, ни API REST поиска Azure не поможет вам это обеспечить.

Это не просто гипотетическое соображение. Представьте себе ситуацию, когда вы добавляете новое поле в существующий индекс с типом `Edm.Int32`. После обновления определения индекса все документы будут иметь значение null для этого нового поля (поскольку все типы допускают значение NULL в службе поиска Azure). Если затем для этого поля вы используете класс модели со свойством `int`, не допускающим нулевое значение, при попытке получения документов вы получите `JsonSerializationException` следующим образом:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

По этой причине по-прежнему рекомендуется использовать типы, допускающие значения NULL, в классах модели.

Дополнительные сведения по этой ошибке и исправлению см. в разделе об [этой проблеме на GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).


