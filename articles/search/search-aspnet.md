<properties
	pageTitle="Интеграция службы поиска Azure с веб-приложениями ASP.NET MVC | Microsoft Azure"
	description="Подключение веб-приложения ASP.NET к службе поиска Azure. Узнайте, как подключаться, создавать запросы и обрабатывать результаты с помощью библиотеки .NET или REST API."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="08/26/2015"
	ms.author="heidist"/>

#Интеграция службы поиска Azure c веб-приложениями ASP.NET MVC

ASP.NET MVC является основной платформой веб-приложений в пользовательских решениях, которые интегрируются со службой поиска Azure. В этой статье описано, как включить в приложение ASP.NET службу поиска Azure. Также вы ознакомитесь с шаблонами проектирования стандартных операций и некоторыми методами программирования, которые позволят быстрее освоиться с процессом разработки.

##Примеры и образцы использования ASP.NET и службы поиска Azure

Есть несколько готовых примеров кода, которые демонстрируют, как интегрировать поиск в ASP.NET. По следующим ссылкам вы можете перейти к примерам кода или демонстрационным приложениям.

- [Демоверсия сайта вакансий Нью-Йорка](http://aka.ms/azjobsdemo)
- [Попробуйте службу приложений Azure с поиском Azure](search-tryappservice.md)
- [Полный список видео, учебников, демоверсий и примеров кода](earch-video-demo-tutorial-list.md)

##Подключение к службе

Чтобы установить соединение со службой для передачи запросов, вашему веб-приложению нужны три компонента:

- URL-адрес подготовленной службы поиска Azure в формате https://<service-name>.search.windows.net;
- ключ API (строка) для проверки подлинности при подключении к поиску Azure;
- HTTPClient или SearchServiceClient для формирования запроса на подключение.

####URL-адреса и ключи API

URL-адрес и ключ API можно получить на [портале](search-create-service-portal.md) или программным способом с помощью [REST API управления](https://msdn.microsoft.com/library/dn832684.aspx).

Как правило, URL-адрес и ключ хранятся в файле web.config приложения для взаимодействия с пользователем.

      <appSettings>
    	<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
    	<add key="SearchServiceApiKey" value="[API KEY]" />
    	. . .
      </appSettings>

В качестве имени службы поиска можно использовать короткое имя, указанное во время подготовки, а при подключении добавлять к нему имя домена (search.windows.net). Также вы можете сохранить в файле web.config полное имя (<service-name>.search.windows.net) без префикса HTTPS.

Ключ API — это маркер проверки подлинности. Он создается во время подготовки службы (только ключи администратора) или вручную при создании ключей запроса на портале. Тип ключа определяет операции поиска, которые сможет выполнять ваше приложение:

- ключи администратора (разрешения на чтение и запись; по 2 ключа для каждой службы);
- ключи запроса (только для чтения; до 50 ключей для каждой службы).

Ключи API представляют собой строки длиной 32 символа. Ключи администратора и ключи запроса не имеют визуальных отличий. Если вы забыли тип ключа, указанный в коде, его можно определить с помощью портала или REST API управления. Дополнительные сведения о ключах см. в статье [REST API поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

> [AZURE.TIP]Ключ запроса предоставляет клиенту только право на чтение данных. Статья [Попробуйте службу приложений Azure с поиском Azure](search-tryappservice.md) позволит вам потренироваться в использовании операций поиска Azure, доступных в службе с доступом в режиме только для чтения. Обратите внимание, что в пробной версии службы приложений вы можете полностью изменять код веб-приложения. Например, вы можете поправить код C# в проекте ASP.NET, чтобы изменить макет веб-страницы, структуру поискового запроса или вывод результатов поиска. Доступ на чтение распространяется только на операции загрузки документов и индексирования службы поиска Azure, поскольку при подключении к службе используется ключ запроса.

####Подключение клиента

В двух следующих фрагментах кода устанавливается подключение к службе поиска с использованием URL-адреса и ключа API. Как вы помните, имя службы и ключи API указываются в файле web.config. При вызовах REST ключи администратора должны передаваться в заголовке запроса. Тогда как ключи запроса могут передаваться в заголовке или непосредственно в URL-адресе.

**[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx) с вызовами REST API**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message;
            }
        }

**[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx) с .NET**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##Шаблоны проектирования

Веб-приложение, которое интегрируется в службу поиска Azure, должно формировать запросы и отображать результаты. В этом разделе описано, как структурировать код для выполнения задач в программах, которые содержат код взаимодействия с пользователем. Задачи определения схемы, создания индекса и приема данных здесь не рассматриваются. Рекомендации по выполнению этих операций см. в пошаговых руководствах и примерах, приведенных в статье [Поиск Azure: видео, примеры и учебники](search-video-demo-tutorial-list.md).

###Формирование запросов

Полнотекстовый поиск с помощью индекса выполняется по полям, у которых в схеме определения индекса есть отметка **isSearchable**. Используя входной параметр условия поиска (представленный ниже строкой q), поисковая система ищет совпадения по всем полям, пригодным для поиска. Затем она возвращает результаты, полученные из полей с отметкой **isRetrievable**.

> [AZURE.NOTE]Скорее всего, поиск будет осуществляться почти по всем полям. Но индекс может содержать и поля, которые используются только в критериях фильтра. Для таких полей вам следует убрать отметку isSearchable, чтобы исключить их из полнотекстового поиска, и отметку isRetrievable, чтобы исключить их из результатов поиска.

Поисковый запрос упаковывает введенное пользователем условие поиска в запрос к поисковой системе. В этом запросе указывается индекс, по которому выполняется поиск, а также параметры для фильтрации или уточнения запроса. Встроенные операторы в строке поиска, например «+», «-» или «|», обрабатываются автоматически, и вам не нужно включать в программу дополнительный код для синтаксического анализа условия поиска. Весь синтаксический анализ осуществляется внутренними операциями поисковой системы. Можно предположить, что для переданной строки поисковая система выполнит синтаксический и логический анализ.

Поисковые запросы бывают двух типов: **Search** или **Suggestions**. Вам следует определить отдельные методы для каждого типа запроса. Метод **Search** предполагает полнотекстовый поиск по полям индекса. Метод **Suggestions** поиска Azure представляет собой функцию упреждающего ввода или автоматического завершения запроса. Эта функция создает список потенциальных условий поиска на основе первых трех символов, введенных пользователем. В большинстве случаев удобнее использовать метод **Suggestions** только с полями, значения которых относительно уникальны или отличительны (например, имя продукта или публикации) и не содержат недифференцированные данные.

Следующий фрагмент кода принимает условие поиска в приложении, которое использует REST API. Входной параметр представлен строкой q, а остальные параметры передают значения фильтра из структуры фасетной навигации, расположенной на той же странице поиска. И входной параметр, и значения фильтра используются в поисковом методе Search.

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
Далее представлен метод **Search**, который принимает данный запрос. Обратите внимание, что этот метод определяет параметры строки запроса, а также структуру фасетной навигации (которая реализуется с помощью фильтров, выполняющих непростую задачу по сужению результатов поиска) и порядок сортировки.

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

Метод .NET, который создает строку поиска, можно разместить в представлении MVC или в контроллере. Эта функция передает строку контроллеру Home. Также она определяет структуру данных при выводе результатов.

    function Search() {

        var q = $("#q").val();
        
        $.post('/home/search',
        {
            q: q
        },
        function (data) {
            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
            searchResultsHTML += "<td>DATE EDITED</td></tr>";
            for (var i = 0; i < data.length; i++) {
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
            }

            $("#searchResults").html(searchResultsHTML);

        });

Метод .NET для вызова метода **Search** может выглядеть представленным ниже образом. Кроме того, он может включаться в основную программу C#, которая отвечает за операции подключения и поиска.

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###Обработка результатов поиска

Результаты поиска возвращаются в виде набора строк, в который входят поля с отметкой isRetrievable в схеме индекса. Одним из самых простых способов отображения набора строк результата является использование системного объекта ViewBag в MVC. Следующий фрагмент кода взят из файла Index.cshtml [проекта AdventureWorksDemo на сайте CodePlex](https://azuresearchadventureworksdemo.codeplex.com/).

	@model dynamic
	
	@{
	    ViewBag.Title = "Search";
	}
	
	<h2>Product search</h2>
	
	@if (@ViewBag.errorMessage != null) {
	    @ViewBag.errorMessage
	} else {
	    <div class="container">
	        <form action="/Home/Search" method="get">
	            <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
	            <input type="hidden" name="color" id="color" value="@ViewBag.color" />
	            <input type="hidden" name="category" id="category" value="@ViewBag.category" />
	            <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
	            <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
	            <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
	        </form>
	    </div>

###Фасетная навигация

В том же файле Index.cshmtl используется HTML-код для построения структуры фасетной навигации. Она содержит классификации для самоуправляемой фильтрации с последовательным сужением результатов поиска по цвету, цене или категории.

	    if (@Model != null)
	    {
	        <div class="container">
	            <div class="row">
	                <div class="col-md-4">
	                    Colors:
	                    <ul>
	                        @foreach (var colorFacet in Model["@search.facets"].color)
	                        {
	                            <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
	document.forms[0].submit(); 
	return false;">@colorFacet.value</a> (@colorFacet.count)</li>
	                        }
	                    </ul>
	                    Categories:
	                    <ul>
	                        @foreach (var categoryFacet in Model["@search.facets"].categoryName)
	                        {
	                            <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
	                        }
	                    </ul>
	                    Prices:
	                    <ul>
	                        @foreach (var priceFacet in Model["@search.facets"].listPrice)
	                        {
	                            if (priceFacet.count > 0)
	                            {
	                       <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
	document.forms[0].submit(); return false;
	">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
	                            }
	                        }
	                    </ul>
	                </div>
	                <div class="col-md-8">
	                    <p>
	                        Sort -
	                        <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
	                        <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
	                        <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
	                    </p>
	                    <p>Found @Model["@odata.count"] products in the catalog</p>
	
	                    <ul>
	                        @foreach (var product in Model.value)
	                        {
	                            <li>
	                                <h3><b>@product.name</b></h3>
	                                price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
	                            </li>
	                        }
	                    </ul>
	                </div>
	            </div>
	        </div>
	    }
	}


###Выделение совпадений

Применение стиля к экземпляру условия поиска в результатах поиска называется выделением совпадений. В службе поиска Azure выделение совпадений задается в запросе с помощью параметра поиска highlight. Для этого параметра вы через запятую перечисляете поля, в которых нужно проверять наличие совпадений. Применяемый стиль выделения вы выбираете сами. Следующие три фрагмента кода взяты из учебника [Попробуйте службу приложений Azure с поиском Azure](search-tryappservice.md).

Сначала укажите выделение совпадений в качестве параметра поиска и задайте список полей, в которых будут проверяться совпадения. Укажите стиль HTML, который будет использоваться для выделения совпадений.

	// Set the Search parameters used when executing the search request
	     var sp = new SearchParameters
	{
	// Include a count of results in the query result
	     IncludeTotalResultCount = true,
	// Limit the results to 20 documents
	     Top = 20,
	// Enable hit-highlighting
	     HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
	     HighlightPreTag = "<b>",
	     HighlightPostTag = "</b>",
	};

Затем выполните последовательный перебор результатов поиска, чтобы найти строку, которая должна быть выделена. private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)

	  {
	     if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
	      {
	      string highlightedResult = string.Join("...", item.Highlights[fieldName]);
	      return new HtmlString(highlightedResult);
	      }
	      return new HtmlString(item.Document[fieldName].ToString());
	   }

И, наконец, задайте структуру отображения результатов поиска, указав набор результатов, который был получен в предыдущем фрагменте кода.

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


##Распространенные приемы программирования

Вы еще не знакомы с MVC, программированием .NET или интерфейсами REST API? В этих разделах приведено несколько приемов для быстрого начала работы.

###Шаблон MVC

Следующая таблица описывает использование компонентов шаблона MVC в приложениях, которые включают функцию поиска Azure. Если вы используете MVC 4 или MVC 5, код для интеграции службы поиска Azure, как правило, добавлен в эти модули.

Файл|Описание
----|-----------
Web.config|Укажите URL-адрес службы и ключ API. Добавьте в основной программный модуль ссылку на System.Configuration для считывания этих значений.
Program.cs|В основной программе настройте HttpClient или SearchServiceClient для подключения к службе. Добавьте в эту программу метод Search.
DataModel|Не используется. Если операции создания индекса и загрузки данных выполняются разными программами, для использования службы поиска Azure в веб-приложении не нужно использовать модели данных.
Views|Представление содержит HTML-код веб-страницы приложения, начиная от поля ввода условий поиска и заканчивая динамическим HTML для обработки результатов поиска.
Controllers;|За формирование запросов и обработку ошибок обычно отвечает файл HomeContoller.cs. Контроллер должен включать по меньше мере метод поиска, который получает полученные от службы поиска Azure результаты и передает набор результатов в представление. 

Если вы используете метод поиска предложений для автозавершения запросов, следует добавить специальный метод. Этот метод возвращает предлагаемые запросы в зависимости от того, содержит ли ваш индекс значения, соответствующие символам вводимого пользователем условия поиска.

###Когда следует использовать клиентские библиотеки .NET, а когда — Интерфейс REST API

Для приложений ASP.NET клиентская библиотека .NET будет оптимальным выбором, поскольку она устанавливает подключение HTTP, а также выполняет сериализацию и десериализацию JSON, что упрощает код.

В некоторых случаях выбор API может определяться сравнением функций двух имеющихся подходов. Как правило, [клиентская библиотека .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) и [REST API службы](https://msdn.microsoft.com/library/azure/dn798935.aspx) полностью взаимозаменяемы, если в них реализованы нужные вам операции. Тем не менее иногда новые функции сначала появляются в REST API в составе предварительной версии. В библиотеку .NET они добавляются на несколько месяцев позже. Например, индексаторы, которые используются для автоматизации операций загрузки данных из некоторых типов источников данных, сначала появились в предварительной версии REST API, и только через несколько месяцев — в клиентской библиотеке. Все ограничения по реализации функций указаны в соответствующей документации.

###Включение AzureSearchHelper.cs в REST API для сериализации и десериализации JSON

В отличие от библиотеки .NET, которая осуществляет сериализацию и десериализацию документов JSON при обмене запросами и ответами со службой, использование REST API предусматривает выполнение этого шага пользователями. Формат JSON используется для передачи полезных данных при загрузке или обновлении документов в индексе.

Код для сериализации JSON можно найти в нескольких примерах в файле с именем **AzureSearchHelper.cs**.

	using System;
	using System.Net.Http;
	using System.Text;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Converters;
	using Newtonsoft.Json.Serialization;
	
	namespace CatalogCommon
	{
	    public class AzureSearchHelper
	    {
	        public const string ApiVersionString = "api-version=2014-07-31-Preview";
	
	        private static readonly JsonSerializerSettings _jsonSettings;
	
	        static AzureSearchHelper()
	        {
	            _jsonSettings = new JsonSerializerSettings
	            {
	                Formatting = Formatting.Indented, // for readability, change to None for compactness
	                ContractResolver = new CamelCasePropertyNamesContractResolver(),
	                DateTimeZoneHandling = DateTimeZoneHandling.Utc
	            };
	
	            _jsonSettings.Converters.Add(new StringEnumConverter());
	        }
	
	        public static string SerializeJson(object value)
	        {
	            return JsonConvert.SerializeObject(value, _jsonSettings);
	        }
	
	        public static T DeserializeJson<T>(string json)
	        {
	            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
	        }
	
	        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
	        {
	            UriBuilder builder = new UriBuilder(uri);
	            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
	            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;
	
	            var request = new HttpRequestMessage(method, builder.Uri);
	
	            if (json != null)
	            {
	                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
	            }
	
	            return client.SendAsync(request).Result;
	        }
	
	        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
	        {
	            if (!response.IsSuccessStatusCode)
	            {
	                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
	                throw new Exception("Search request failed: " + error);
	            }
	        }
	    }
	}

###Правильная организация кода

Разделение рабочих нагрузок на автономные проекты в рамках одного решения Visual Studio обеспечивает гибкость в выборе методов разработки, поддержки и выполнения отдельных программ. Мы предлагаем использовать три подхода:

- код создания индексов;
- код приема данных;
- код взаимодействия с пользователем.

В службе поиска Azure операции индексирования и работы с документами (например, добавления или обновления документов или выполнения запросов) полностью независимы друг от друга. Это означает, что вы можете вынести управление индексами из кода ASP.NET, который взаимодействует с пользователем, формирует поисковые запросы и отображает результаты.

В большинстве наших примеров кода индекс создается и загружается в отдельном проекте (в разных примерах он называется DataIndexer, CatalogIndexer или DataCatalog). В то же время код, который обрабатывает поисковые запросы и ответы, помещается в проект приложения MVC ASP.NET. При изучении примеров кода удобно совмещать создание индекса и отправку документа в рамках одного проекта, но в рабочем коде лучше разделять эти операции. После создания индекс редко изменяется (и в зависимости от изменения может потребоваться его перестроение), тогда как документы обычно обновляются регулярно.

Разделение рабочей нагрузки дает и другие преимущества. Например, вы можете выбирать уровни разрешений для службы поиска Azure (полные права администратора или права только для запросов), использовать различные языки программирования, более точно указывать зависимости для каждой программы, а также изменять программы независимо друг от друга или создавать несколько приложений внешнего интерфейса, которые используют один индекс, а также строятся и поддерживаются центральным приложением индексирования.

##Дальнейшие действия

Чтобы узнать больше о функциях службы поиска Azure и ее интеграции в ASP.NET, изучите следующие статьи.

- [Использование Поиска Azure в приложении .NET](search-howto-dotnet-sdk.md) 
- [Пример разработки решения в службе "Поиск Azure"](search-dev-case-study-whattopedia.md)
- [Типовая процедура разработки Поиска Azure](search-workflow.md) 

<!---HONumber=Oct15_HO4-->