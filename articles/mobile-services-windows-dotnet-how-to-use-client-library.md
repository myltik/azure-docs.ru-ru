<properties linkid="obile-services-how-to-dotnet-client" urlDisplayName="Клиентская библиотека .NET" pageTitle="Работа с мобильными службами клиентской библиотеки .NET" metaKeywords="Мобильные службы Azure, клиент .NET для мобильных служб, клиент .NET" description="Использование клиента .NET для мобильных служб Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Использование клиента .NET для мобильных служб Azure" authors=""  solutions="" writer="krisragh" manager="" editor=""  />

 



# Использование клиента .NET для мобильных служб Azure

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework" class="current">.NET Framework</a>
  	<a href="/ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


В этом руководстве показано, как реализовать типичные сценарии с использованием клиента .NET для мобильных служб Azure в приложениях Магазина Windows и приложениях Windows Phone. В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей и обработка ошибок. Если вы впервые работаете с мобильными службами, то рекомендуем сначала проработать учебник "Быстрый запуск мобильных служб" ([Учебник быстрого запуска для Магазина Windows]/[учебник быстрого запуска для Windows Phone]), а также учебник "Приступая к работе с данными в .NET" ([Учебник по данным Магазина Windows]/[Учебник по данным Windows Phone]). Для учебника по быстрому запуску потребуется пакет [SDK для мобильных служб]. Он позволяет настроить учетную запись и создать первую мобильную службу.


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

<h2><a name="setup"></a><span class="short-header">Настройка</span>Настройка и необходимые компоненты</h2>

Мы предполагаем, что вы создали мобильную службу и таблицу. Дополнительные сведения см. в разделе [Создание таблицы](http://go.microsoft.com/fwlink/?LinkId=298592). В коде, используемом в данном разделе, используется имя таблицы `TodoItem`, и эта таблица содержит следующие столбцы: `Id`, `Text` и `Complete`.

Соответствующий типизированный тип .NET на стороне клиента выглядит следующим образом:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}
	
