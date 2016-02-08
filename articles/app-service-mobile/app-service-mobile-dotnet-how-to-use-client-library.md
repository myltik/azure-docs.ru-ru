<properties
	pageTitle="Работа с управляемой клиентской библиотекой мобильных приложений службы приложений (Windows | Xamarin) | Microsoft Azure"
	description="Узнайте, как использовать клиент .NET для мобильных приложений службы приложений Azure с приложениями Windows и Xamarin."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/20/2016" 
	ms.author="glenga"/>

# Использование управляемого клиента для мобильных приложений Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Обзор 

В этом руководстве показано, как реализовать типичные сценарии с использованием управляемой клиентской библиотеки для мобильных приложений службы приложений Azure в приложениях Windows и Xamarin. Если вы не знакомы с мобильными приложениями, сначала мы рекомендуем прочитать [краткое руководство по мобильным приложениям](app-service-mobile-windows-store-dotnet-get-started.md). В данном руководстве мы сосредоточимся на управляемом пакете SDK клиентской части. Дополнительные сведения о серверных пакетах SDK для мобильных приложений см. в разделе [Работа с серверными пакетами SDK для .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) или [Использование серверного пакета SDK для Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Справочная документация

Справочная документация по пакету SDK клиента находится здесь: [Справочник по клиенту .NET мобильных приложений Azure](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.aspx).

##<a name="setup"></a>Настройка и необходимые компоненты

Предполагается, что вы уже создали и опубликовали проект внутреннего сервера мобильных приложений, который включает одну таблицу. В коде, который используется в этом разделе, применяется таблица `TodoItem`, которая содержит следующие столбцы: `Id`, `Text` и `Complete`. Это та же таблица, которая была создана при выполнении [краткого учебника](app-service-mobile-windows-store-dotnet-get-started.md).

Соответствующий типизированный тип на стороне клиента в C# выглядит следующим образом:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Обратите внимание, что атрибут [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) используется для определения сопоставления *PropertyName* между типом клиента и таблицей.

Сведения о создании новых таблиц в серверной части мобильных приложений см. в разделах [Практическое руководство. Определение контроллера таблиц](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (серверная часть .NET) или [Определение таблицы с помощью динамической схемы](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (серверная часть Node.js). Для серверной части Node.js можно использовать параметр **Простые таблицы** на [портале Azure](https://portal.azure.com/).

##<a name="create-client"></a>Практическое руководство. Создание клиента мобильных приложений

В следующем коде создается объект `MobileServiceClient`, который используется для доступа к серверной части мобильных приложений.

	MobileServiceClient client = new MobileServiceClient(
	"MOBILE_APP_URL");

В приведенном выше коде замените `MOBILE_APP_URL` URL-адресом серверной части мобильных приложений, который можно найти в колонке серверной части мобильных приложений на [портале Azure](https://portal.azure.com/).

##<a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу

Весь код, который открывает или изменяет данные в таблице серверной части, вызывает функции для объекта `MobileServiceTable`. Ссылку на таблицу можно получить, вызвав метод [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) для экземпляра `MobileServiceClient`:

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Это типизированная модель сериализации. Также поддерживается нетипизированная модель сериализации. Следующий код создает ссылку на нетипизированную таблицу:

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

В нетипизированных запросах необходимо указать соответствующую строку запроса OData.

##<a name="querying"></a>Практическое руководство. Запрос данных из мобильного приложения

В этом разделе показано, как отправлять запросы к внутреннему серверу мобильных приложений. Описаны следующие функциональные возможности:

- [Фильтрация возвращаемых данных]
- [Сортировка возвращаемых данных]
- [Возврат данных на страницах]
- [Выбор определенных столбцов]
- [Поиск данных по идентификатору]

>[AZURE.NOTE] Для предотвращения возврата всех строк принудительно применяется размер страницы, управляемый сервером. Это предотвращает негативное воздействие больших наборов данных на функционирование службы. Для возвращения более 50 строк используйте метод `Take`, как описано в разделе [Возвращение данных на страницах].

### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий код иллюстрирует способ фильтрации данных путем включения предложения `Where` в запрос. Он возвращает все элементы таблицы `todoTable`, свойство `Complete` которых равно `false`. Функция `Where` применяет в запросе к таблице предикат фильтрации строк.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Универсальный код ресурса (URI) запроса, отправленного в серверную часть, можно просмотреть с помощью программы проверки сообщений, такой как средства разработчика браузера или [Fiddler]. Если взглянуть на следующий URI запроса, можно отметить, что мы изменяем саму строку запроса:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

На стороне Azure такой запрос обычно должен преобразовываться примерно в следующий запрос SQL:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Функция, которая передается в метод `Where`, может включать произвольное число условий. Например, следующая строка:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

Будет преобразована (для того же запроса, приведенного выше) примерно в

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

Инструкция `where` выше будет находить элементы, для состояния `Complete` которых задано значение "Ложь", со значением `Text`, не равным NULL.

Это же выражение можно написать с использованием нескольких строк:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Эти два способа эквивалентны и могут быть взаимозаменяемыми. Первый вариант, объединение нескольких предикатов в одном запросе, является более компактным и рекомендуемым.

Предложение `where` поддерживает операции, которые будут преобразованы в подмножество OData. К ним относятся операторы сравнения (==, !=, <, <=, >, >=), арифметические операторы (+, -, /, *, %), методы, связанные с точностью чисел (Math.Floor, Math.Ceiling), строковые функции (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), свойства даты (Year, Month, Day, Hour, Minute, Second), свойства доступа к объекту, а также сочетания перечисленных операторов.

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

В следующем коде показано, как сортировать данные, включив в запрос функцию `OrderBy` или `OrderByDescending` Он возвращает элементы таблицы `todoTable`, упорядочивая их по возрастанию значений в поле `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах

По умолчанию внутренний сервер возвращает только первые 50 строк. Число возвращенных строк можно увеличить путем вызова метода [Take]. Чтобы запросить определенную "страницу" общего набора данных, возвращенного запросом, используйте метод `Take` вместе с методом [Skip]. При выполнении следующего запроса будут возвращены три главных элемента в таблице.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Следующий измененный запрос пропускает первые три результата и после этого возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы составляет три элемента.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Можно также использовать метод [IncludeTotalCount], чтобы запрос возвращал общее количество <i>всех</i> записей, которые должны быть возвращены, без учета указанных предложений paging/limit:

	query = query.IncludeTotalCount();

Это упрощенный сценарий передачи жестко запрограммированных значений подкачки в методы `Take` и `Skip`. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице.

>[AZURE.NOTE]Чтобы переопределить ограничение серверной части мобильных приложений в 50 строк, необходимо также применить класс [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) к общедоступному методу GET и настроить подкачку. В этом случае максимальное количество возвращаемых строк увеличивается до 1000:

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

Добавив в запрос предложение `Select`, можно задать набор свойств, который будет включен в возвращаемые результаты. Например, в следующем коде показано, как выбрать только одно поле, а также способы выбора и форматирования нескольких полей:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", 
						todoItem.Text.PadRight(30), todoItem.Complete ? 
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Все описанные функции являются суммируемыми, поэтому каждая последующая вызываемая функция будет продолжать влиять на запрос. Еще один пример:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Практическое руководство. Поиск данных по идентификатору

Функцию `LookupAsync` можно использовать для поиска в базе данных объектов с определенным идентификатором.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="lookingup"></a>Практическое руководство. Выполнение нетипизированных запросов

При выполнении запроса с помощью объекта нетипизированной таблицы необходимо явно указать строку запроса OData, как показано в следующем примере:

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Вы будете получать значения JSON, которые можно использовать в качестве контейнера свойств. Более подробные сведения о JToken и Json.NET см. в разделе [Json.NET](http://json.codeplex.com/)

##<a name="inserting"></a>Практическое руководство. Вставка данных в серверную часть мобильных приложений

Все типы клиентов должны содержать член с именем **Id** (идентификатор), который по умолчанию является строкой. Этот **идентификатор** необходим для выполнения операций CRUD и работы вне сети. В следующем коде показано, как вставить новые строки в таблицу. Параметр содержит данные, которые вставляются в качестве объекта .NET.

	await todoTable.InsertAsync(todoItem);

Если уникальный настраиваемый идентификатор не включен в `todoItem` при передаче этого объекта в вызов `todoTable.InsertAsync`, значение идентификатора будет создано сервером в объекте `todoItem`, возвращенном клиенту.

Для вставки нетипизированных данных можно использовать Json.NET, как показано ниже.

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Вот пример использования адреса электронной почты в качестве уникального строкового идентификатора.

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);


###Работа со значениями идентификаторов

Мобильные приложения поддерживают уникальные настраиваемые строковые значения для столбца **Id** таблицы. Это позволяет приложениям использовать в качестве идентификатора настраиваемые значения, такие как адреса электронной почты или имена пользователей.

Строковые идентификаторы предоставляют следующие преимущества.

+ Идентификаторы создаются без обмена данными с базой данных.
+ Можно легко объединять записи из разных таблиц или баз данных.
+ Значения идентификаторов можно удобно интегрировать с логикой приложения.

Если для вставленной записи не задано значение строкового идентификатора, внутренний сервер мобильных приложений создает уникальное значение для идентификатора. Для создания собственных значений идентификатора в клиенте или серверной части можно использовать метод `Guid.NewGuid()`.

##<a name="modifying"></a>Практическое руководство. Изменение данных в серверной части мобильных приложений

Следующий код показывает, как добавить новую информацию в существующий экземпляр с тем же идентификатором. Параметр содержит данные, которые обновляются в качестве объекта .NET.

	await todoTable.UpdateAsync(todoItem);

Для вставки нетипизированных данных можно использовать Json.NET следующим образом: 
	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Привет всем");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Обратите внимание, что при выполнении обновления необходимо указать идентификатор. Таким образом внутренний сервер определяет, какой экземпляр нужно обновить. Идентификатор можно получить из результатов вызова метода `InsertAsync`. При попытке обновить элемент без предоставления значения Id создается исключение `ArgumentException`.


##<a name="deleting"></a>Практическое руководство. Удаление данных в серверной части мобильных приложений

Следующий код показывает, как удалить существующий экземпляр. Экземпляр идентифицируется по полю Id, заданному в свойстве `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Для удаления нетипизированных данных можно использовать Json.NET следующим образом:

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Обратите внимание, что при создании запроса на удаление необходимо указать идентификатор. Другие свойства не передаются в службу или игнорируются ею. В результате вызова функции `DeleteAsync` обычно возвращается значение `null`. Идентификатор для передачи можно получить в результате вызова метода `InsertAsync`. При попытке удалить элемент без предоставления значения Id серверная часть возвращает исключение `MobileServiceInvalidOperationException`.

##<a name="#custom-api"></a>Практическое руководство. Вызов настраиваемого интерфейса API

Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API можно вызвать путем вызова одной из перегрузок метода [InvokeApiAsync] для клиента. Например, следующая строка кода отправляет запрос POST API-интерфейсу **completeAll** в серверную часть:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Обратите внимание, что это вызов типизированного метода, для которого требуется определить возвращаемый тип **MarkAllResult**. Поддерживаются типизированные и нетипизированные методы. Это предельно простой пример, поскольку он типизированный, не отправляет полезных данных, не содержит параметров запроса и не меняет заголовков запроса. Практические примеры и дополнительную информацию о методе [InvokeApiAsync] см. в разделе [Custom API in Azure Mobile Services Client SDKs] (Настраиваемый интерфейс API в пакетах SDK для клиента мобильных служб Azure).

##Практическое руководство. Регистрация для получения push-уведомлений

Клиент мобильных приложений позволяет выполнить регистрацию для получения push-уведомлений с помощью центров уведомлений Azure. При регистрации вы получаете маркер из службы push-уведомлений (PNS). Это значение необходимо указать при регистрации вместе со всеми тегами. Следующий код регистрирует ваше приложение Windows для получения push-уведомлений через службу уведомлений Windows (WNS):

		private async void InitNotificationsAsync()
		{
		    // Request a push notification channel.
		    var channel =
		        await PushNotificationChannelManager
		            .CreatePushNotificationChannelForApplicationAsync();

		    // Register for notifications using the new channel and a tag collection.
			var tags = new List<string>{ "mytag1", "mytag2"};
		    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
		}

Обратите внимание, что в этом примере в регистрацию включены два тега. Дополнительные сведения о приложениях Windows, включая способ регистрации для выполнения регистраций шаблонов, см. в разделе [Добавление push-уведомлений в приложение](app-service-mobile-windows-store-dotnet-get-started-push.md).

Приложениям Xamarin требуется дополнительный код для регистрации приложения, работающего под управлением iOS или Android, в Службе push-уведомлений Apple (APNS) и службе Google Cloud Messaging (GCM) соответственно. Более подробные сведения об этом см. в статье **Добавление push-уведомлений к приложению** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).

## Использование шаблонов для отправки кроссплатформенных уведомлений выполнившим проверку подлинности пользователям

Для регистрации шаблонов просто передайте их в клиентское приложение с помощью метода **MobileService.GetPush().RegisterAsync()**.

        MobileService.GetPush().RegisterAsync(channel.Uri, newTemplates());

Шаблоны будут относиться к типу JObject и содержать несколько шаблонов в следующем формате JSON:

        public JObject newTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";
            
            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

Метод **RegisterAsync()** принимает также вспомогательные плитки:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Обратите внимание, что все теги будут удалены для безопасности. В статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure] показано, как добавлять теги для установки устройства или шаблоны в пределах установки.

Для отправки уведомлений с использованием зарегистрированных шаблонов используйте [API концентраторов уведомлений](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="optimisticconcurrency"></a>Практическое руководство. Использование оптимистичного параллелизма

В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. При *управлении оптимистичным параллелизмом* предполагается, что каждая транзакция может фиксироваться, поэтому не использует блокировки каких-либо ресурсов. Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается.

Мобильные приложения поддерживают управление оптимистичным параллелизмом за счет отслеживания изменений в каждом элементе с помощью столбца системных свойств `__version`, определенного для каждой таблицы в серверной части мобильных приложений. При каждом обновлении записи мобильные приложения задают новое значение свойства `__version` для этой записи. При обработке каждого запроса на обновление свойство `__version` записи, включенное в запрос, сравнивается с тем же свойством записи на сервере. Если версия, переданная с запросом, не соответствует серверной части, клиентская библиотека создает исключение `MobileServicePreconditionFailedException<T>`. Тип, включенный в исключение, является записью с внутреннего сервера, которая содержит версию записи на сервере. Затем приложение может использовать эти данные, чтобы решить, следует ли повторно выполнить полученный из серверной части запрос изменения с правильным значением `__version` для фиксации изменений.

Чтобы включить оптимистичный параллелизм, приложение определяет столбец в классе таблицы для системного свойства `__version`. В следующем определении приведен пример.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


Приложения, в которых используются нетипизированные таблицы, включают оптимистичный параллелизм, устанавливая флаг `Version` в таблице `SystemProperties` следующим образом.

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


В следующем коде показано, как разрешить конфликт записи после его определения. Правильное значение `__version` должно быть включено в вызов `UpdateAsync()` для фиксации разрешения.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

Дополнительные сведения см. в статье [Автономная синхронизация данных в мобильных приложениях Azure](app-service-mobile-offline-data-sync.md).


##<a name="binding"></a>Практическое руководство. Привязка данных мобильных приложений к пользовательскому интерфейсу Windows

В этом разделе показано, как отображать возвращенные объекты данных с использованием элементов пользовательского интерфейса в приложении Windows. Для запроса незавершенных элементов в таблице `todoTable` и отображения его в простом списке необходимо выполнить следующий пример кода, чтобы привязать источник к списку с помощью запроса. С помощью `MobileServiceCollection` создается коллекция привязок, поддерживающая мобильные приложения.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Некоторые элементы управления в управляемой среде выполнения Windows поддерживают интерфейс [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Этот интерфейс позволяет элементам управления запрашивать дополнительные данные во время прокрутки, выполняемой пользователем. Для универсальных приложений для Windows 8.1 предусмотрена встроенная поддержка этого интерфейса через класс `MobileServiceIncrementalLoadingCollection`, который автоматически обрабатывает вызовы из элементов управления. Чтобы использовать класс `MobileServiceIncrementalLoadingCollection` в приложениях для Windows, выполните следующее:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Чтобы использовать новую коллекцию в приложениях для Windows Phone 8 и Silverlight, используйте методы расширения `ToCollection` в интерфейсах `IMobileServiceTableQuery<T>` и `IMobileServiceTable<T>`. Чтобы фактически загрузить данные, вызовите метод `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

При использовании коллекции, созданной путем вызова метода `ToCollectionAsync` или `ToCollection`, вы получите коллекцию, которую можно привязать к элементам управления пользовательским интерфейсом. Эта коллекция поддерживает разбиение по страницам, то есть элемент управления может запросить в коллекции загрузку дополнительных элементов, что и будет выполнено коллекцией для этого элемента. На этом этапе пользовательский код не участвует, элемент управления запустит поток. Однако так как коллекция загружает данные из сети, ожидается, что иногда эта загрузка будет завершаться со сбоем. Чтобы обработать подобные ошибки, можно переопределить метод `OnException` класса `MobileServiceIncrementalLoadingCollection` для обработки исключений, возникающих в результате вызова элементами управления метода `LoadMoreItemsAsync`.

Наконец, представьте, что в таблице содержится множество полей, однако необходимо отобразить только те из них, которые находятся под вашим управлением. С помощью инструкций в разделе [Выбор конкретных столбцов](#selecting) выше можно выбрать столбцы, отображаемые в пользовательском интерфейсе.

## <a name="adal"></a>Практическое руководство: проверка подлинности пользователей с помощью библиотеки проверки подлинности Active Directory

Библиотеку проверки подлинности Active Directory (ADAL) можно использовать для входа пользователей в приложение с помощью Azure Active Directory. Этот подход является более предпочтительным, чем использование методов `loginAsync()`, так как он обеспечивает более удобный интерфейс входа для пользователя и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью AAD, следуя инструкциям в руководстве [Настройка приложения службы приложений для использования службы входа Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Обязательно выполните дополнительный этап регистрации собственного клиентского приложения.

2. В Visual Studio или Xamarin Studio откройте проект и добавьте ссылку на пакет NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory`. Во время поиска включите предварительные версии.

3. Добавьте в приложение код, соответствующий используемой платформе. Код находится ниже. В каждом коде выполните следующие замены.

* Замените текст **INSERT-AUTHORITY-HERE** именем клиента, в котором вы подготовили свое приложение. Используйте следующий формат: https://login.windows.net/contoso.onmicrosoft.com. Это значение можно скопировать на вкладке "Домен" в Azure Active Directory на [классическом портале Azure].

* Замените текст **INSERT-RESOURCE-ID-HERE** идентификатором клиента для серверной части мобильного приложения. Это значение можно скопировать на вкладке **Дополнительно** в разделе **Параметры Azure Active Directory** на портале.

* Замените текст **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

* Замените текст **INSERT-REDIRECT-URI-HERE** конечной точкой вашего сайта _/.auth/login/done_, используя схему HTTPS. Это значение должно быть аналогично _https://contoso.azurewebsites.net/.auth/login/done_.

Ниже приведены колы для каждой платформы.

**Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

**Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
			{
				AuthenticationContext ac = new AuthenticationContext(authority);
				AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
				JObject payload = new JObject();
				payload["access_token"] = ar.AccessToken;
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
			}
			catch (Exception ex)
			{
				Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
			}
        }

**Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
			{
				AuthenticationContext ac = new AuthenticationContext(authority);
				AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
				JObject payload = new JObject();
				payload["access_token"] = ar.AccessToken;
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
			}
			catch (Exception ex)
			{
				AlertDialog.Builder builder = new AlertDialog.Builder(this);
				builder.SetMessage(ex.Message);
				builder.SetTitle("You must log in. Login Required");
				builder.Create().Show();
			}
        }
		protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
		{
			base.OnActivityResult(requestCode, resultCode, data);
			AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
		}

## <a name="package-sid"></a>Практическое руководство. Получение SID пакета Магазина Windows

Приложениям Windows ИД безопасности пакета необходим для включения push-уведомлений и определенных режимов проверки подлинности. Для получения этого значения выполните следующие действия:

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения для Магазина Windows, щелкните **Магазин**, а затем — **Связать приложение с Магазином…**.
2. В окне мастера нажмите кнопку **Далее**, выполните вход с помощью учетной записи Майкрософт, введите имя приложения в поле **Зарезервировать новое имя приложения** и нажмите кнопку **Зарезервировать**.
3. После успешного создания регистрации приложения выберите новое имя приложения, нажмите кнопку **Далее**, а затем кнопку **Связать**. Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.
4. Выполните вход в [Центр разработки для Windows](https://dev.windows.com/ru-RU/overview) с использованием учетной записи Майкрософт. В разделе **Мои приложения** щелкните только что созданную регистрацию приложения.
5. Щелкните **Управление приложениями** > **Удостоверение приложения**, а затем выполните прокрутку вниз, чтобы найти ваш **ИД безопасности пакета**.

Во многих случаях ИД безопасности пакета рассматривается как URI, при этом необходимо использовать _ms-app://_ в качестве схемы. Запишите версию ИД безопасности пакета, получаемую путем сцепления этого значения в качестве префикса.

<!--- We want to just point to the authentication topic when it's done
##<a name="authentication"></a>How to: Authenticate users

Mobile Apps supports authenticating and authorizing app users using a variety of external identity providers: Facebook, Google, Microsoft Account, Twitter, and Azure Active Directory. You can set permissions on tables to restrict access for specific operations to only authenticated users. You can also use the identity of authenticated users to implement authorization rules in server scripts. For more information, see the tutorial [Add authentication to your app].

Two authentication flows are supported: a _server flow_ and a _client flow_. The server flow provides the simplest authentication experience, as it relies on the provider's web authentication interface. The client flow allows for deeper integration with device-specific capabilities as it relies on provider-specific device-specific SDKs.

###Server flow
To have App Service manage the authentication process in your Windows apps,
you must register your app with your identity provider. Then in your mobile App backed, you need to configure the application ID and secret provided by your provider. For more information, see the tutorial [Add authentication to your app].

Once you have registered your identity provider, simply call the [LoginAsync method] with the [MobileServiceAuthenticationProvider] value of your provider. For example, the following code initiates a server flow sign-in by using Facebook.

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

If you are using an identity provider other than Facebook, change the value of [MobileServiceAuthenticationProvider] above to the value for your provider.

In this case, App Service manages the OAuth 2.0 authentication flow by displaying the sign-in page of the selected provider and generating an App Service authentication token after successful sign-on with the identity provider. The [LoginAsync method] returns a [MobileServiceUser], which provides both the [userId] of the authenticated user and the [MobileServiceAuthenticationToken], as a JSON web token (JWT). This token can be cached and re-used until it expires. For more information, see [Caching the authentication token].

###Client flow

Your app can also independently contact the identity provider and then provide the returned token to App Service for authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional user data from the identity provider.

####Single sign-in using a token from Facebook or Google

In the most simplified form, you can use the client flow as shown in this snippet for Facebook or Google.

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}


####Single sign-in using Microsoft Account with the Live SDK

To be able to authenticate users, you must register your app at the Microsoft account Developer Center. You must then connect this registration with your Mobile App backend. Complete the steps in [Register your app to use a Microsoft account login](mobile-services-how-to-register-microsoft-authentication.md) to create a Microsoft account registration and connect it to your Mobile App backend. If you have both Windows Store and Windows Phone 8/Silverlight versions of your app, register the Windows Store version first.

The following code authenticates using Live SDK and uses the returned token to sign-in to your Mobile App backend. 

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope is requested.
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }


###<a name="caching"></a>Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. You can use [PasswordVault] for Windows Store apps to cache the current user identity the first time they log in and every subsequent time you check whether you already have the user identity in our cache. When the cache is empty, you still need to send the user through the login process.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}

	 // Log out
	client.Logout();
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


For Windows Phone apps, you may encrypt and cache data using the [ProtectedData] class and store sensitive information in isolated storage.

-->

##<a name="errors"></a>Практическое руководство. Обработка ошибок

Следующий пример демонстрирует обработку исключения, возвращенного внутренним сервером.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}


##<a name="unit-testing"></a>Практическое руководство. Разработка модульных тестов

Функция `MobileServiceClient.GetTable` и запросы возвращают значение, которое является интерфейсом. Из-за этого их легко критиковать с точки зрения целесообразности тестирования, поэтому вы можете создать `MyMockTable : IMobileServiceTable<TodoItem>` для реализации своей логики тестирования.

##<a name="customizing"></a>Практическое руководство. Настройка клиента

В этом разделе показано, как настроить заголовки запроса, а также сериализацию объектов JSON в ответе.

### <a name="headers"></a>Практическое руководство. Настройка заголовков запроса

Для поддержки определенного сценария приложения вам может потребоваться настроить связь с внутренним сервером мобильных приложений. Например, может возникнуть необходимость добавлять настраиваемый заголовок к каждому исходящему запросу или даже изменять коды состояний ответов. Для этого нужно предоставить настраиваемый метод [DelegatingHandler], как показано в следующем примере:

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl", "", "",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage> 
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

Этот код добавляет новый заголовок **x-my-header** в запрос и произвольно задает для кода ответа значение "Недоступно". В реальном сценарии код состояния ответа задается с учетом настраиваемой логики, необходимой для приложения.

### <a name="serialization"></a>Практическое руководство. Настройка сериализации

Клиентская библиотека мобильных приложений использует Json.NET для преобразования ответа JSON в объекты .NET на стороне клиента. Вы можете настроить параметры сериализации между типами .NET и JSON в сообщениях. Класс [MobileServiceClient] предоставляет свойство `SerializerSettings` типа [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm).

С помощью этого свойства вы можете установить одно из многих свойств Json.NET, например:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Это свойство преобразует все свойства в нижний регистр во время сериализации.

<!-- Anchors. -->
[Фильтрация возвращаемых данных]: #filtering
[Сортировка возвращаемых данных]: #sorting
[Возврат данных на страницах]: #paging
[Возвращение данных на страницах]: #paging
[Выбор определенных столбцов]: #selecting
[Поиск данных по идентификатору]: #lookingup

<!-- Images. -->



<!-- URLs. -->
[Add authentication to your app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[Optimistic Concurrency Tutorial]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[MobileServiceClient]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Custom API in Azure Mobile Services Client SDKs]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx

<!---HONumber=AcomDC_0128_2016-->