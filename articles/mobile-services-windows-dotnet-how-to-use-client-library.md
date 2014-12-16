
<properties urlDisplayName=".NET Client Library" pageTitle="Работа с клиентской библиотекой мобильных служб .NET " metaKeywords="мобильных служб Azure, клиент мобильных служб .NET, клиент .NET" description="Learn how to use an .NET client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use a .NET client for Azure Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="glenga" />





# Как использовать клиент .NET для мобильных служб Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework" class="current">.NET Framework.</a>
  	<a href="/ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


В этом руководстве показано, как выполнять общие сценарии с использованием клиента .NET для мобильных служб Azure в приложениях Windows Store и Windows Phone. В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей и обработка ошибок. Если вы впервые работаете с мобильными службами, то рекомендуем сначала проработать учебник "Быстрый запуск мобильных служб" ([Учебник быстрого запуска для Магазина Windows]/[учебник быстрого запуска для Windows Phone]), а также учебник "Приступая к работе с данными в .NET" ([Учебник по данным Магазина Windows]/[Учебник по данным Windows Phone]). Для настоящего краткого руководства требуется установка пакета [SDK мобильных служб]. Оно поможет настроить учетную запись и создать первую мобильную службу.


## Оглавление

- [Что такое мобильные службы?]
- [Основные понятия]
- [Практическое руководство. Создание клиента мобильных служб]
- [Практическое руководство. Создание ссылки на таблицу]
- [Практическое руководство. Запрос данных от мобильной службы]
	- [Фильтрация возвращаемых данных]
    - [Сортировка возвращаемых данных]
	- [Возврат данных на страницах]
	- [Выбор определенных столбцов]
	- [Поиск данных по идентификатору]
- [Практическое руководство. Вставка данных в мобильную службу]
- [Практическое руководство. Изменение данных в мобильной службе]
- [Практическое руководство. Удаление данных в мобильной службе]
- [Практическое руководство. Вызов настраиваемого интерфейса API]
- [Практическое руководство. Использование оптимистичного параллелизма]
- [Практическое руководство. Привязка данных к пользовательскому интерфейсу в мобильной службе]
- [Практическое руководство. Проверка подлинности пользователей]
- [Практическое руководство. Обработка ошибок]
- [Практическое руководство. Работа с нетипизированными данными]
- [Практическое руководство. Разработка модульных тестов]
- [Практическое руководство. Настройка клиента]
	- [Настройка заголовков запроса]
	- [Настройка сериализации]
- [Дальнейшие действия]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a>Настройка и необходимые компоненты</h2>

