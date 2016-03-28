<properties
	pageTitle="Работа с управляемой клиентской библиотекой мобильных приложений службы приложений (Windows | Xamarin) | Microsoft Azure"
	description="Узнайте, как использовать клиент .NET для мобильных приложений службы приложений Azure с приложениями Windows и Xamarin."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="glenga"/>

# Использование управляемого клиента для мобильных приложений Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием управляемой клиентской библиотеки для мобильных приложений службы приложений Azure в приложениях Windows и Xamarin. Если вы не знакомы с мобильными приложениями, рекомендуем сначала прочитать [Краткое руководство по мобильным приложениям Azure]. В данном руководстве мы сосредоточимся на управляемом пакете SDK клиентской части. Дополнительные сведения о пакетах SDK серверной части для мобильных приложений см. в методических указаниях для [пакета SDK для серверной части .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) или [пакета SDK для серверной части Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Справочная документация

Справочная документация по пакету SDK клиента находится здесь: [Справочник по клиенту .NET мобильных приложений Azure]. Несколько примеров клиентов доступны в [репозитории GitHub "Примеры для Azure"].

##<a name="setup"></a>Настройка и необходимые компоненты

Предполагается, что вы уже создали и опубликовали проект внутреннего сервера мобильных приложений, который включает одну таблицу. В коде, который используется в этом разделе, применяется таблица `TodoItem`, которая содержит следующие столбцы: `Id`, `Text` и `Complete`. Это та же таблица, которая была создана при выполнении [Краткого руководства по мобильным приложениям Azure].

Соответствующий типизированный тип на стороне клиента в C# выглядит следующим образом:

	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Обратите внимание, что атрибут [JsonPropertyAttribute] используется для определения сопоставления *PropertyName* между типом клиента и таблицей.

Чтобы научиться создавать новые таблицы в серверной части мобильных приложений, обратитесь к [Методическим указаниям для пакета SDK серверной части .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) или [Методическим указаниям для пакета SDK серверной части Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-dynamicschema). При создании серверной части мобильного приложения на портале Azure с помощью краткого руководства можно использовать **Простые таблицы** в [портале Azure].

###<a name="symbolsource"></a>Практическое руководство: работа с символами отладки в Visual Studio

Символы для пространства имен Microsoft.Azure.Mobile доступны на [SymbolSource]. Обратитесь к [инструкциям по SymbolSource], чтобы интегрировать SymbolSource с Visual Studio.

##<a name="create-client"></a>Создание клиента мобильных приложений

Следующий код создает объект [MobileServiceClient], используемый для доступа к серверной части мобильного приложения.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

В приведенном выше коде замените `MOBILE_APP_URL` URL-адресом серверной части мобильных приложений, который можно найти в колонке серверной части мобильных приложений на [портале Azure]. Рекомендуется (и это нормально), чтобы экземпляр клиента был одноэлементным.

## Работа с таблицами

Ниже подробно описано, как выполнить поиск, извлечение записей и изменение данных в таблице. В этой статье содержатся следующие разделы.

