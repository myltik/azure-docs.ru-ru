<properties 
	pageTitle="Работа с клиентской библиотекой .NET мобильных служб" 
	description="Узнайте, как использовать клиент .NET для мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="glenga"/>

# Как использовать клиент .NET для мобильных служб Azure

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием клиента .NET для мобильных служб Azure в приложениях для Магазина Windows и Windows Phone. В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей и обработка ошибок. Если вы еще не работали с мобильными службами, сначала мы рекомендуем ознакомиться с [кратким руководством по мобильным службам]() или учебником [Добавление мобильных служб в существующее приложение]().

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Настройка и необходимые компоненты

Мы предполагаем, что вы создали мобильную службу и таблицу. Дополнительные сведения см. в разделе [Создание таблицы](http://go.microsoft.com/fwlink/?LinkId=298592). В коде, который используется в этом разделе, применяется таблица `TodoItem`, которая содержит следующие столбцы: `Id`, `Text` и `Complete`.

Соответствующий типизированный тип .NET на стороне клиента выглядит следующим образом:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Если динамическая схема включена, мобильные службы Azure автоматически создают новые столбцы на основе объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема](http://go.microsoft.com/fwlink/?LinkId=296271).

##<a name="create-client"></a>Практическое руководство. Создание клиента мобильных служб

В следующем коде создается объект `MobileServiceClient`, который используется для доступа к мобильной службе.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

В приведенном выше коде замените `AppUrl` и `AppKey` URL-адресом мобильной службы и ключом приложения соответственно. Оба варианта доступны на портале управления Azure: выберите свою мобильную службу и щелкните "Панель управления".

>[AZURE.IMPORTANT]Ключ приложения предназначен для фильтрации случайных запросов в мобильной службе и распространяется вместе с приложением. Поскольку этот ключ не зашифрован, он не может считаться безопасным. Чтобы обеспечить надежную защиту данных мобильной службы, необходимо выполнять проверку подлинности пользователей перед предоставлением им доступа. Дополнительные сведения см. в разделе [Практическое руководство. Проверка подлинности пользователей](#authentication).

##<a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу

Весь код, который обращается к данным в таблице мобильных служб или изменяет эти данные, вызывает функции объекта `MobileServiceTable`. Ссылку на таблицу можно получить, вызвав функцию [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) для экземпляра `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Это типизированная модель сериализации; <a href="#untyped">нетипизированная модель сериализации</a> обсуждается ниже.

##<a name="querying"></a>Практическое руководство. Запрос данных от мобильной службы

В этом разделе показано, как отправлять запросы к мобильной службе. Описаны следующие функциональные возможности:

- [Фильтрация возвращаемых данных]
- [Сортировка возвращаемых данных]
- [Возврат данных на страницах]
- [Выбор определенных столбцов]
- [Поиск данных по идентификатору]

>[AZURE.NOTE]Для предотвращения возврата всех строк принудительно применяется размер страницы, управляемый сервером. Это предотвращает негативное воздействие больших наборов данных на функционирование службы. Для возвращения более 50 строк используйте метод `Take`, как описано в разделе [Возвращение данных на страницах].

### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий код иллюстрирует способ фильтрации данных путем включения предложения `Where` в запрос. Он возвращает все элементы таблицы `todoTable`, свойство `Complete` которых равно `false`. Функция `Where` применяет в запросе к таблице предикат фильтрации строк.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Универсальный код ресурса (URI) запроса, отправленного в мобильную службу, можно просмотреть с помощью программы проверки сообщений, такой как средства разработчика браузера или [Fiddler]. Если взглянуть на следующий URI запроса, можно отметить, что мы изменяем саму строку запроса:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
На стороне сервера такой запрос обычно должен преобразовываться примерно в такой запрос SQL:

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

Эти два способа эквивалентны и могут быть взаимозаменяемыми. Первый вариант - объединение нескольких предикатов в одном запросе - является более компактным и рекомендуемым.

Предложение `where` поддерживает операции, которые будут преобразованы в подмножество OData мобильных служб. К ним относятся реляционные операторы (==, !=, <, <=, >, >=), арифметические операторы (+, -, /, *, %), определения точности значений (Math.Floor, Math.Ceiling), строковые функции (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), свойства даты (Year, Month, Day, Hour, Minute, Second), свойства доступа к объекту и выражения, объединяющие все вышеупомянутые операции.

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

По умолчанию сервер возвращает только первые 50 строк. Число возвращенных строк можно увеличить путем вызова метода [Take]. Чтобы запросить определенную "страницу" общего набора данных, возвращенного запросом, используйте метод `Take` вместе с методом [Skip]. При выполнении следующего запроса будут возвращены три главных элемента в таблице.

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

####Вопросы подкачки в серверной мобильной службе .NET

Чтобы переопределить ограничение в 50 строк в серверной мобильной службе .NET, необходимо также применить класс [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) к общедоступному методу GET и настроить подкачку. В этом случае максимальное количество возвращаемых строк увеличивается до 1000:

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

Добавив в запрос предложение `Select`, можно задать набор свойств, который будет включен в возвращаемые результаты. Например, в следующем коде показано, как выбрать только одно поле, а также способы выбора и форматирования нескольких полей:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
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

##<a name="inserting"></a>Практическое руководство. Вставка данных в мобильную службу

> [AZURE.NOTE]Чтобы выполнить операцию вставки, поиска, удаления или обновления для какого-то типа, необходимо создать элемент с именем **Id**. Именно поэтому в примере класса **TodoItem** используется член с именем **Id**. В операциях обновления и удаления всегда должно присутствовать допустимое значение идентификатора.

В следующем коде показано, как вставить новые строки в таблицу. Параметр содержит данные, которые вставляются в качестве объекта .NET.

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

Мобильные службы поддерживают уникальные настраиваемые строковые значения для столбца **id** таблицы. Это позволяет приложениям использовать в качестве идентификатора настраиваемые значения, такие как адреса электронной почты или имена пользователей.

Строковые идентификаторы предоставляют следующие преимущества.

+ Идентификаторы создаются без обмена данными с базой данных.
+ Можно легко объединять записи из разных таблиц или баз данных.
+ Значения идентификаторов можно удобно интегрировать с логикой приложения.

Если для вставленной записи не задано строковое значение идентификатора, мобильные службы создают уникальное значение для идентификатора. Для создания собственного значения идентификатора в клиенте или серверной мобильной службе .NET можно использовать метод `Guid.NewGuid()`. Дополнительные сведения о создании GUID в серверной мобильной службе JavaScript см. в разделе [Практическое руководство. Создание уникальных значений идентификатора](mobile-services-how-to-use-server-scripts.md#generate-guids).

Также для таблиц можно использовать целочисленные идентификаторы. Для этого необходимо создать таблицу с помощью команды `mobile table create`, используя параметр `--integerId`. Эта команда используется в интерфейсе командной строки (CLI) Azure. Дополнительные сведения об использовании CLI см. в статье [Интерфейс командной строки для управления таблицами мобильных служб](../virtual-machines-command-line-tools.md#Mobile_Tables).

##<a name="modifying"></a>Практическое руководство. Изменение данных в мобильной службе

Следующий код показывает, как добавить новую информацию в существующий экземпляр с тем же идентификатором. Параметр содержит данные, которые обновляются в качестве объекта .NET.

	await todoTable.UpdateAsync(todoItem);


Для вставки нетипизированных данных можно использовать Json.NET. Обратите внимание, что при выполнении обновления должен быть указан идентификатор, поскольку с его помощью мобильная служба определяет, какой экземпляр нужно обновить. Идентификатор можно получить из результатов вызова метода `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

При попытке обновить элемент без предоставления значения Id служба не сможет определить, какой экземпляр следует обновить, поэтому пакет SDK для мобильных служб выдаст исключение `ArgumentException`.


##<a name="deleting"></a>Практическое руководство. Удаление данных в мобильной службе

Следующий код показывает, как удалить существующий экземпляр. Экземпляр идентифицируется по полю Id, заданному в свойстве `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Для удаления нетипизированных данных можно использовать Json.NET. Обратите внимание, что при выполнении запроса на удаление должен быть указан идентификатор, поскольку с его помощью мобильная служба определяет, какой экземпляр нужно удалить. Для запроса на удаление требуется только идентификатор; другие свойства не передаются в службу; любые передаваемые свойства будут игнорироваться службой. В результате вызова функции `DeleteAsync` обычно возвращается значение `null`. Идентификатор для передачи можно получить в результате вызова метода `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Если вы попытаетесь удалить элемент без заданного поля Id, служба не сможет определить, какой экземпляр нужно удалить, поэтому от нее будет получен ответ `MobileServiceInvalidOperationException`. Аналогично, при попытке удалить нетипизированный элемент, для которого не задано поле Id, от службы также будет получен ответ `MobileServiceInvalidOperationException`.

##<a name="#custom-api"></a>Практическое руководство. Вызов настраиваемого интерфейса API

Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON. Полный пример, который включает в себя создание пользовательского API в мобильной службе, см. в разделе [Вызов из клиента настраиваемого интерфейса API].

Настраиваемый API можно вызвать путем вызова одной из перегрузок метода [InvokeApiAsync] для клиента. Например, следующая строка кода отправляет запрос POST интерфейсу API **completeAll** в мобильной службе:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Обратите внимание, что это вызов типизированного метода, для которого требуется определить возвращаемый тип **MarkAllResult**. Поддерживаются типизированные и нетипизированные методы. Это предельно простой пример, поскольку он типизированный, не отправляет полезных данных, не содержит параметров запроса и не меняет заголовков запроса. Практические примеры и дополнительную информацию о методе [InvokeApiAsync] см. в разделе [Custom API in Azure Mobile Services Client SDKs] (Настраиваемый интерфейс API в пакетах SDK для клиента мобильных служб Azure).

##Практическое руководство. Регистрация для получения push-уведомлений

Клиент мобильных служб позволяет выполнить регистрацию для получения push-уведомлений с помощью концентраторов уведомлений Azure. При регистрации вы получите универсальный код ресурса (URI) канала из платформы Windows. Это значение необходимо указать при регистрации вместе со всеми тегами. Следующий код регистрирует приложение Магазина Windows, использующее службу Windows Notification Service (WNS), для получения push-уведомлений:

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

Обратите внимание, что в этом примере в регистрацию включены два тега. Дополнительные сведения см. в разделе [Добавление push-уведомлений в приложение](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md).

>[AZURE.NOTE]Если уведомления необходимо отправлять конкретному зарегистрированному пользователю, обязательно запросите проверку подлинности перед регистрацией, а затем убедитесь, что пользователь вправе зарегистрироваться с использованием определенного тега. Например, необходимо убедиться, что при регистрации пользователь не воспользовался тегом, который представляет собой идентификатор другого пользователя. Дополнительные сведения см. в статье [Рассылка push-уведомлений проверенным пользователям](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).


##<a name="optimisticconcurrency"></a>Практическое руководство. Использование оптимистичного параллелизма

В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. При управлении оптимистичным параллелизмом предполагается, что каждая транзакция может фиксироваться, поэтому не использует блокировки каких-либо ресурсов. Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается.

Мобильные службы поддерживают управление оптимистичным параллелизмом за счет отслеживания изменений в каждом элементе с помощью столбца системных свойств `__version`, определенного для каждой таблицы, созданной мобильными службами. При каждом обновлении записи мобильные службы задают новое значение свойства `__version` для этой записи. При обработке каждого запроса на обновление свойство `__version` записи, включенное в запрос, сравнивается с тем же свойством записи на сервере. Если версия, переданная с запросом, не соответствует серверу, клиентская библиотека .NET мобильных служб вызывает исключение `MobileServicePreconditionFailedException<T>`. Тип, включенный в исключение, является записью с сервера, которая содержит версию записи на сервере. Затем приложение может использовать эти данные, чтобы решить, следует ли повторно выполнить полученный с сервера запрос изменения с правильным значением `__version` для фиксации изменений.

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


Более полный пример использования оптимистичного параллелизма для мобильных служб см. в [учебнике по оптимистичного параллелизму].


##<a name="binding"></a>Практическое руководство. Привязка данных к пользовательскому интерфейсу в мобильной службе

В этом разделе показано, как отображать возвращенные объекты данных с использованием элементов пользовательского интерфейса. Для запроса незавершенных элементов в таблице `todoTable` и отображения его в простом списке необходимо выполнить следующий пример кода, чтобы привязать источник к списку с помощью запроса. С помощью `MobileServiceCollection` создается коллекция привязок, поддерживающая мобильные службы.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Некоторые элементы управления в среде выполнения Windows поддерживают интерфейс [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Этот интерфейс позволяет элементам управления запрашивать дополнительные данные во время прокрутки, выполняемой пользователем. Для приложений для Магазина Windows предусмотрена встроенная поддержка этого интерфейса через класс `MobileServiceIncrementalLoadingCollection`, который автоматически обрабатывает вызовы из элементов управления. Чтобы использовать класс `MobileServiceIncrementalLoadingCollection` в приложениях Магазина Windows, выполните следующие действия:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Чтобы использовать новую коллекцию в Windows Phone, используйте методы расширения `ToCollection` в интерфейсах `IMobileServiceTableQuery<T>` и `IMobileServiceTable<T>`. Чтобы фактически загрузить данные, вызовите метод `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

При использовании коллекции, созданной путем вызова метода `ToCollectionAsync` или `ToCollection`, вы получите коллекцию, которую можно привязать к элементам управления пользовательским интерфейсом. Эта коллекция поддерживает разбиение по страницам, то есть элемент управления может запросить в коллекции загрузку дополнительных элементов, что и будет выполнено коллекцией для этого элемента. На этом этапе пользовательский код не участвует, элемент управления запустит поток. Однако так как коллекция загружает данные из сети, ожидается, что иногда эта загрузка будет завершаться со сбоем. Чтобы обработать подобные ошибки, можно переопределить метод `OnException` класса `MobileServiceIncrementalLoadingCollection` для обработки исключений, возникающих в результате вызова элементами управления метода `LoadMoreItemsAsync`.

Наконец, представьте, что в таблице содержится множество полей, однако необходимо отобразить только те из них, которые находятся под вашим управлением. Можно воспользоваться указаниями в приведенном выше разделе <a href="#selecting">Выбор конкретных столбцов</a> для выбора столбцов, отображаемых в пользовательском интерфейсе.

##<a name="authentication"></a>Практическое руководство. Проверка подлинности пользователей

Мобильные службы поддерживают аутентификацию и авторизацию пользователей с помощью различных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике [Добавление проверки подлинности в приложение].

Поддерживаются два потока проверки подлинности: _серверный_ и _клиентский_. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

###Серверный поток
Чтобы мобильная служба могла выполнять аутентификацию в приложении для магазина Windows или для Windows Phone, необходимо зарегистрировать приложение у поставщика удостоверений. Затем в вашей мобильной службе необходимо настроить код приложения и секретный код, предоставленный поставщиком. Дополнительные сведения см. в учебнике [Добавление проверки подлинности в приложение].

После регистрации у поставщика удостоверений просто вызовите [метод LoginAsync] с указанием значения [MobileServiceAuthenticationProvider] вашего поставщика. Например, следующий код запускает вход в систему через поток сервера с помощью Facebook.

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

В этом случае мобильная служба управляет потоком проверки подлинности OAuth 2.0, отображая страницу входа выбранного поставщика и генерируя маркер проверки подлинности мобильных служб после успешного соединения с поставщиком удостоверений. Метод [LoginAsync] возвращает ответ [MobileServiceUser], в котором содержится как [userId] авторизованного пользователя, так и [MobileServiceAuthenticationToken] в качестве веб-маркера JSON (JWT). Этот маркер можно кэшировать и повторно использовать до истечения срока его действия. Дополнительные сведения см. в разделе [Кэширование маркера проверки подлинности].

> [AZURE.NOTE]**Приложение Магазина Windows.** Если вы используете поставщик входа в учетные записи Майкрософт для проверки подлинности пользователей в приложении Магазина Windows, этот пакет приложения также необходимо зарегистрировать в мобильных службах. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Чтобы узнать, как зарегистрировать пакет приложения Магазина Windows, изучите документ [Регистрация пакета приложения Магазина Windows для проверки подлинности Майкрософт](/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). После регистрации данных пакета в мобильных службах вызовите метод [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594%20target="_blank"), подставив вместо параметра **useSingleSignOn** значение _true_, чтобы повторно использовать учетные данные.

###Клиентский поток

Приложение может также независимо связаться с поставщиком удостоверений и указать возвращаемый маркер в мобильные службы для проверки подлинности. Этот клиентский поток позволяет пользователям выполнять единый вход или получать дополнительные данные о пользователе от поставщика удостоверений.

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

Если используется учетная запись Майкрософт, вход следует выполнить следующим образом:

	// Replace authentication_token_value with actual value of your Microsoft authentication token obtained through the Live SDK
	user = await client
		.LoginWithMicrosoftAccountAsync(authentication_token_value);

Более полный пример использования учетной записи Майкрософт для обеспечения единого входа см. в разделе "Проверка подлинности приложения с помощью единого входа" ([Магазин Windows](/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/develop/mobile/tutorials/single-sign-on-wp8/)).

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

##<a name="errors"></a>Практическое руководство. Обработка ошибок

Существует несколько способов выявления, проверки и устранения ошибок в мобильных службах.

Например, серверные сценарии зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. Процесс определения и регистрации серверного сценария для проверки и изменения данных можно представить следующим образом:

	function insert(item, user, request)
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

Серверный сценарий проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинные строки; в этом случае отклоняются строки длиной более 10 знаков.

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, можно обновить приложение .NET для обработки сообщений об ошибках, полученных при выполнении проверки.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
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

##<a name="untyped"></a>Практическое руководство. Работа с нетипизированными данными

Клиентский компонент .NET предназначен для строго типизированных сценариев. Тем не менее в некоторых случаях удобнее использовать менее типизированные сценарии; например, это может потребоваться при работе с объектами с открытой схемой. Этот сценарий активируется следующим образом. В запросах нужно отказаться от LINQ и использовать телеграфный формат.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Вы будете получать значения JSON, которые можно использовать в качестве контейнера свойств. Более подробные сведения о JToken и Json.NET см. в разделе [Json.NET](http://json.codeplex.com/)

##<a name="unit-testing"></a>Практическое руководство. Разработка модульных тестов

Функция `MobileServiceClient.GetTable` и запросы возвращают значение, которое является интерфейсом. Из-за этого их легко критиковать с точки зрения целесообразности тестирования, поэтому вы можете создать `MyMockTable : IMobileServiceTable<TodoItem>` для реализации своей логики тестирования.

##<a name="customizing"></a>Практическое руководство. Настройка клиента

В этом разделе показано, как настроить заголовки запроса, а также сериализацию объектов JSON в ответе.

### <a name="headers"></a>Практическое руководство. Настройка заголовков запроса

Для поддержки определенного сценария приложения вам может потребоваться настроить связь с мобильной службой. Например, может возникнуть необходимость добавлять настраиваемый заголовок к каждому исходящему запросу или даже изменять коды состояний ответов. Для этого нужно предоставить настраиваемый метод DelegatingHandler, как показано в следующем примере:

	public async Task CallClientWithHandler()
	{
		MobileServiceClient client = new MobileServiceClient(
			"AppUrl",
			"AppKey" ,
			new MyHandler()
			);
		IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
		var newItem = new TodoItem { Text = "Hello world", Complete = false };
		await table.InsertAsync(newItem);
	}

	public class MyHandler : DelegatingHandler
	{
		protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		{
			request.Headers.Add("x-my-header", "my value");
			var response = awaitbase.SendAsync(request, cancellationToken);
			response.StatusCode = HttpStatusCode.ServiceUnavailable;
			return response;
		}
	}

Этот код добавляет новый заголовок **x-my-header** в запрос и произвольно задает для кода ответа значение "Недоступно". В реальном сценарии код состояния ответа задается с учетом настраиваемой логики, необходимой для приложения.

### <a name="serialization"></a>Практическое руководство. Настройка сериализации

Клиентская библиотека мобильных служб использует Json.NET для преобразования ответа JSON в объекты .NET на стороне клиента. Вы можете настроить параметры сериализации между типами .NET и JSON в сообщениях. Класс [MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) предоставляет свойство `SerializerSettings` типа [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm).

С помощью этого свойства вы можете установить одно из многих свойств Json.NET, например:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Это свойство преобразует все свойства в нижний регистр во время сериализации.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Фильтрация возвращаемых данных]: #filtering
[Сортировка возвращаемых данных]: #sorting
[Возврат данных на страницах]: #paging
[Возвращение данных на страницах]: #paging
[Выбор определенных столбцов]: #selecting
[Поиск данных по идентификатору]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Use Optimistic Concurrency]: #optimisticconcurrency
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[Кэширование маркера проверки подлинности]: #caching
[How to: Call a custom API]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Добавление проверки подлинности в приложение]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[метод LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[учебнике по оптимистичного параллелизму]: mobile-services-windows-store-dotnet-handle-database-conflicts.md

[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Custom API in Azure Mobile Services Client SDKs]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[Вызов из клиента настраиваемого интерфейса API]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
 

<!---HONumber=July15_HO2-->