Предполагается, что мобильная служба и таблица созданы. Дополнительные сведения см. в разделе [Создание таблицы](http://go.microsoft.com/fwlink/?LinkId=298592). В коде, используемом в данном разделе, таблица носит имя `TodoItem` и имеет следующие столбцы: `Id`, `Text` и `Complete`.

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

<h2><a name="create-client"></a>Практическое руководство. Создание клиента мобильных служб</h2>

В следующем коде создается объект `MobileServiceClient`, который используется для получения доступа к мобильной службе.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

В приведенном выше коде замените `AppUrl` и `AppKey` на URL мобильной службы и ключ приложения, в том же порядке. Оба варианта доступны на портале управления Azure: выберите свою мобильную службу и щелкните "Панель управления".

<h2><a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу</h2>

Весь код, который обращается к данным или изменяет данные в таблице мобильных служб, вызывает функции объекта MobileServiceTable. Ссылку на таблицу можно получить путем вызова функции [GetTable](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554275.aspx) на экземпляре `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Это типизированная модель сериализации; см. обсуждение ниже в разделе <a href="#untyped">Нетипизированная модель сериализации</a>.

<h2><a name="querying"></a>Практическое руководство. Запрос данных от мобильной службы</h2>

В этом разделе показано, как отправлять запросы к мобильной службе. В подразделах описываются различные аспекты, например сортировка, фильтрация и разбиение по страницам.

>[WACOM.NOTE] Для предотвращения возврата всех строк по умолчанию используется размер страницы, управляемый сервером. Это предотвращает негативное воздействие больших наборов данных на функционирование службы. Чтобы вернуть более 50 строк, используйте метод `Take`, как описано в разделе [Возврат данных с разбиением на страницы].  

### <a name="filtering"></a>Практическое руководство. Данные, возвращенные фильтром

В следующем коде показано, как фильтровать данные, включив предложение `Where` в запрос. Он возвращает все элементы из `todoTable`, если их свойство `Complete` имеет значение `false`. Функция `Where` применяет предикат фильтрации строк к запросу на таблице.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Можно просмотреть URI запроса, отправленного в мобильную службу, с использованием программного обеспечения просмотра сообщений, такого как средства разработчика браузера или [Fiddler]. Если взглянуть на следующий URI запроса, можно отметить, что мы изменяем саму строку запроса:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
На стороне сервера такой запрос обычно должен преобразовываться примерно в такой запрос SQL:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Функция, передаваемая в метод `Where`, может иметь произвольное количество условий. Например, следующая строка:

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

В приведенной выше инструкции `where` осуществляется поиск элементов, статус `Complete` которых равен false, а значение `Text` отлично от NULL.

Это же выражение можно написать с использованием нескольких строк:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Эти два способа эквивалентны и могут быть взаимозаменяемыми.  Первый вариант (объединение нескольких предикатов в одном запросе) является более компактным и рекомендуемым.

Предложение `where` поддерживает операции, которые могут быть транслированы в подмножество OData мобильных служб. К ним относятся реляционные операторы (==, !=, <, <=, >, >=), арифметические операторы (+, -, /, *, %), определения точности значений (Math.Floor, Math.Ceiling), строковые функции (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), свойства даты (Year, Month, Day, Hour, Minute, Second), свойства доступа к объекту и выражения, объединяющие все вышеупомянутые операции.

### <a name="sorting"></a>Практическое руководство. Данные, возвращенные сортировкой

Следующий код показывает, как сортировать данные, включая функцию `OrderBy` или `OrderByDescending` в запрос. Он возвращает элементы из таблицы `todoTable`, отсортированные по возрастанию значения поля `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Практическое руководство. Возврат данных в виде страниц

По умолчанию сервер возвращает только первые 50 строк. Число возвращенных строк можно увеличить путем вызова метода [Take]. Используйте метод `Take` наряду с методом [Skip] для запроса конкретной "страницы" из полного набора данных, возвращенного запросом. При выполнении следующего запроса будут возвращены три главных элемента в таблице.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Следующий измененный запрос пропускает первые три результата и после этого возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы равен трем элементам.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Можно также использовать метод [IncludeTotalCount], чтобы запрос возвращал общее количество <i>всех</i> записей, которые должны быть возвращены без учета указанных предложений разбиения по страницам или ограничения:

	query = query.IncludeTotalCount();

Это упрощенный сценарий передачи жестко запрограммированных значений разбиения по страницам в методы Take и Skip. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице.

### <a name="selecting"></a>Практическое руководство. Выбор конкретных столбцов

Можно указать, какой набор свойств должен быть включен в результаты, добавляя предложение `Select` к запросу. Например, в следующем коде показано, как выбрать только одно поле, а также способы выбора и форматирования нескольких полей:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Все описанные функции являются суммируемыми, поэтому каждая последующая вызываемая функция будет влиять на запрос все больше. Ниже приведен еще один пример:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Практическое руководство. Поиск данных по идентификатору

Функция `LookupAsync` может использоваться для поиска в базе данных объектов с конкретным идентификатором.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

<a name="inserting"></a>Практическое руководство. Вставка данных в мобильную службу

<div class="dev-callout"><strong>Примечание</strong>. <p>Если вы хотите выполнить операцию вставки, поиска, удаления или обновления для данного типа, необходимо создать члена с именем <strong>Id</strong>. Именно поэтому в примере класса <strong>TodoItem</strong> используется член с именем <strong>Id</strong>. Допустимое значение идентификатора всегда должно присутствовать в операциях обновления и удаления.</p> </div>

Следующий код показывает, как вставлять новые строки в таблицу. Параметр содержит данные, которые должны быть вставлены как объект .NET.

	await todoTable.InsertAsync(todoItem);

Если уникальное пользовательское идентификационное значение не включено в элемент `todoItem`, переданный в вызове `todoTable.InsertAsync`, то значение идентификатора будет сформировано серверным набором в объекте `todoItem`, возвращенном клиенту.

Мобильные службы поддерживают уникальные пользовательские строковые значения для идентификатора таблицы. Это позволяет задавать в приложениях пользовательские значения, такие как адреса электронной почты или имена пользователей, для столбца идентификатора таблицы мобильных служб. Если значение идентификатора строки не указано при вставке новых записей в таблицу, мобильные службы создают уникальное значение идентификатора.

Поддержка строковых идентификаторов предоставляет разработчикам следующие преимущества:

+ Идентификаторы можно создавать без обмена данными с базой данных.
+ Можно легко объединять записи из разных таблиц или баз данных.
+ Значения идентификаторов можно удобно интегрировать с логикой приложения.

Можно также использовать серверный скрипт для задания значений идентификаторов. В приведенном ниже примере скрипта создается пользовательский GUID и присваивается идентификатору новой записи. Это аналогично тому значению идентификатора, которое было бы создано мобильными службами, если бы не было передано значение для идентификатора записи.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Если приложение предоставляет значение для идентификатора, мобильные службы сохраняют его "как есть". (В том числе с начальными или конечными пробелами.) Пробелы не удаляются в значении.

Значение для `идентификатора` должно быть уникальным и не может включать символы из следующих наборов:

+ Управляющие символы: [0x0000-0x001F] и [0x007F-0x009F]. Дополнительные сведения см. в разделе [Управляющие коды ASCII C0 и C1].
+  Печатаемые символы: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Идентификаторы "." и ".."


Иным образом, можно использовать для таблиц целочисленные идентификаторы. Для этого необходимо создать таблицу с помощью команды `mobile table create` с параметром `--integerId`. Эта команда используется в интерфейсе командной строки (CLI) Azure. Дополнительные сведения об использовании CLI см. в разделе [CLI для управления таблицами мобильных служб].


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


<h2><a name="modifying"></a>Практическое руководство. Изменение данных в мобильной службе</h2>

В следующем коде показано, как обновить существующий экземпляр с тем же идентификатором, задавая новую информацию. Параметр содержит данные, подлежащие обновлению как объект .NET.

	await todoTable.UpdateAsync(todoItem);


Чтобы вставить нетипизированные данные, можно воспользоваться Json.NET примерно следующим образом. Обратите внимание, что при выполнении обновления должен быть указан идентификатор, поскольку с его помощью мобильная служба определяет, какой экземпляр нужно обновить. Идентификатор может быть получен из результатов вызова "InsertAsync".

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

При попытке обновить элемент без значения "Id" служба не сможет определить обновляемый экземпляр, поэтому SDK мобильных служб вызовет исключение "ArgumentException".


<h2><a name="deleting"></a>Практическое руководство. Удаление данных в мобильной службе</h2>

В следующем коде показано, как удалить существующий экземпляр. Экземпляр идентифицируется полем "Id", заданным в таблице "todoItem".

	await todoTable.DeleteAsync(todoItem);

Для удаления нетипизированных данных можно воспользоваться Json.NET примерно следующим образом. Обратите внимание, что при выполнении запроса на удаление должен быть указан идентификатор, поскольку с его помощью мобильная служба определяет, какой экземпляр нужно удалить. Для запроса на удаление требуется только идентификатор; другие свойства не передаются в службу; любые передаваемые свойства будут игнорироваться службой. В результате вызова `DeleteAsync` обычно будет выдаваться ответ `null`. Передаваемый идентификатор можно получить из результата вызова `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

При попытке удалить элемент, не задавая значения поля "Id", служба не сможет определить удаляемый экземпляр, поэтому активирует исключение "MobileServiceInvalidOperationException". Аналогичным образом, при попытке удалить нетипизированный элемент без заданного поля "Id" служба активирует исключение "MobileServiceInvalidOperationException".

## <a name="#custom-api"></a>Практическое руководство. Вызов настраиваемого интерфейса API

Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON. Полный пример, который в том числе показывает, как создать пользовательский API в своей мобильной службе, см. в разделе [Вызов пользовательского API из клиента].

Можно вызвать пользовательский API с помощью запроса к одному из перегруженных методов [InvokeApiAsync] на клиенте. Например, в следующей строке кода передается запрос POST к функции API **completeAll** в мобильной службе:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Следует отметить, что это типизированный вызов метода, который требует, чтобы был определен возвращаемый тип **MarkAllResult**. Поддерживаются типизированные и нетипизированные методы. Это предельно простой пример, поскольку он типизированный, не отправляет полезных данных, не содержит параметров запроса и не меняет заголовков запроса. Более реалистичные примеры и более полное обсуждение функции [InvokeApiAsync] см. в разделе [Пользовательский API в SDK клиентских мобильных служб Azure].


## <a name="optimisticconcurrency"></a>Практическое руководство. Использование оптимистичного параллелизма

В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. При управлении оптимистичным параллелизмом предполагается, что каждая транзакция может фиксироваться, поэтому не использует блокировки каких-либо ресурсов. Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается.

Мобильные службы поддерживают оптимистическое управление параллелизмом, отслеживая изменения в каждом элементе с использованием столбца системного свойства `__version`, который задан для каждой таблицы, созданной мобильными службами. При каждом обновлении записи мобильные службы задают новое значение свойства `__version` этой записи. При выполнении каждого запроса на обновление свойство `__version` записи, представленное в запросе, сравнивается с тем же свойством записи на сервере. Если версия, переданная в запросе, не согласуется со значением на сервере, то клиентская библиотека .NET мобильных служб вызовет исключение `MobileServicePreconditionFailedException<T>`. Тип, включенный в исключение, является записью с сервера, которая содержит версию записи на сервере. Затем в приложении можно воспользоваться этой информацией для принятия решения о том, следует ли снова выполнить запрос на обновление с правильным значением `__version` с сервера, чтобы зафиксировать изменения.  

Для обеспечения оптимистического управления параллелизмом в приложении определяется столбец на классе таблицы для системного свойства `__version`. Пример представлен в следующем определении.

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


Приложения, использующие нетипизированные таблицы, включают оптимистичное управление параллелизмом, задавая флаг `Version` в таблице `SystemProperties` следующим образом.

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


В следующем коде показано, как разрешить конфликт операции записи после его обнаружения. Правильное значение `__version` должно быть включено в вызов `UpdateAsync()` для фиксации разрешения.

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
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
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


Более полный пример использования оптимистического управления параллелизмом для мобильных служб см. в разделе [Учебник по оптимистическому управлению параллелизмом].


<h2><a name="binding"></a>Практическое руководство. Привязка данных к пользовательскому интерфейсу в мобильной службе</h2>

В этом разделе показано, как отображать возвращаемые объекты данных с помощью элементов пользовательского интерфейса. Чтобы запросить неполные элементы в таблице `todoTable` и показать их в виде очень простого списка, можно выполнить следующий код примера для привязки источника списка к запросу. При использовании `MobileServiceCollection` создается коллекция привязок с учетом мобильных служб.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Некоторые элементы управления в среде Windows времени выполнения поддерживают интерфейс [ISupportIncrementalLoading](http://msdn.microsoft.com/ru-ru/library/windows/apps/Hh701916). Этот интерфейс позволяет элементам управления запрашивать дополнительные данные во время прокрутки, выполняемой пользователем. Предусмотрена встроенная поддержка этого интерфейса для приложений Windows Store через коллекцию `MobileServiceIncrementalLoadingCollection`, которая автоматически обрабатывает вызовы от элементов управления. Чтобы использовать коллекцию `MobileServiceIncrementalLoadingCollection` в приложениях Windows Store, выполните следующее:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Для использования новой коллекции в Windows Phone примените методы расширения `ToCollection` для `IMobileServiceTableQuery<T>` и `IMobileServiceTable<T>`. Чтобы фактически загрузить данные, вызовите `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

При использовании коллекции, созданной путем вызова `ToCollectionAsync` или `ToCollection`, будет получена коллекция, привязанная к элементам управления пользовательского интерфейса. Эта коллекция поддерживает разбиение по страницам, то есть элемент управления может запросить в коллекции загрузку дополнительных элементов, что и будет выполнено коллекцией для этого элемента. На этом этапе пользовательский код не участвует, элемент управления запустит поток. Однако так как коллекция загружает данные из сети, ожидается, что иногда эта загрузка будет завершаться со сбоем. Для обработки таких сбоев можно переопределить метод `OnException` коллекции `MobileServiceIncrementalLoadingCollection`, что позволит обрабатывать исключения, возникающие при вызовах метода `LoadMoreItemsAsync`, осуществляемых элементами управления.

 Наконец, предположим, что в таблице много полей, но элементe управления требуется отобразить лишь некоторые из них. Можно воспользоваться указаниями в приведенном выше разделе <a href="#selecting">"Выбор конкретных столбцов"</a>  для выбора столбцов, отображаемых в пользовательском интерфейсе.

<h2><a name="authentication"></a>Практическое руководство. Проверка подлинности пользователей</h2>

Мобильные службы поддерживают проверку подлинности и авторизацию пользователей приложений с помощью ряда внешних поставщиков идентификации: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в разделе "Приступая к работе с проверкой подлинности" учебника ([Магазин Windows][Проверка подлинности магазина Windows]/[Windows Phone][Проверка подлинности Windows Phone])

Поддерживаются два потока проверки подлинности: a _server flow_ и _client flow_. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Поток клиента обеспечивает более глубокую интеграцию с зависящими от устройства возможностями, поскольку он опирается на пакет SDK, зависящий от устройства и от поставщика.

<h3>Серверный поток</h3>
Чтобы обеспечить управление с помощью мобильных служб процессом проверки подлинности в приложении Windows Store или Windows Phone, необходимо зарегистрировать приложение у своего поставщика идентификации. Затем в вашей мобильной службе необходимо настроить код приложения и секретный код, предоставленный поставщиком. Дополнительные сведения см. в разделе "Приступая к работе с проверкой подлинности" учебника ([Магазин Windows][Проверка подлинности магазина Windows]/[Windows Phone][Проверка подлинности Windows Phone]).

После регистрации своего поставщика идентификации достаточно вызвать метод [LoginAsync] со значением [MobileServiceAuthenticationProvider] для своего поставщика. Например, следующий код запускает вход в систему через поток сервера с помощью Facebook.

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

В этом случае мобильные службы управляют потоком проверки подлинности OAuth 2.0, отображая страницу входа выбранного поставщика и создавая маркер проверки подлинности мобильных служб после успешной регистрации у поставщика удостоверений. Метод [LoginAsync] возвращает ответ [MobileServiceUser], в котором содержится как [userId] авторизованного пользователя, так и [MobileServiceAuthenticationToken] в качестве веб-маркера JSON (JWT). Этот маркер можно кэшировать и повторно использовать до истечения срока его действия. Дополнительные сведения см. в разделе [Кэширование маркера проверки подлинности].

<div class="dev-callout"><b>Приложение Магазина Windows</b>
<p>При использовании поставщика входа в систему учетных записей Майкрософт для проверки подлинности пользователей в вашем приложении Магазина Windows необходимо также зарегистрировать этот пакет приложения в мобильных службах. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Чтобы узнать, как зарегистрировать пакет приложения Магазина Windows, изучите документ <a href="/ru-ru/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Регистрация пакета приложения Магазина Windows для проверки подлинности Майкрософт</a>. После регистрации информации пакета в мобильных службах вызовите метод <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> , передав значение <strong>true</strong> в параметре <em>useSingleSignOn</em>, чтобы повторно использовать учетные данные.</p>
</div>

<h3>Клиентский поток</h3>

Кроме того, приложение может независимо от этого взаимодействовать с поставщиком удостоверений, а затем передавать возвращенный маркер в мобильные службы для проверки подлинности. Этот клиентский поток позволяет пользователям выполнять единый вход или получать дополнительные данные о пользователе от поставщика удостоверений.

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

Пример использования учетной записи Майкрософт для обеспечения единого входа см. в разделе "Проверка подлинности приложения с единственной регистрацией" учебника ([Магазин Windows](/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/ru-ru/develop/mobile/tutorials/single-sign-on-wp8/)).

<h3><a name="caching"></a>Кэширование маркера проверки подлинности</h3>
В некоторых случаях можно избежать вызова метода входа после выполнения первой проверки подлинности пользователя. Чтобы кэшировать удостоверение текущего пользователя при первом входе в систему, в приложениях для Магазина Windows можно использовать класс [PasswordVault]. При каждом последующем входе будет проверяться, есть ли в кэше удостоверение пользователя. Если кэш пуст, пользователь будет перенаправлен на страницу входа в систему.

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


Для приложений Windows Phone данные можно шифровать и кэшировать с помощью класса [ProtectedData] и хранить конфиденциальные данные в изолированном хранилище.

<h2><a name="errors"></a>Практическое руководство. Обработка ошибок</h2>

Существует несколько способов выявления, проверки и устранения ошибок в мобильных службах.

В качестве примера можно отметить, что серверные сценарии регистрируются в мобильной службе и могут использоваться для выполнения целого ряда операций с данными, подвергаемыми вставке и обновлению, включая проверку и модификацию данных. Допустим, требуется определить и зарегистрировать серверный сценарий, который проверяет и модифицирует данные примерно так:

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

<h2><a name="untyped"></a>Практическое руководство. Работа с нетипизированными данными</h2>

Клиент .NET предназначен для строго типизированных сценариев. Тем не менее в некоторых случаях удобнее использовать менее типизированные сценарии; например, это может потребоваться при работе с объектами с открытой схемой. Этот сценарий активируется следующим образом. В запросах предварительно выполняется код LINQ и используется формат передачи по сети.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Вы получаете значения JSON, которые можно использовать в качестве контейнера свойств. Более подробные сведения о JToken и Json.NET см. в разделе [Json.NET](http://json.codeplex.com/)

<h2><a name="unit-testing"></a>Практическое руководство. Разработка модульных тестов</h2>

Метод `MobileServiceClient.GetTable` и запросы представляют собой интерфейсы. Это означает, что их можно легко имитировать в целях проверки, что дает возможность создать интерфейс `MyMockTable: IMobileServiceTable<TodoItem>`, реализующий логику тестирования.

<h2><a name="customizing"></a>Практическое руководство. Настройка клиента</h2>

### <a name="headers"></a>Практическое руководство. Настройка заголовков запроса

Может потребоваться присоединять пользовательский заголовок к каждому исходящему запросу или изменять коды статуса ответов. Это можно выполнить, настроив DelegatingHandler, как показано ниже:

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


### <a name="serialization"></a>Практическое руководство. Настраиваемая сериализация

Класс [MobileServiceClient](http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) предоставляет доступ к свойству `SerializerSettings` типа [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Это свойство позволяет задать многие из свойств Json.NET, например для приведения всех свойств к нижнему регистру:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

<h2><a name="nextsteps"></a>Дальнейшие действия</h2>

Теперь, когда вы закончили изучение этого концептуального раздела, узнайте более подробно, как можно выполнять дополнительные важные задачи в мобильных службах.

* [Приступая к работе с мобильными службами]
  <br/>Сведения об основах использования мобильных служб.

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [Проверка и изменение данных с помощью скриптов]
  <br/>Использование скриптов сервера в мобильных службах для проверки и изменения данных, отправленных из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Использование разбивки на страницы в запросах для управления количеством данных, обрабатываемых в одном запросе.

* [Авторизация пользователей с помощью скриптов]
  <br/>Сведения об использовании значения идентификатора пользователя, предоставляемого мобильными службами на основе пользователя, прошедшего проверку подлинности, для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. -->
[Что такое мобильные службы?]: #what-is
[Основные понятия]: #concepts
[Практическое руководство. Создание клиента мобильных служб]: #create-client
[Практическое руководство. Создание ссылки на таблицу]: #instantiating
[Практическое руководство. Запрос данных от мобильной службы]: #querying
[Фильтрация возвращаемых данных]: #filtering
[Сортировка возвращаемых данных]: #sorting
[Возврат данных на страницах]: #paging
[Выбор определенных столбцов]: #selecting
[Поиск данных по идентификатору]: #lookingup
[Практическое руководство. Привязка данных к пользовательскому интерфейсу в мобильной службе]: #binding
[Практическое руководство. Вставка данных в мобильную службу]: #inserting
[Практическое руководство. Изменение данных в мобильной службе]: #modifying
[Практическое руководство. Удаление данных в мобильной службе]: #deleting
[Практическое руководство. Использование оптимистичного параллелизма]: #optimisticconcurrency
[Практическое руководство. Проверка подлинности пользователей]: #authentication
[Практическое руководство. Обработка ошибок]: #errors
[Практическое руководство. Разработка модульных тестов]: #unit-testing
[Практическое руководство. Запрос данных от мобильной службы]: #querying
[Практическое руководство. Настройка клиента]: #customizing
[Практическое руководство. Работа с нетипизированными данными]: #untyped
[Настройка заголовков запроса]: #headers
[Настройка сериализации]: #serialization
[Дальнейшие действия]: #nextsteps
[Кэширование маркера проверки подлинности]: #caching
[Практическое руководство. Вызов настраиваемого интерфейса API]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[Учебник быстрого запуска для Магазина Windows]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started/
[Учебник быстрого запуска для Windows Phone]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-wp8/
[Учебник по данным Магазина Windows]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/
[Учебник по данным Windows Phone]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8/
[Проверка подлинности Магазина Windows]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Проверка подлинности Windows Phone]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8/
[PasswordVault]: http://msdn.microsoft.com/ru-ru/library/windows/apps/windows.security.credentials.passwordvault.aspx
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[ProtectedData]: http://msdn.microsoft.com/ru-ru/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Пакет SDK для мобильных служб]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Метод LoginAsync]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[userID:]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[Управляющие коды ASCII C0 и C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Интерфейс командной строки для управления таблицами мобильных служб]: http://www.windowsazure.com/ru-ru/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Учебник оптимистичного параллелизма]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/

[IncludeTotalCount]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Пользовательский API в SDK клиента мобильных служб Azure]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[Вызов из клиента настраиваемого интерфейса API]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
