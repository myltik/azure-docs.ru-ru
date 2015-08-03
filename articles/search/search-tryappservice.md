<properties 
   pageTitle="Попробуйте службу приложений Azure с поиском Azure" 
   description="Попробуйте службу поиска Azure бесплатно в течение одного часа с помощью шаблона TryAzureAppService." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="07/13/2015"
   ms.author="heidist"/>

# Попробуйте службу приложений Azure с поиском Azure

[Попробуйте службу приложений Azure](https://tryappservice.azure.com/) — это новый, совершенно бесплатный способ тестирования выбранных служб Azure, включая поиск Azure, продолжительностью до часа без необходимости регистрации подписки на Azure.

Сайт позволяет выбрать один из нескольких шаблонов. При выборе шаблона ASP.NET, который включает поиск Azure, вы получаете один час доступа к полнофункциональному веб-сайту, оснащенному выбранными службами. Обновлять и удалять данные, управляемые поиском Azure, невозможно, но вы можете выполнять запросы и выполнять сколько угодно изменений кода, меняющих пользовательский интерфейс. Если срок сеанса истечет до того, как вы закончите тестирование, вы всегда можете начать заново в другом сеансе или перейти на пробную или полную подписку, если вы собираетесь создать или загрузить индекс напрямую.

На сайте [Попробуйте службу приложений Azure](https://tryappservice.azure.com/) поиск Azure входит в состав шаблона "Веб-приложение", предоставляя функциональный полнотекстовый интерфейс поиска со множеством функций поиска, доступных только в этой службе на платформе Azure.

Несмотря на то что другие службы Azure, такие как база данных SQL, включают полнотекстовый поиск, служба поиска Azure предоставляет возможности настройки, разбиения на страницы и счетчиков, выделения попаданий, автозаполнения рекомендуемых запросов, поддержку естественного языка, фасетную навигацию, фильтры и многое другое. Как демонстрирует несколько наших [примеров](https://github.com/AzureSearch), вы можете разработать полнофункциональное приложение для поиска, используя только службу поиска Azure и ASP.NET.

В рамках предложения [Попробуйте службу приложений Azure](https://tryappservice.azure.com/) служба поиска Azure доступна только для чтения. Это означает, что вам придется использовать совокупность искомых данных, представленную в сеансе. Загружать и использовать собственный индекс или данные невозможно. Данные, с которыми вы будете работать, получены из [геологического исследования США (USGS)](), состоящего из примерно 3 миллионов строк с достопримечательностями, историческими местами, зданиями и другими интересными местами США.

Приведенные ниже указания с запросами и кодом помогут вам в полной мере воспользоваться 1-часовым сеансом.

Прежде чем двигаться дальше, рекомендуем потратить несколько минут на изучение основных сведений о коде, службе и искомых данных. Знание основ может быть полезно, если вы еще не знакомы со службой поиска Azure.

##Факты о коде и службе поиска Azure

Служба поиска Azure — это предложение [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service), состоящее из полностью управляемой службы поиска и искомых данных, которые можно загружать при использовании неограниченного экземпляра службы поиска Azure (т. е. без использования предложения "Попробуйте службу приложений Azure").

Данные, используемые в операциях поиска, хранятся в службе поиска Azure, где близость данных к операциям обеспечивает низкую задержку и предсказуемое поведение поиска. В настоящий момент отсутствует поддержка автономного и удаленного хранения искомых данных. Немного подробностей:

- Данные для поиска хранятся в индексе, управляемом службой поиска Azure и заполненном документами (по одному на искомый элемент). 
- Большинство индексов загружается из одного набора данных, заранее подготовленного вами с использованием только тех полей, которые будут полезны в контексте операций поиска. 
- Схема, которая задает индекс, определяется пользователем и задает поля, доступные для поиска, недоступные для поиска поля, которые могут быть полезны в выражениях фильтра, и таких конструкций, как профили оценивания для настройки результатов.
- Данные могут автоматически загружаться индексатором (поддерживаемом только для баз данных SQL Azure и Azure DocumentDB) или поступать в индекс поиска через один из API-интерфейсов поиска Azure. При использовании API-интерфейса можно передавать данные из любого источника, если они имеют формат JSON.

В предложении [Попробуйте службу приложений Azure](https://tryappservice.azure.com/) шаблон "ASP.NET + сайт поиска Azure" предоставляет исходный код для веб-приложения, который можно изменять в Visual Studio Online (доступном в рамках 1-часового сеанса). Для просмотра и изменения кода не требуется отдельных средств разработки.

Код написан на языке C# с использованием [клиентской библиотеки .NET поиска Azure](https://msdn.microsoft.com/library/dn951165.aspx) для выполнения запросов к индексу, обеспечения фасетной навигации, а также отображения счетчиков и результатов поиска на веб-странице.

Другой код, не включенный в шаблон, использовался для создания и загрузки поискового индекса USGS. Так как служба доступна только для чтения, все операции, требующие разрешения на запись, необходимо выполнить заранее. Вы можете просмотреть [копию схемы](#schema), используемую для создания схема, в конце этой статьи.

##Приступая к работе

Еще 1-часовой сеанс еще не начался, выполните следующие действия, чтобы приступить к работе.

1. Перейдите на сайт [https://tryappservice.azure.com](https://tryappservice.azure.com/) и прокрутите вниз, чтобы выбрать шаблон **Веб-приложение**. 
2. Нажмите кнопку **Далее**.
3. Выберите шаблон **ASP.NET + сайт поиска Azure**.

    ![][1]

4. Щелкните **Создать**.
5. Выберите метод входа, а затем укажите имя пользователя и пароль.

    ![][2]

6. Подождите, пока подготавливается сайт. Когда он будет готов, вы увидите страницу примерно следующего вида. На каждой странице отображаются идущие часы, чтобы вы всегда знали, сколько времени осталось.

    ![][3]

7. Выберите команду **Изменить в Visual Studio Online**, чтобы показать решение и просмотреть сайт.
9. В Visual Studio Online разверните узлы сеансов в верхней части страницы и выберите команду **Обзор веб-сайта**.

    ![][4]

10. Должна открыться начальная страница веб-сайта поиска Azure. Нажмите кнопку **Начало работы**, чтобы открыть сайт.

    ![][5]

11. В браузере откроется веб-сайт ASP.NET с полем поиска. Введите знакомый термин для поиска, например *Yellowstone*, или название известной горы, например *Mount Rainier*. Начав с известной достопримечательности, вам будет проще оценить результаты.

    ![][6]


##Первые действия
Поскольку индекс поиска является полнофункциональным, рекомендуется начать с нескольких пробных запросов. Поиск Azure поддерживает все стандартные операторы поиска (+, -, |), кавычки для точных совпадений, подстановочные знаки (*) и операторы приоритета. Полный список операторов вы найдете в справочнике по синтаксису запросов.

- Для начала выполните подстановочный поиск с помощью звездочки (`*`). Так вы узнаете, сколько документов найдено в индексе: 2 262 578.
- Затем введите "Yellowstone" и добавьте слова "+center", "+building" и "-ND", чтобы постепенно ограничить результаты поиска центрами для посетителей парка Йеллоустоун, кроме тех, которые находятся в Северной Дакоте: `Yellowstone +center +building -ND`.  
- Попробуйте поисковую фразу, в которой сочетаются операторы приоритета и сопоставления строк: `statue+(park+MT)`. Результаты должны быть аналогичны показанным на следующем снимке экрана. Обратите внимание, что фасетные категории отображаются в разделе "Класс функции" и обеспечивают автоматическую фильтрацию с помощью фасетной навигации — функцию, которая часто встречается в большинстве поисковых приложений.

    ![][7]

Готовы двигаться дальше? Изменим несколько строк кода, чтобы определить результаты операций полнотекстового поиска.

##Изменение searchMode.All

Служба поиска Azure включает настраиваемое свойство **searchMode**, которое можно использовать для управления поведением операторов поиска. Допустимыми значениями для этого свойства являются `Any` (по умолчанию) и `All`. В разделе [Простой синтаксис запросов](https://msdn.microsoft.com/library/dn798920.aspx) вы найдете дополнительные указания по настройке этих параметров.

- Свойство **searchMode.Any** указывает, что любого совпадения в термине поиска достаточно для включения элемента в результаты поиска. Если ввести фразу для поиска `Yellowstone visitor center`, то любой документ, содержащий один из этих терминов, будет включен в результаты поиска. Этот режим направлен на *количество* результатов.
- Свойство **searchModel.All**, используемое в этом примере, требует, чтобы все указанные элементы присутствовали в документе. Этот режим более строг, чем **searchMode.Any**, но если вы предпочитаете *точность*, а не количество результатов, то это правильный выбор для вашего приложения. 

> [AZURE.NOTE]Режим **searchMode.Any** лучше подходит для конструкций запросов, состоящих в основном из фраз, с минимальным использованием операций. Общая закономерность заключается в том, что люди, которые ищут потребительские приложения, например сайты электронной коммерции, обычно используют только термины, а пользователи, которые ищут контент или данные, чаще включают операторы в поисковую фразу. Если вы считаете, что при поиске будут часто использовать операторы, например `NOT (-)`, начните с режима **searchModel.All**. В то же время, другой вариант, **searchMode.Any**, применяет операцию `OR` к оператору `NOT` с другими терминами поиска, что может значительно расширять, а не сокращать результаты. Приведенный ниже пример поможет вам понять разницу.

В этом задании мы изменим свойство **searchMode** и сравним результаты поиска в разных режимах.

1. Откройте окно браузера, содержащее пример приложения, и выберите пункт **Подключиться к Visual Studio Online**.

    ![][8]

2. Откройте файл **Search.cshtml**, найдите строку `searchMode.All` в строке 39 и замените ее на `searchMode.Any`.

    ![][9]

3. В панели переходов справа нажмите кнопку **Запустить**.

    ![][10]
 
В перестроенном окне приложения введите термин поиска, который вы использовали раньше, например `Yellowstone +center +building -ND`, и сравните результаты изменения свойства **searchMode**.

Различия весьма значительны. Вместо семи результатов поиска можно получить более двух миллионов.

   ![][11]
 
Наблюдаемое поведение вызвано включением оператора `NOT` (в этом случае "-ND"), к которому применяется операция *OR*, а не *AND*, если свойство **searchMode** имеет значение `Any`.

При такой конфигурации результаты поиска включает попадания для терминов поиска `Yellowstone`, `center` и `building`, а также все документы по условию `NOT North Dakota`. Так как имеет только 13 081 документов с фразой `North Dakota`, то возвращается почти весь набор данных.

Конечно, это кажется неправдоподобным сценарием, но он иллюстрирует влияние свойства **searchMode** на фразы поиска, которые включают оператор `NOT`, поэтому важно понимать, почему это происходит и как изменить это поведение, если оно вам не подходит.

Чтобы продолжить работу с данным руководством, восстановите исходное значение свойства **searchMode** (`All` в строке 39), запустите программу и используйте перестроенное приложение для остальных действий.
 
##Добавление глобального фильтра для штата Вашингтон

Как правило, если требуется выполнить поиск по подмножеству доступных данных, при импорте данных в источнике данных задают фильтр. В учебных целях при работе с данными, доступными только для чтения, мы создадим фильтр в приложении, чтобы возвращать только документы, где упоминается штат Вашингтон.

1. Откройте файл Search.cshtml, найдите блок кода **SearchParameters** (он начинается со строки 36) и добавьте строку комментария и фильтр.

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


Фильтры задаются с помощью синтаксиса OData и часто используются с фасетной навигацией или включаются в строку запроса для его ограничения. Дополнительные сведения см. в статье [Синтаксис фильтров OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

2. Щелкните **Выполнить**.

3. Откройте приложение.

4. Введите подстановочный знак (*), чтобы вернуть количество. Обратите внимание, что результаты теперь ограничены 42 411 элементом, каждый из которых посвящен географическим достопримечательностям штата Вашингтон.

   ![][12]

##Добавление выделения совпадений

Теперь, когда мы выполнили ряд изменений кода по одной строке, вы можете попробовать более значительные изменения, для которых требуется менять код в нескольких местах. Следующую версию сайта **Search.cshtml** можно вставить вместо файла Search.cshtml в текущем сеансе.

Следующий код добавляет выделение совпадений. Обратите внимание на новые свойства, объявленные в разделе [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx). Кроме того, существует новая функция, которая обходит набор результатов, чтобы получить документы, требующие выделения, а также HTML-код, который выводит результат.

При запуске этого примера кода термины поиска, для которых есть совпадения в указанных полях, выделяются жирным шрифтом.

   ![][14]

Вы можете сохранить копию исходного файла **Search.cshtml**, чтобы сравнить два варианта.

> [AZURE.NOTE]Комментарии были сокращены, чтобы уменьшить размер файла.
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
    <div class="col-xs-12 col-sm-6 col-md-10">
    <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
    <ul class="list-unstyled">
    <!-- Cycle through the search results -->
    @foreach (var item in response.Results)
    {
    <li>
    <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
    <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
    <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
    <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
    <br />
    </li>
    }
    </ul>
    </div>
    }
    }


##Дальнейшие действия

С помощью доступной только для чтения службы, предоставляемой на сайте [Попробуйте службу приложений Azure](https://tryappservice.azure.com/), вы увидели синтаксис запросов и полнотекстовый поиск в действии, изучили свойство searchMode и фильтры, а также добавили выделение совпадений в приложение поиска. В качестве следующего этапа вы можете перейти к созданию и обновлению индексов. Это дает возможность:

- [определять профили оценивания](https://msdn.microsoft.com/library/dn798928.aspx), используемые для настройки оценок поиска, чтобы более ценные элементы отображались первыми;
- [определять рекомендации](https://msdn.microsoft.com/library/mt131377.aspx), которые обеспечивают автозаполнение или предварительные рекомендации запросов в ответ на ввод данных пользователем;
- [определять индексаторы](https://msdn.microsoft.com/library/dn946891.aspx), которые обновляют индекс автоматически, если источником данных является база данных SQL Azure или Azure DocumentDB.

Для выполнения всех этих задач необходима подписка на Azure, чтобы можно было создавать и заполнять индексы в службе. Дополнительные сведения о регистрации бесплатной пробной версии см. на странице [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/).

Сведения о службе поиска Azure см. на [странице документации](http://azure.microsoft.com/documentation/services/search/) сайта [http://azure.microsoft.com](http://azure.microsoft.com) или в [примерах и видео](https://msdn.microsoft.com/library/dn818681.aspx), посвященных полному спектру функций поиска Azure.

<a name="Schema"></a>
##О схеме

На следующем снимке экрана показана схема, используемая для создания индекса, используемого в этом шаблоне.
 
   ![][13]

###Файл Schema.json

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }


<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=July15_HO4-->