* [Создание ссылки на таблицу](#instantiating)
* [Запрос данных](#querying)
* [Фильтрация возвращаемых данных](#filtering)
* [Сортировка возвращаемых данных](#sorting)
* [Возврат данных на страницах](#paging)
* [Выбор определенных столбцов](#selecting)
* [Поиск записи по идентификатору](#lookingup)
* [Работа с нетипизированными запросами](#untypedqueries)
* [Вставка данных](#inserting)
* [Обновление данных](#updating)
* [Удаление данных](#deleting)
* [Разрешение конфликтов и оптимистичный параллелизм](#optimisticconcurrency)
* [Привязка к пользовательскому интерфейсу Windows](#binding)

###<a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу

Весь код, который открывает или изменяет данные в таблице серверной части, вызывает функции для объекта `MobileServiceTable`. Ссылку на таблицу можно получить, вызвав метод [GetTable] для экземпляра `MobileServiceClient`:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

Это типизированная модель сериализации. Также поддерживается нетипизированная модель сериализации. Следующий код [создает ссылку на нетипизированную таблицу]\:

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

В нетипизированных запросах необходимо указать соответствующую строку запроса OData.

###<a name="querying"></a>Практическое руководство. Запрос данных из мобильного приложения

В этом разделе показано, как отправлять запросы к внутреннему серверу мобильных приложений. Описаны следующие функциональные возможности:

- [Фильтрация возвращаемых данных]
- [Сортировка возвращаемых данных]
- [Возврат данных на страницах]
- [Выбор определенных столбцов]
- [Поиск данных по идентификатору]

>[AZURE.NOTE] Для предотвращения возврата всех строк принудительно применяется размер страницы, управляемый сервером. Это предотвращает негативное воздействие больших наборов данных на функционирование службы. Для возвращения более 50 строк используйте метод `Take`, как описано в разделе [Возвращение данных на страницах].

###<a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий код иллюстрирует способ фильтрации данных путем включения предложения `Where` в запрос. Он возвращает все элементы таблицы `todoTable`, свойство `Complete` которых равно `false`. Функция [Where] применяет предикат фильтрации строк для запросов к таблице.

	// This query filters out completed TodoItems and items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Универсальный код ресурса (URI) запроса, отправленного в серверную часть, можно просмотреть с помощью программы проверки сообщений, такой как средства разработчика браузера или [Fiddler]. Если взглянуть на следующий URI запроса, можно отметить, что сама строка запроса изменена:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Этот запрос OData с помощью пакета SDK сервера преобразуется в SQL-запрос, который напоминает следующий:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Функция, которая передается в метод `Where`, может включать произвольное число условий. Например, следующая строка:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
	   .ToListAsync();

С помощью пакета SDK сервера она преобразуется в SQL-запрос, который напоминает следующий:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

Инструкция `WHERE` выше будет находить элементы, для состояния `Complete` которых задано значение "Ложь", со значением `Text`, не равным NULL.

Этот запрос также можно разбить на несколько предложений:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Эти два способа эквивалентны и могут быть взаимозаменяемыми. Первый вариант, объединение нескольких предикатов в одном запросе, является более компактным и рекомендуемым.

Предложение `Where` поддерживает операции, которые будут преобразованы в подмножество OData. К ним относятся операторы сравнения (==, !=, <, <=, >, >=), арифметические операторы (+, -, /, *, %), методы, связанные с точностью чисел (Math.Floor, Math.Ceiling), строковые функции (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), свойства даты (Year, Month, Day, Hour, Minute, Second), свойства доступа к объекту, а также сочетания перечисленных операторов. Чтобы узнать, что поддерживает пакет SDK для сервера, обратитесь к [документации по OData версии 3].

###<a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

В следующем коде показано, как сортировать данные, включая в запрос функцию [OrderBy] или [OrderByDescending]. Он возвращает элементы таблицы `todoTable`, упорядочивая их по возрастанию значений в поле `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Практическое руководство. Возврат данных на страницах

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

>[AZURE.NOTE]Чтобы переопределить ограничение серверной части мобильных приложений в 50 строк, необходимо также применить класс [EnableQueryAttribute] к общедоступному методу GET и настроить подкачку. В этом случае максимальное количество возвращаемых строк увеличивается до 1000:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

Набор свойств, который войдет в результаты, можно задать, добавив в запрос предложение [Select]. Например, в следующем коде показано, как выбрать только одно поле, а также способы выбора и форматирования нескольких полей:

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

Функцию [LookupAsync] можно использовать для поиска в базе данных объектов с определенным идентификатором.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Практическое руководство. Выполнение нетипизированных запросов

При выполнении запроса с помощью объекта нетипизированной таблицы необходимо явно указать строку запроса OData, вызвав функцию [ReadAsync], как показано в следующем примере:

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Вы будете получать значения JSON, которые можно использовать в качестве контейнера свойств. Более подробные сведения о JToken и Newtonsoft Json.NET см. на сайте [Json.NET].

### <a name="inserting"></a>Практическое руководство. Вставка данных в серверную часть мобильных приложений

Все типы клиентов должны содержать член с именем **Id** (идентификатор), который по умолчанию является строкой. Этот **идентификатор** необходим для выполнения операций CRUD и работы вне сети. В следующем коде показано, как вставить новые строки в таблицу с помощью метода [InsertAsync]. Параметр содержит данные, которые вставляются в качестве объекта .NET.

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

* Идентификаторы создаются без обмена данными с базой данных.
* Можно легко объединять записи из разных таблиц или баз данных.
* Значения идентификаторов можно удобно интегрировать с логикой приложения.

Если для вставленной записи не задано значение строкового идентификатора, внутренний сервер мобильных приложений создает уникальное значение для идентификатора. Для создания собственных значений идентификатора в клиенте или серверной части можно использовать метод [Guid.NewGuid].

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Практическое руководство. Изменение данных в серверной части мобильных приложений

Следующий код показывает, как обновить существующую запись с тем же идентификатором с помощью метода [UpdateAsync]. Параметр содержит данные, которые обновляются в качестве объекта .NET.

	await todoTable.UpdateAsync(todoItem);

Для добавления нетипизированных данных можно использовать [Json.NET] следующим образом:

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

При выполнении обновления необходимо указать поле `id`. Таким образом внутренний сервер определяет, какой экземпляр нужно обновить. Поле `id` можно получить из результатов вызова метода `InsertAsync`. При попытке обновить элемент без указания значения `id` выдается исключение `ArgumentException`.

###<a name="deleting"></a>Практическое руководство. Удаление данных в серверной части мобильных приложений

Следующий код показывает, как удалить существующий экземпляр с помощью метода [DeleteAsync]. Экземпляр идентифицируется по полю `id`, заданному в свойстве `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Для удаления нетипизированных данных можно использовать Json.NET следующим образом:

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Обратите внимание, что при создании запроса на удаление необходимо указать идентификатор. Другие свойства не передаются в службу или игнорируются ею. В результате вызова функции `DeleteAsync` обычно возвращается значение `null`. Идентификатор для передачи можно получить в результате вызова метода `InsertAsync`. При попытке удалить элемент без указания значения `id` выдается исключение `MobileServiceInvalidOperationException`.

###<a name="optimisticconcurrency"></a>Практическое руководство. Разрешение конфликтов и оптимистичный параллелизм

Иногда два и более клиентов могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. При *управлении оптимистичным параллелизмом* предполагается, что каждая транзакция может фиксироваться, поэтому не использует блокировки каких-либо ресурсов. Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается.

Мобильные приложения поддерживают управление оптимистичным параллелизмом за счет отслеживания изменений в каждом элементе с помощью столбца системных свойств `version`, определенного для каждой таблицы в серверной части мобильных приложений. При каждом обновлении записи мобильные приложения задают новое значение свойства `version` для этой записи. При обработке каждого запроса на обновление свойство `\version` записи, включенное в запрос, сравнивается с тем же свойством записи на сервере. Если версия, переданная с запросом, не соответствует серверной части, клиентская библиотека выдает исключение `MobileServicePreconditionFailedException<T>`. Тип, включенный в исключение, является записью серверной части, которая содержит версию записи на сервере. Затем приложение может использовать эти данные, чтобы решить, следует ли повторно выполнить полученный из серверной части запрос изменения с правильным значением `version` для фиксации изменений.

Чтобы включить оптимистичный параллелизм, определите столбец в классе таблицы для системного свойства `version`, например:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public byte[] Version { set; get; }
    }


Приложения, в которых используются нетипизированные таблицы, включают оптимистичный параллелизм, устанавливая флаг `Version` в таблице `SystemProperties` следующим образом.

	//Enable optimistic concurrency by retrieving version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Наряду с включением оптимистичного параллелизма при вызове [UpdateAsync] также необходимо перехватывать исключение `MobileServicePreconditionFailedException<T>` в коде. Разрешите конфликт, применив правильный `version` к обновленной записи и вызвав [UpdateAsync] с разрешенной записью. В следующем коде показано, как разрешить конфликт записи после его обнаружения:

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
	    MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

Дополнительные сведения см. в разделе [Автономная синхронизация данных в мобильных приложениях Azure].

###<a name="binding"></a>Практическое руководство. Привязка данных мобильных приложений к пользовательскому интерфейсу Windows

В этом разделе показано, как отображать возвращенные объекты данных с использованием элементов пользовательского интерфейса в приложении Windows. Запустите следующий пример кода для привязки источника списка к запросу незавершенных элементов в `todoTable` и его отображения в простом списке. При использовании [MobileServiceCollection] создается коллекция привязок с учетом мобильных служб.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Некоторые элементы управления в управляемой среде выполнения Windows поддерживают интерфейс [ISupportIncrementalLoading]. Этот интерфейс позволяет элементам управления запрашивать дополнительные данные во время прокрутки, выполняемой пользователем. Предусмотрена встроенная поддержка этого интерфейса для универсальных приложений Window через коллекцию [MobileServiceIncrementalLoadingCollection], которая автоматически обрабатывает вызовы от элементов управления. Чтобы использовать класс `MobileServiceIncrementalLoadingCollection` в приложениях для Windows, выполните следующее:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Чтобы использовать новую коллекцию в приложениях для Windows Phone 8 и Silverlight, используйте методы расширения `ToCollection` в интерфейсах `IMobileServiceTableQuery<T>` и `IMobileServiceTable<T>`. Чтобы фактически загрузить данные, вызовите метод `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

При использовании коллекции, созданной путем вызова метода `ToCollectionAsync` или `ToCollection`, вы получите коллекцию, которую можно привязать к элементам управления пользовательским интерфейсом. Эта коллекция поддерживает разбиение по страницам, то есть элемент управления может запросить в коллекции загрузку дополнительных элементов, что и будет выполнено коллекцией для этого элемента. На этом этапе пользовательский код не участвует, элемент управления запустит поток. Однако так как коллекция загружает данные из сети, ожидается, что иногда эта загрузка будет завершаться со сбоем. Чтобы обработать подобные ошибки, можно переопределить метод `OnException` класса `MobileServiceIncrementalLoadingCollection` для обработки исключений, возникающих в результате вызова элементами управления метода `LoadMoreItemsAsync`.

Наконец, представьте, что в таблице содержится множество полей, однако необходимо отобразить только те из них, которые находятся под вашим управлением. С помощью инструкций в разделе [Выбор конкретных столбцов](#selecting) выше можно выбрать столбцы, отображаемые в пользовательском интерфейсе.

##<a name="#customapi"></a>Работа с настраиваемым интерфейсом API

Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API можно вызвать путем вызова одной из перегрузок метода [InvokeApiAsync] для клиента. Например, следующая строка кода отправляет запрос POST API-интерфейсу **completeAll** в серверную часть:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Обратите внимание, что это вызов типизированного метода, для которого требуется определить возвращаемый тип **MarkAllResult**. Поддерживаются типизированные и нетипизированные методы.


##<a name="authentication"></a>Проверка подлинности пользователей

Мобильные приложения поддерживают аутентификацию и авторизацию пользователей с помощью различных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике [Добавление проверки подлинности в приложение].

Поддерживаются два потока проверки подлинности: _серверный_ и _клиентский_. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

В любом случае необходимо зарегистрировать приложение у поставщика удостоверений. Ваш поставщик удостоверений предоставит идентификатор клиента и секрет клиента. После этого необходимо настроить проверку подлинности или авторизацию службы приложений Azure с идентификатором и секретом клиента, предоставленными поставщиком удостоверений. Дополнительные сведения приведены в руководстве [Добавление проверки подлинности в приложение].

###<a name="serverflow"></a>Поток сервера
После регистрации в поставщике удостоверений вызовите метод MobileServiceCleint.[LoginAsync method] с указанием значения [MobileServiceAuthenticationProvider] для своего поставщика. Например, следующий код запускает вход в систему через поток сервера с помощью Facebook.

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

Если используется поставщик удостоверений, отличный от Facebook, измените значение [MobileServiceAuthenticationProvider] выше на значение для вашего поставщика.

В потоке сервера служба приложений Azure управляет потоком проверки подлинности OAuth 2.0, отображая страницу входа выбранного поставщика и создавая маркер проверки подлинности службы приложений после успешного соединения с поставщиком удостоверений. [Метод LoginAsync] возвращает объект [MobileServiceUser], в котором содержится как [UserId] авторизованного пользователя, так и [MobileServiceAuthenticationToken] в виде веб-маркера JSON (JWT). Этот маркер можно кэшировать и повторно использовать до истечения срока его действия. Дополнительные сведения см. в разделе [Кэширование маркера проверки подлинности](#caching).

###Клиентский поток

Приложение может также независимо связаться с поставщиком удостоверений и предоставить возвращаемый маркер службе приложений для проверки подлинности. Этот клиентский поток позволяет пользователям выполнять единый вход или получать дополнительные данные о пользователе от поставщика удостоверений.

####Однократный вход в систему с помощью маркера Google или Facebook

Упрощенная схема использования клиентского потока показана в этом фрагменте для Google или Facebook.

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
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
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

####Однократный вход в систему с использованием учетной записи Майкрософт с помощью пакета Live SDK

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать свое приложение в центре разработчиков учетных записей Майкрософт. Затем необходимо связать эту регистрацию с серверной частью мобильного приложения. Выполните действия в разделе [Регистрация приложения для входа с использованием учетной записи Майкрософт], чтобы зарегистрировать учетную запись Майкрософт и подключить ее к серверной части мобильного приложения. Если у вас есть версии приложения Магазина Windows и Windows Phone 8/Silverlight, сначала зарегистрируйте версию для Магазина Windows.

Следующий код выполняет проверку подлинности с помощью пакета Live SDK и использует возвращенный маркер для входа в серверную часть мобильного приложения.

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
			// The wl.basic scope should always be requested.  Other scopes can be added
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

Для получения дополнительной информации о [пакете SDK Windows Live] обратитесь к документации.

###<a name="caching"></a>Кэширование маркера проверки подлинности
В некоторых случаях вызова способа входа в систему можно избежать первого выполнения проверки подлинности пользователя. Чтобы кэшировать удостоверение текущего пользователя при первом входе в систему, в приложениях для Магазина Windows можно использовать класс [PasswordVault]. При каждом последующем входе будет проверяться, есть ли в кэше удостоверение пользователя. Если кэш пуст, пользователь будет перенаправлен на страницу входа в систему.

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


Для приложений для Windows Phone данные кэширования можно шифровать с помощью класса [ProtectedData] и сохранять конфиденциальные данные в отдельном хранилище.

-->

### <a name="adal"></a>Проверка подлинности пользователей с помощью библиотеки проверки подлинности Active Directory

Библиотеку проверки подлинности Active Directory (ADAL) можно использовать для входа пользователей в приложение с помощью Azure Active Directory. Этот подход является более предпочтительным, чем использование методов `loginAsync()`, так как он обеспечивает более удобный интерфейс входа для пользователя и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью AAD, следуя указаниям в руководстве [Настройка службы приложений для входа Active Directory]. Обязательно выполните дополнительный этап регистрации собственного клиентского приложения.

2. В Visual Studio или Xamarin Studio откройте проект и добавьте ссылку на пакет NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory`. Во время поиска включите предварительные версии.

3. Добавьте в приложение код, соответствующий используемой платформе. Код находится ниже. В каждом коде выполните следующие замены.

* Замените **INSERT-AUTHORITY-HERE** именем клиента, в котором подготовлено приложение. Используйте следующий формат: https://login.windows.net/contoso.onmicrosoft.com. Это значение можно скопировать на вкладке "Домен" в Azure Active Directory на [классическом портале Azure].

* Замените **INSERT-RESOURCE-ID-HERE** идентификатором клиента для серверной части мобильного приложения. Это значение можно скопировать на портале в разделе **Настройки Azure Active Directory** на вкладке **Дополнительно**.

* Замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

* Замените **INSERT-REDIRECT-URI-HERE** конечной точкой сайта _/.auth/login/done_, используя схему HTTPS. Это значение должно быть аналогично _https://contoso.azurewebsites.net/.auth/login/done_.

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

##<a name="pushnotifications">Push-уведомления

Push-уведомления рассматриваются в следующих разделах:

* [Регистрация для получения push-уведомлений](#register-for-push)
* [Получение SID пакета Магазина Windows](#package-sid)
* [Регистрация с помощью межплатформенных шаблонов](#register-xplat)

###<a name="register-for-push"></a>Практическое руководство. Регистрация для получения push-уведомлений

Клиент мобильных приложений позволяет выполнить регистрацию для получения push-уведомлений с помощью центров уведомлений Azure. При регистрации вы получаете маркер из службы push-уведомлений (PNS). Это значение необходимо указать при регистрации вместе со всеми тегами. Следующий код регистрирует ваше приложение Windows для получения push-уведомлений через службу уведомлений Windows (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

При отправке push-уведомлений в WNS необходимо получить SID пакета Магазина Windows (см. ниже). Обратите внимание, что в этом примере в регистрацию включены два тега. Дополнительные сведения о приложениях Windows, включая способ регистрации для выполнения регистраций шаблонов, см. в разделе [Добавление push-уведомлений в приложение].

Обратите внимание, что запрос тегов от клиента не поддерживается. Запросы тегов автоматически отбрасываются из регистрации. Если вы хотите зарегистрировать устройство с тегами, создайте пользовательский интерфейс API, который использует API центров уведомлений для выполнения регистрации от вашего имени. [Вызовите пользовательский интерфейс API](#customapi) вместо метода `RegisterNativeAsync()`.

###<a name="package-sid"></a>Практическое руководство. Получение SID пакета Магазина Windows

Для включения push-уведомлений для приложений Магазина Windows необходим SID пакета. SID пакета также используется для других целей (например, для единого входа Windows). Для получения SID пакета необходимо зарегистрировать приложение в Магазине Windows.

Для получения этого значения выполните следующие действия:

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения для Магазина Windows, щелкните **Магазин**, а затем — **Связать приложение с Магазином…**.
2. В окне мастера нажмите кнопку **Далее**, выполните вход с помощью учетной записи Майкрософт, введите имя приложения в поле **Зарезервировать новое имя приложения** и нажмите кнопку **Зарезервировать**.
3. После успешного создания регистрации приложения выберите новое имя приложения, нажмите кнопку **Далее**, а затем кнопку **Связать**. Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.
4. Выполните вход в [Центр разработки для Windows] с использованием учетной записи Майкрософт. В разделе **Мои приложения** щелкните только что созданную регистрацию приложения.
5. Щелкните **Управление приложениями** > **Удостоверение приложения**, а затем выполните прокрутку вниз, чтобы найти ваш **ИД безопасности пакета**.

Во многих случаях ИД безопасности пакета рассматривается как URI, при этом необходимо использовать _ms-app://_ в качестве схемы. Запишите версию ИД безопасности пакета, получаемую путем сцепления этого значения в качестве префикса.

Приложениям Xamarin требуется дополнительный код для регистрации приложения, работающего под управлением iOS или Android, в Службе push-уведомлений Apple (APNS) и службе Google Cloud Messaging (GCM) соответственно. Дополнительные сведения см. в разделе для вашей платформы:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Использование шаблонов для отправки кроссплатформенных push- уведомлений

Чтобы зарегистрировать шаблоны, используйте метод `RegisterAsync()` с шаблонами, как показано ниже:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Шаблоны будут относиться к типу JObject и содержать несколько шаблонов в следующем формате JSON:

        public JObject myTemplates()
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

Для отправки уведомлений с использованием этих зарегистрированных шаблонов обратитесь к разделу [Интерфейсы API центров уведомлений].

##<a name="misc"></a>Разное

###<a name="errors"></a>Практическое руководство. Обработка ошибок

При возникновении ошибки в серверной части пакет SDK клиента выдаст исключение `MobileServiceInvalidOperationException`. Следующий пример демонстрирует обработку исключения, возвращенного серверной частью:

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

Еще один пример работы с ошибками можно найти в разделе [Файлы примеров для мобильных приложений] — пример [LoggingHandler] представляет собой пример обработчика делегата для ведения журналов запросов к серверной части (см. ниже). Это позволяет удобно отлаживать приложения Xamarin вместо использования Fiddler.

###<a name="headers"></a>Практическое руководство. Настройка заголовков запроса

Для поддержки определенного сценария приложения вам может потребоваться настроить связь с внутренним сервером мобильных приложений. Например, может возникнуть необходимость добавлять настраиваемый заголовок к каждому исходящему запросу или даже изменять коды состояний ответов. Для этого нужно предоставить настраиваемый метод [DelegatingHandler], как показано в следующем примере:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
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
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->
[Фильтрация возвращаемых данных]: #filtering
[Сортировка возвращаемых данных]: #sorting
[Возврат данных на страницах]: #paging
[Возвращение данных на страницах]: #paging
[Выбор определенных столбцов]: #selecting
[Поиск данных по идентификатору]: #lookingup

<!-- Images. -->

<!-- Internal URLs. -->
[Краткого руководства по мобильным приложениям Azure]: app-service-mobile-windows-store-dotnet-get-started.md
[Краткое руководство по мобильным приложениям Azure]: app-service-mobile-windows-store-dotnet-get-started.md
[Добавление проверки подлинности в приложение]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Work with .NET backend SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[How to use the Node.js backend SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[How to: Define a table controller]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[Define Tables using a Dynamic Schema]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Автономная синхронизация данных в мобильных приложениях Azure]: app-service-mobile-offline-data-sync.md
[Добавление push-уведомлений в приложение]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Регистрация приложения для входа с использованием учетной записи Майкрософт]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Настройка службы приложений для входа Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[Справочник по клиенту .NET мобильных приложений Azure]: https://msdn.microsoft.com/ru-RU/library/azure/mt419521(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/ru-RU/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx
[MobileServiceCollection]: https://msdn.microsoft.com/ru-RU/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/ru-RU/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/ru-RU/library/azure/jj554275(v=azure.10).aspx
[создает ссылку на нетипизированную таблицу]: https://msdn.microsoft.com/ru-RU/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/ru-RU/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/ru-RU/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/ru-RU/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/ru-RU/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/ru-RU/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/ru-RU/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/ru-RU/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/ru-RU/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/ru-RU/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/ru-RU/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/ru-RU/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/ru-RU/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/ru-RU/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/ru-RU/library/azure/dn250579(v=azure.10).aspx
[портале Azure]: https://portal.azure.com/
[классическом портале Azure]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/ru-RU/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Центр разработки для Windows]: https://dev.windows.com/ru-RU/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[пакете SDK Windows Live]: https://msdn.microsoft.com/ru-RU/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Интерфейсы API центров уведомлений]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Файлы примеров для мобильных приложений]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63
[репозитории GitHub "Примеры для Azure"]: https://github.com/Azure-Samples

<!-- External URLs -->
[JsonPropertyAttribute]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[документации по OData версии 3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[SymbolSource]: http://www.symbolsource.org/
[инструкциям по SymbolSource]: http://www.symbolsource.org/Public/Wiki/Using

<!---HONumber=AcomDC_0316_2016-->