Если динамическая схема включена, мобильные службы Azure автоматически создают новые столбцы на основе объекта в запросах вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема](http://go.microsoft.com/fwlink/?LinkId=296271).

<h2><a name="create-client"></a><span class="short-header">Создание клиента мобильных служб</span>Практическое руководство. Создание клиента мобильных служб</h2>

Следующий код создает объект `MobileServiceClient`, используемый для доступа к мобильной службе. 

			
	MobileServiceClient client = new MobileServiceClient( 
		"AppUrl", 
		"AppKey" 
	); 

В приведенном выше коде замените `AppUrl` и `AppKey` на URL-адрес мобильной службы и ключ приложения соответственно. Оба варианта доступны на портале управления Azure: выберите свою мобильную службу и щелкните "Панель управления".

<h2><a name="instantiating"></a><span class="short-header">Создание ссылки на таблицу</span>Практическое руководство. Создание ссылки на таблицу</h2>

Весь код, который обращается к данным или изменяет данные в таблице мобильных служб, вызывает функции объектов `MobileServiceTable`. Ссылку на таблицу можно получить, вызвав функцию [GetTable](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554275.aspx) для экземпляра `MobileServiceClient`. 

    IMobileServiceTable<TodoItem> todoTable = 
		client.GetTable<TodoItem>();

Это типизированная модель сериализации; обсуждение <a href="#untyped">типизированной модели сериализации</a> см. ниже.
			
<h2><a name="querying"></a><span class="short-header">Запрос данных</span>Практическое руководство. Запрос данных от мобильной службы</h2>

В этом разделе описывается, как отправлять запросы мобильной службе. В подразделах описываются различные аспекты, например сортировка, фильтрация и разбиение по страницам. 

>[WACOM.NOTE] Для предотвращения возврата всех строк по умолчанию используется размер страницы, управляемый сервером. Это предотвращает негативное воздействие больших наборов данных на функционирование службы. Для возвращения более 50 строк используйте метод `Take`, как описано в разделе [Возвращение данных на страницах].  
			
### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий код иллюстрирует способ фильтрации данных, включая предложение `Where` в запросе. Он возвращает все элементы из `todoTable`, свойство `Complete` которого равно `false`. Функция `Where` применяет предикат фильтрации строк для запросов к таблице. 

	// This query filters out completed TodoItems and 
	// items without a timestamp. 
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Можно просмотреть URI запроса, отправленного в мобильную службу, с помощью программы проверки сообщений, включая средства разработчика браузера и Fiddler. Если взглянуть на следующий URI запроса, можно отметить, что мы изменяем саму строку запроса:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1				   
На стороне сервера такой запрос обычно должен преобразовываться примерно в такой запрос SQL:
			
	SELECT * 
	FROM TodoItem 			
	WHERE ISNULL(complete, 0) = 0
			
Функция, которая передается в метод `Where`, может иметь произвольное число условий. Например, следующая строка:

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

Инструкция `where`, указанная выше, будет находить элементы со статусом `Complete` в состоянии false и с ненулевым значением `Text`.

Это же выражение можно написать с использованием нескольких строк:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Эти два способа эквивалентны и могут быть взаимозаменяемыми.  Первый вариант - объединение нескольких предикатов в одном запросе - является более компактным и рекомендуемым.

Предложение `where` поддерживает операции, которые будут преобразованы в поднабор OData мобильных служб. К ним относятся операторы отношения (==, !=, <, <=, >, >=), арифметические операторы (+, -, /, *, %), точность чисел (Math.Floor, Math.Ceiling), строковые функции (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), свойства дат (Year, Month, Day, Hour, Minute, Second), свойства доступа к объекту, а также выражения, сочетающие все эти элементы.

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

В следующем коде показано, как сортировать данные, включая в запрос функцию `OrderBy` или `OrderByDescending`. Он возвращает элементы из `todoTable`, упорядочивая их в восходящем порядке по полю `Text`. 

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();			

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах

По умолчанию сервер возвращает только первые 50 строк. Число возвращенных строк можно увеличить путем вызова метода [Take]. Использование `Take` вместе с методом [Skip] для запроса определенной страницы общего набора данных, возвращенных запросом. При выполнении следующего запроса будут возвращены три главных элемента в таблице. 

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
			
Можно также использовать метод [IncludeTotalCount], чтобы запрос возвращал общее количество <i>всех</i> записей, которые должны быть возвращены без учета указанных предложений paging/limit:

	query = query.IncludeTotalCount();

Это упрощенный сценарий передачи жестко запрограммированных значений разбиения по страницам в методы `Take` и `Skip`. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице. 

### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

Можно задать набор свойств, включаемых в результаты, добавив к запросу предложение `Select`. Например, в следующем коде показано, как выбрать только одно поле, а также способы выбора и форматирования нескольких полей:

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

<h2><a name="inserting"></a><span class="short-header">Вставка данных</span>Практическое руководство. Вставка данных в мобильную службу</h2>

<div class="dev-callout"><strong>Примечание</strong>. <p>Если вы хотите выполнить операцию вставки, поиска, удаления или обновления для данного типа, необходимо создать члена с именем <strong>Id</strong>. Именно поэтому в примере класса <strong>TodoItem</strong> используется член с именем <strong>Id</strong>. Допустимое значение идентификатора всегда должно присутствовать в операциях обновления и удаления.</p> </div>

В следующем коде показано, как вставить новые строки в таблицу. Параметр содержит данные, которые вставляются в качестве объекта .NET.

	await todoTable.InsertAsync(todoItem);

Если уникальный пользовательский идентификатор не включен в `todoItem` при передаче этого объекта в вызов `todoTable.InsertAsync`, значение идентификатора будет создано сервером, заданным в объекте `todoItem`, возвращенным клиенту. 

Мобильные службы поддерживают уникальные настраиваемые строковые значения идентификатора таблицы. Это позволяет приложениям использовать настраиваемые значения, например адреса электронной почты или имена пользователей, для столбца идентификатора таблицы мобильных служб. Если значение идентификатора строки не указано при вставке новых записей в таблицу, мобильные службы создают уникальное значение идентификатора.

Поддержка строковых идентификаторов предоставляет разработчикам следующие преимущества:

+ Идентификаторы можно создавать без обмена данными с базой данных.
+ Можно легко объединять записи из разных таблиц или баз данных.
+ Значения идентификаторов можно удобно интегрировать с логикой приложения.

Серверные скрипты также можно использовать для задания значений идентификаторов. В следующем примере скрипта создается настраиваемый идентификатор GUID, который назначается идентификатору новой записи. Этот идентификатор похож на значение, создаваемое мобильными службами, если не передать значение идентификатора записи.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Если приложение предоставляет значение для идентификатора, мобильные службы сохраняют его как есть. (В том числе с начальными или конечными пробелами.) Пробелы не удаляются из идентификатора.

Значение идентификатора должно быть уникальным и не должно содержать символы из следующих наборов:

+ Управляющие символы: [0x0000-0x001F] и [0x007F-0x009F]. Дополнительные сведения см. в статье [Управляющие коды ASCII C0 и C1].
+  Печатные символы: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Идентификаторы "." и ".."


Также для таблиц можно использовать целочисленные идентификаторы. Для этого необходимо создать таблицу с помощью команды `mobile table create` с параметром `--integerId`. Эта команда используется в интерфейсе командной строки (CLI) Azure. Дополнительные сведения об использовании CLI см. в статье [Интерфейс командной строки для управления таблицами мобильных служб].


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


<h2><a name="modifying"></a><span class="short-header">Изменение данных</span>Практическое руководство. Изменение данных в мобильной службе</h2>

Следующий код показывает, как добавить новую информацию в существующий экземпляр с тем же идентификатором. Параметр содержит данные, которые обновляются в качестве объекта .NET.

	await todoTable.UpdateAsync(todoItem);


Для вставки нетипизированных данных можно использовать Json.NET. Обратите внимание, что при выполнении обновления должен быть указан идентификатор, поскольку с его помощью мобильная служба определяет, какой экземпляр нужно обновить. Идентификатор можно получить из результатов вызова `InsertAsync`.

	JObject jo = new JObject(); 
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);
			
Если предпринимается попытка обновить элемент без предоставления значения Id, служба не сможет определить, какой экземпляр следует обновить, поэтому пакет SDK для мобильных служб вызовет исключение `ArgumentException`. 
			
			
<h2><a name="deleting"></a><span class="short-header">Удаление данных</span>Практическое руководство. Удаление данных в мобильной службе</h2>

Следующий код показывает, как удалить существующий экземпляр. Экземпляр идентифицируется по полю Id в `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Для удаления нетипизированных данных можно использовать Json.NET. Обратите внимание, что при выполнении запроса на удаление должен быть указан идентификатор, поскольку с его помощью мобильная служба определяет, какой экземпляр нужно удалить. Для запроса на удаление требуется только идентификатор; другие свойства не передаются в службу; любые передаваемые свойства будут игнорироваться службой. В результате вызова `DeleteAsync` обычно будет выдаваться ответ `null`. Идентификатор для ввода можно получить в результате вызова `InsertAsync`.

	JObject jo = new JObject(); 
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);
			
Если предпринимается попытка удалить элемент без заданного поля Id, служба не сможет определить, какой экземпляр нужно удалить, поэтому от службы будет получен ответ `MobileServiceInvalidOperationException`. Аналогичным образом, при попытке удалить нетипизированный элемент, для которого не задано поле Id, от службы также будет получен ответ `MobileServiceInvalidOperationException`. 
			


<h2><a name="optimisticconcurrency"></a><span class="short-header">Оптимистичный параллелизм</span>Практическое руководство. Использование оптимистичного параллелизма</h2>

В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. При управлении оптимистичным параллелизмом предполагается, что каждая транзакция может фиксироваться, поэтому не использует блокировки каких-либо ресурсов. Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается. 

Мобильные службы поддерживают управление оптимистичным параллелизмом путем отслеживания изменений на каждый элемент с помощью `__version` системы свойства столбца, определенного для каждой таблицы, созданные службами мобильных устройств. При каждом обновлении записи мобильные службы задают новое значение свойства `__version` для этой записи. Во время каждого запроса обновления свойство `__version` записи, включенное в запрос, сравнивается с тем же свойством для записи на сервере. Если версия, переданная с запросом, не соответствует серверу, клиентская библиотека .NET для мобильных служб вызывает исключение `MobileServicePreconditionFailedException<T>`. Тип, включенный в исключение, является записью с сервера, которая содержит версию записи на сервере. Затем приложение может использовать эти сведения, чтобы решить, следует ли повторно выполнить запрос обновления с надлежащим значением `__version` с сервера для фиксации изменений.  

Для включения проверки оптимистического параллелизма приложение определяет столбец в классе таблицы для системного свойства `__version`. В следующем определении приведен пример.

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


Приложения, использующие нетипизированные таблицы, включают оптимистичный параллелизм, устанавливая флаг `Version` в таблице `SystemProperties` следующим образом.

	//Enable optimistic concurrency by retrieving __version 
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


В следующем коде показано, как разрешить конфликт записи после его определения. Правильное значение `__version` должно быть включено в вызов `UpdateAsync()` для фиксации исправления.

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





Более полный пример использования оптимистичного параллелизма для мобильных служб см. в [учебнике по оптимистичного параллелизму].



<h2><a name="binding"></a><span class="short-header">Отображение данных</span>Практическое руководство. Привязка данных к пользовательскому интерфейсу в мобильной службе</h2>

В этом разделе показано, как отображать возвращенные объекты данных с использованием элементов пользовательского интерфейса. Для запроса незавершенных элементов в `todoTable` и отображения его в простом списке необходимо выполнить следующий пример кода для привязки источника к списку посредством запроса. С помощью `MobileServiceCollection` создается коллекция привязок, учитывающая мобильные службы.
			 
	// This query filters out completed TodoItems. 
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;            
			
	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Некоторые элементы управления в среде выполнения Windows поддерживают интерфейс [ISupportIncrementalLoading](http://msdn.microsoft.com/ru-ru/library/windows/apps/Hh701916). Этот интерфейс позволяет элементам управления запрашивать дополнительные данные во время прокрутки, выполняемой пользователем. Существует встроенная поддержка этого интерфейса для приложений Магазина Windows через `MobileServiceIncrementalLoadingCollection`, который автоматически обрабатывает вызовы из элементов управления. Чтобы использовать `MobileServiceIncrementalLoadingCollection` в приложениях Магазина Windows, выполните следующие действия:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Чтобы использовать новую коллекцию в Windows Phone, используйте методы расширения `ToCollection` в `IMobileServiceTableQuery<T>` и `IMobileServiceTable<T>`. Чтобы фактически загрузить данные, вызовите `LoadMoreItemsAsync()`. 

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection(); 
	await items.LoadMoreItemsAsync();         

При использовании коллекции, созданную путем вызова `ToCollectionAsync` или `ToCollection`, можно получить коллекцию, которая может быть привязана к элементам управления пользовательским интерфейсом. Эта коллекция поддерживает разбиение по страницам, то есть элемент управления может запросить в коллекции загрузку дополнительных элементов, что и будет выполнено коллекцией для этого элемента. На этом этапе пользовательский код не участвует, элемент управления запустит поток. Однако так как коллекция загружает данные из сети, ожидается, что иногда эта загрузка будет завершаться со сбоем. Чтобы обработать подобные ошибки, можно переопределить метод `OnException` в `MobileServiceIncrementalLoadingCollection` для обработки исключений, которые образуются при вызове `LoadMoreItemsAsync`, которые выполняются элементами управления. 

Наконец, представьте, что в таблице содержится множество полей, однако необходимо отобразить только те из них, которые находятся под вашим управлением. Можно использовать руководство в разделе <a href="#selecting">Выбрать конкретные столбцы</a> выше, чтобы выбрать определенные столбцы для отображения в пользовательском интерфейсе.

<h2><a name="authentication"></a><span class="short-header">Проверка подлинности</span>Практическое руководство. Проверка подлинности пользователей</h2>

Мобильные службы поддерживают проверку подлинности и авторизацию пользователей с помощью различных внешних поставщиков идентификаторов: Facebook, Google, учетная запись Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике "Приступая к работе с проверкой подлинности" ([Магазин Windows][Windows Store authentication]/[Windows Phone][Windows Phone authentication]).

Поддерживаются два потока проверки подлинности: _серверный_ и _клиентский_. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

<h3>Серверный поток</h3>
Чтобы мобильная служба могла выполнять процесс проверки подлинности в вашем приложении для магазина Windows или для Windows Phone, необходимо зарегистрировать приложение у поставщика удостоверений. Затем в вашей мобильной службе необходимо настроить код приложения и секретный код, предоставленный поставщиком. Дополнительные сведения см. в учебнике "Приступая к работе с проверкой подлинности" ([Магазин Windows][Windows Store authentication]/[Windows Phone][Windows Phone authentication]).

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

В этом случае мобильная служба управляет потоком проверки подлинности OAuth 2.0, отображая страницу входа выбранного поставщика и генерируя маркер проверки подлинности мобильных служб после успешного соединения с поставщиком удостоверений. [Метод LoginAsync] возвращает ответ [MobileServiceUser], в котором содержится как [userId] пользователя, прошедшего проверку, так и [MobileServiceAuthenticationToken], в качестве веб-маркера JSON (JWT). Этот маркер можно кэшировать и повторно использовать до истечения срока его действия. Дополнительные сведения см. в разделе [Кэширование маркера проверки подлинности].

<div class="dev-callout"><b>Приложение Магазина Windows</b>
<p>При использовании поставщика входа в систему учетных записей Майкрософт для проверки подлинности пользователей в вашем приложении Магазина Windows необходимо также зарегистрировать этот пакет приложения в мобильных службах. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Чтобы узнать, как зарегистрировать пакет приложения Магазина Windows, изучите документ <a href="/ru-ru/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Регистрация пакета приложения Магазина Windows для проверки подлинности Майкрософт</a>. После регистрации сведений пакета в мобильных службах вызовите метод <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> путем предоставления значения <strong>true</strong> для параметра <em>useSingleSignOn</em> в целях повторного использования учетных данных.</p>
</div>

<h3>Клиентский поток</h3>

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

Более полный пример использования учетной записи Майкрософт для обеспечения единого входа см. в разделе "Проверка подлинности приложения с помощью единого входа" ([Магазин Windows](/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/ru-ru/develop/mobile/tutorials/single-sign-on-wp8/)).

<h3><a name="caching"></a>Кэширование маркера проверки подлинности</h3>
В некоторых случаях вызова способа входа в систему можно избежать первого выполнения проверки подлинности пользователя. Можно использовать [PasswordVault] для приложений Магазина Windows для кэширования удостоверения текущего пользователя при первом входе в систему; при каждом последующем входе будет выполняться проверка наличия удостоверения пользователя в кэше. Если кэш пуст, пользователь будет перенаправлен на страницу входа в систему. 

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


При использовании приложений Windows Phone можно шифровать данные кэширования с использованием класса [ProtectedData] и сохранять конфиденциальные данные в отдельном хранилище.

<h2><a name="errors"></a><span class="short-header">Обработка ошибок</span>Практическое руководство. Обработка ошибок</h2>

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

<h2><a name="untyped"></a><span class="short-header">Работа с нетипизированными данными</span>Практическое руководство. Работа с нетипизированными данными</h2>

Клиентский компонент .NET предназначен для строго типизированных сценариев. Тем не менее в некоторых случаях удобнее использовать менее типизированные сценарии; например, это может потребоваться при работе с объектами с открытой схемой. Этот сценарий активируется следующим образом. В запросах нужно отказаться от LINQ и использовать телеграфный формат.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");			

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Вы будете получать значения JSON, которые можно использовать в качестве контейнера свойств. Более подробные сведения о JToken и Json.NET см. в разделе [Json.NET](http://json.codeplex.com/)

<h2><a name="unit-testing"></a><span class="short-header">Разработка тестов</span>Практическое руководство. Разработка модульных тестов</h2>

Значение, возвращаемое `MobileServiceClient.GetTable`, и запросы являются интерфейсами. Это без труда определяет их состояние как mockable в целях тестирования, поэтому можно создать `MyMockTable : IMobileServiceTable<TodoItem>` для реализации логики тестирования.

<h2><a name="customizing"></a><span class="short-header">Настройка клиента</span>Практическое руководство. Настройка клиента</h2>

### <a name="headers"></a>Практическое руководство. Настройка заголовков запроса

Вам может потребоваться присоединить настраиваемый заголовок к каждому исходящему запросу или изменить коды состояний ответов. Для этого можно настроить DelegatingHandler следующим образом:

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
			
			
### <a name="serialization"></a>Практическое руководство. Настройка сериализации

Класс [MobileServiceClient](http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) предоставляет свойство S`erializerSettings` типа [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

С помощью этого свойства можно задать свойства Json.NET (их много), включая одно, к примеру, для преобразования всех свойств в нижний регистр:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

<h2><a name="nextsteps"></a>Дальнейшие действия</h2>

Теперь, когда вы закончили изучение этого концептуального раздела, узнайте более подробно, как можно выполнять дополнительные важные задачи в мобильных службах.

* [Приступая к работе с мобильными службами]
  <br/>Сведения об основах использования мобильных служб.

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности учетных данных пользователей приложения с помощью поставщика удостоверений.

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

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

<!-- Images. -->



<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[Учебник быстрого запуска для Магазина Windows]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started/
[Учебник быстрого запуска для Windows Phone]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-wp8/
[Учебник по данным Магазина Windows]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/
[Учебник по данным Windows Phone]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8/
[Проверка подлинности Магазина Windows]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Проверка подлинности Windows Phone]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8/
[PasswordVault]: http://msdn.microsoft.com/ru-ru/library/windows/apps/windows.security.credentials.passwordvault.aspx
[SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[ProtectedData]: http://msdn.microsoft.com/ru-ru/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[SDK для мобильных служб]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Метод LoginAsync]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[Управляющие коды ASCII C0 и C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Интерфейс командной строки для управления таблицами мобильных служб]: http://www.windowsazure.com/ru-ru/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[учебнике по оптимистичного параллелизму]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/

[IncludeTotalCount]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn250560.aspx 
[Skip]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn250574.aspx 

