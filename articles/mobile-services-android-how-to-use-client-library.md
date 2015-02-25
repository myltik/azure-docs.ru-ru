<properties pageTitle="Работа с клиентской библиотекой Android мобильных служб" description="Узнайте, как использовать клиент Android для мобильных служб Azure." services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/20/2014" ms.author="ricksal"/>

# Использование клиентской библиотеки Android для мобильных служб

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">Платформа .NET Framework</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


В этом руководстве показано, как реализовать типичные сценарии с использованием клиента Android для мобильных служб Azure.  В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей, обработка ошибок и настройка клиента. Впервые осваивая мобильные службы, следует вначале ознакомиться с [кратким руководством по мобильным службам][Приступая к работе с мобильными службами]. Учебник quickstart поможет вам настроить учетную запись и создать свою первую мобильную службу.

Примеры написаны на Java и используют [Пакет SDK для мобильных служб]. Чтобы изучить этот учебник, необходим [пакет Android SDK](https://go.microsoft.com/fwLink/p/?LinkID=280125&clcid=0x409), который включает интегрированную среду разработки Eclipse (IDE) и подключаемый модуль средств разработки Android (ADT). Пакет Mobile Services SDK поддерживает Android 2.2 и более поздние версии, но мы рекомендуем использовать в качестве цели Android 4.2 или более поздние версии.



## Оглавление

- [Что такое мобильные службы?]
- [Основные понятия]
- [Настройка и необходимые компоненты]
- [Практическое руководство. Создание клиента мобильных служб]
- [Практическое руководство. Создание ссылки на таблицу]
	- [Структура API]
- [Практическое руководство. Запрос данных из мобильной службы]
	- [Фильтрация возвращаемых данных]
    - [Сортировка возвращаемых данных]
	- [Возврат данных на страницах]
	- [Выбор определенных столбцов]
	- [Практическое руководство. Сцепка методов запросов]
- [Практическое руководство. Вставка данных в мобильную службу]
- [Практическое руководство. Обновление данных в мобильной службе]
- [Практическое руководство. Удаление данных в мобильной службе]
- [Практическое руководство. Поиск конкретного элемента]
- [Практическое руководство. Работа с нетипизированными данными]
- [Практическое руководство. Привязка данных к пользовательскому интерфейсу]
	- [Практическое руководство. Определение макета]
	- [Практическое руководство. Определение адаптера]
	- [Практическое руководство. Использование адаптера]
- [Практическое руководство. Проверка подлинности пользователей]
	- [Кэширование маркеров проверки подлинности]
- [Практическое руководство. Обработка ошибок]
- [Практическое руководство. Настройка клиента]
	- [Настройка заголовков запроса]
	- [Настройка сериализации]
- [Дальнейшие действия][]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]


<h2><a name="setup"></a>Настройка и необходимые компоненты</h2>

Мы предполагаем, что вы создали мобильную службу и таблицу. Дополнительные сведения см. в разделе [Создание таблицы](http://go.microsoft.com/fwlink/p/?LinkId=298592). В коде, используемом в данном разделе, мы предполагаем, что имя таблицы *ToDoItem* и что она содержит следующие столбцы:

<ul>
<li>id</li>
<li>text</li>
<li>complete</li>
<li>due</li>
<li>duration</li>
</ul>

Соответствующий типизированный клиентский объект выглядит следующим образом:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
		private Date due
		private Integer duration;
	}
	
Если динамическая схема включена, мобильные службы Azure автоматически создают новые столбцы на основе объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема]( http://go.microsoft.com/fwlink/p/?LinkId=296271)..

<h2><a name="create-client"></a>Практическое руководство. Создание клиента мобильных служб</h2>

Следующий код создает объект [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html), используемый для доступа к мобильной службе. 

			MobileServiceClient mClient = new MobileServiceClient(
					"MobileServiceUrl", // Replace with the above Site URL
					"AppKey", 			// replace with the Application Key 
					this)

В приведенном выше коде замените `MobileServiceUrl` и `AppKey` на URL-адрес мобильной службы и ключ приложения соответственно. Оба варианта доступны на портале управления Azure: выберите свою мобильную службу и щелкните *Dashboard*.

<h2><a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу</h2>

Самый простой способ запросить или изменить данные в мобильной службе - использование *typed programming model*, поскольку Java является строго типизированным языком (позже мы расскажем о модели *untyped*). Эта модель обеспечивает простую сериализацию и десериализацию в формате JSON с помощью библиотеки <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> при передаче данных между клиентом и мобильной службой: разработчику не нужно ничего делать, платформа сама занимается обработкой.

Первое, что необходимо сделать для запроса или изменения данных - создать объект [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835), вызвав метод **getTable** объекта [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html).  Мы рассмотрим две перегрузки этого метода:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

В следующем коде *mClient* представляет собой ссылку на ваш клиент мобильной службы.

[Первая перегрузка](http://go.microsoft.com/fwlink/p/?LinkId=296839) используется, когда имя класса и имя таблицы одинаковы:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[Вторая перегрузка](http://go.microsoft.com/fwlink/p/?LinkId=296840) используется, когда имя таблицы отличается от имени типа.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

 


### <a name="api"></a>Структура API

В табличных операциях мобильных служб используется модель асинхронного обратного вызова. У всех методов, использующих запросы и такие операции, как вставка, обновление и удаление, есть параметр, который является объектом обратного вызова. Этот объект всегда содержит метод **OnCompleted**. Метод **OnCompleted** содержит один параметр, являющийся объектом **Exception**, который можно проверить для определения успешности вызова метода. Объект **Exception** со значением NULL указывает на успешное завершение, в противном случае значение объекта **Exception** описывает причину ошибки.

Существует несколько объектов обратного вызова. То, какой из них вы будете использовать, зависит от того, будете ли вы запрашивать, изменять или удалять данные. Параметры метода *onCompleted* различаются в зависимости от того, частью какого объекта обратного вызова он является.


<h2><a name="querying"></a>Практическое руководство. Запрос данных из мобильной службы</h2>

В этом разделе описывается, как отправлять запросы мобильной службе. В подразделах описываются различные аспекты, например сортировка, фильтрация и разбиение по страницам. Наконец, мы обсудим, как можно объединять эти операции.

Следующий код возвращает все элементы в таблице *ToDoItem*. 

		mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
				public void onCompleted(List<ToDoItem> result, int count,
					Exception exception, ServiceFilterResponse response) {
					if (exception == null) {
						for (ToDoItem item : result) {
                			Log.i(TAG, "Read object with ID " + item.id);  
						}
					}
				}
			});

Подобные запросы используют объект обратного вызова [**TableQueryCallback&lt;E&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=296849).

Параметр *result* возвращает результирующий набор запроса, а код внутри ветви успешного выполнения теста *exception* показывает, как анализировать отдельные строки.


### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий код возвращает все элементы из таблицы *ToDoItem*, поле *complete* которой имеет значение  *false*.  *mToDoTable* - это ссылка на таблицу мобильной службы, которую мы создали ранее. 

		mToDoTable.where().field("complete").eq(false)
				  .execute(new TableQueryCallback<ToDoItem>() {
						public void onCompleted(List<ToDoItem> result, 
												int count, 
												Exception exception,
												ServiceFilterResponse response) {
				if (exception == null) {
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);  
					}
				} 
			}
		});

Для фильтрации вызовите метод [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) для ссылки на таблицу. После этого вызовите метод [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869), а затем - метод, который определяет логический предикат. Возможные методы предиката: [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) и т. д.

Этого достаточно для сравнения числовых и строковых полей с указанными значениями. Однако вы можете сделать гораздо больше.

Например, можно фильтровать данные по датам. Можно сравнить все поле даты, но также можно сравнивать части даты, используя такие методы, как [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) and [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472). Следующий частичный код добавляет фильтр для элементов, для которых *due date* равно 2013.

		mToDoTable.where().year("due").eq(2013)

Можно применять разнообразные сложные фильтры к строковым полям с помощью таких методов, как [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495), and [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496). Следующий частичный код фильтрует строки таблицы, столбец *text* которых начинается с "PRI0".

		mToDoTable.where().startsWith("text", "PRI0")

Для числовых полей также можно применять обширный спектр сложных фильтров с помощью таких методов, как [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506), and [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507). Следующий частичный код фильтрует строки таблицы, у которых *duration* является четным числом.

		mToDoTable.where().field("duration").mod(2).eq(0)


Предикаты можно объединить с помощью методов [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) and [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515). Этот частичный код объединяет два приведенных выше примера.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")

Кроме того, можно группировать логические операторы и вкладывать их друг в друга, как показано в этом частичном коде:

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))

Более подробное описание и примеры фильтрации см. в статье [Исследование возможностей модели запросов клиента мобильных служб для Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

Следующий код возвращает все элементы из таблицы *ToDoItems*, упорядоченные по полю *text* в порядке возрастания. *mToDoTable* - это ссылка на таблицу мобильной службы, которую вы создали ранее.

		mToDoTable.orderBy("text", QueryOrder.Ascending)
			.execute(new TableQueryCallback<ToDoItem>() { 
				/* same implementation as above */ 
			}); 

Первый параметр метода[**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) - это строка, которая совпадает с именем поля, по которому выполняется сортировка.

Второй параметр использует перечисление[**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521), чтобы указать, следует ли сортировать данные по возрастанию или убыванию.

Обратите внимание, что если используется фильтрация с помощью метода ***where***, метод ***where*** необходимо вызывать до метода ***orderBy***.

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах

Первый пример показывает, как выбрать 5 верхних элементов из таблицы. Этот запрос возвращает элементы из таблицы  *ToDoItems*.  *mToDoTable* - это ссылка на таблицу мобильной службы, которую вы создали ранее.

		mToDoTable.top(5)
	            .execute(new TableQueryCallback<ToDoItem>() {	
	            public void onCompleted(List<ToDoItem> result, 
										int count,
	                    				Exception exception, 
										ServiceFilterResponse response) {
	                if (exception == null) {
	                    for (ToDoItem item : result) {
                			Log.i(TAG, "Read object with ID " + item.id);  
	                    }
	                } 
	            }
	        });

Далее мы определим запрос, который пропускает первые 5 элементов, а затем возвращает следующие 5 элементов.

		mToDoTable.skip(5).top(5)
	            .execute(new TableQueryCallback<ToDoItem>() {	
	            // implement onCompleted logic here
	        });


### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

Следующий код показывает, как получить все элементы из таблицы  *ToDoItems*, но отображает только поля *complete* и  *text*.  *mToDoTable* - это ссылка на таблицу мобильной службы, которую мы создали ранее.

		mToDoTable.select("complete", "text")
	            .execute(new TableQueryCallback<ToDoItem>() { 
					/* same implementation as above */ 
			}); 

	
Здесь параметры функции select являются строковыми именами столбцов таблицы, которые требуется вернуть.

Метод [**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) должен следовать за такими методами, как [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) и [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313), если они используются. После него можно вызывать такие методы, как [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731).

### <a name="chaining"></a>Практическое руководство. Сцепка методов запросов 

Методы, используемые в таблицах мобильных служб, могут быть объединены. Это позволяет, например, выбирать определенные столбцы или фильтрованные строки, которые сортируются и разбиваются на страницы. Можно создавать весьма сложные логические фильтры.

Это возможно из-за того, что используемые методы запроса возвращают объекты [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551), в которых в свою очередь можно вызывать дополнительные методы. Чтобы завершить серию методов и фактически выполнить запрос, вызовите метод [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554).

Ниже приведен пример кода, где *mToDoTable* - это ссылка на таблицу мобильных служб *ToDoItem*.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)				
					.execute(new TableQueryCallback<ToDoItem>() { 
						/* code to execute */ 
				});

Главное требование при объединении методов состоит в том, что метод *where* и предикаты должны идти первыми. После этого можно вызвать последующие методы в том порядке, который лучше всего соответствует требованиям вашего приложения.


<h2><a name="inserting"></a>Практическое руководство. Вставка данных в мобильную службу</h2>

В следующем коде показано, как вставить новые строки в таблицу.

Сначала необходимо создать экземпляр класса *ToDoItem* и задать его свойства.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		mToDoItem.duration = 5; 
		
 Затем вызовите метод [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=296862).

		mToDoTable.insert(mToDoItem, new TableOperationCallback<ToDoItem>() {
			public void onCompleted(ToDoItem entity, 
								Exception exception, 
								ServiceFilterResponse response) {	
				if (exception == null) {
                		Log.i(TAG, "Read object with ID " + entity.id);  
				} 
			}
		});

Для операций **вставки** объектом обратного вызова является [**TableOperationCallback&lt;ToDoItem&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=296865).

Параметр entity метода **onCompleted** содержит только что вставленный объект. В следующем примере показано, как получить доступ к *id* вставленной строки.

Мобильные службы поддерживают уникальные настраиваемые строковые значения идентификатора таблицы. Это позволяет приложениям использовать настраиваемые значения, например, адреса электронной почты или имена пользователей, для столбца идентификатора таблицы мобильных служб. Например, если требуется идентифицировать каждую запись по адресу электронной почты, можно использовать следующий объект JSON.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		mToDoItem.duration = 5; 

Если значение идентификатора строки не указано при вставке новых записей в таблицу, мобильные службы создают уникальное значение идентификатора.

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

Значение для `id` должно быть уникальным и не должно содержать символы из следующих наборов:

+ Управляющие символы: [0x0000-0x001F] и [0x007F-0x009F]. Дополнительные сведения см. в статье [Управляющие коды ASCII C0 и C1].
+  Печатные символы: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Идентификаторы "." и ".."

Также для таблиц можно использовать целочисленные идентификаторы. Для этого необходимо создать таблицу с помощью команды  `mobile table create`, используя параметр `--integerId`. Эта команда используется в интерфейсе командной строки (CLI) Azure. Дополнительные сведения об использовании CLI см. в статье [Интерфейс командной строки для управления таблицами мобильных служб].


<h2><a name="updating"></a>Практическое руководство. Обновление данных в мобильной службе</h2>

В следующем коде показано, как обновить данные в таблице. В этом примере *mToDoItem* - это ссылка на элемент в таблице *ToDoItem*, мы обновляем ее свойство *duration*.

		mToDoItem.duration = 5;
		mToDoTable.update(mToDoItem, new TableOperationCallback<ToDoItem>() {
			public void onCompleted(ToDoItem entity, 
									Exception exception, 
									ServiceFilterResponse response) {
				if (exception == null) {
            			Log.i(TAG, "Read object with ID " + entity.id);  
				} 
			}
		});

Обратите внимание, что объект обратного вызова и параметры метода *onCompleted* такие же, как при вставке.

<h2><a name="deleting"></a>Практическое руководство. Удаление данных в мобильной службе</h2>

В следующем коде показано, как удалить данные из таблицы. Пример удаляет существующий элемент из таблицы ToDoItem, используя ссылку на элемент, в этом случае это *mToDoItem*.

		mToDoTable.delete(mToDoItem, new TableDeleteCallback() {
		    public void onCompleted(Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

Обратите внимание, что при операции *delete* объект обратного вызова - это [**TableDeleteCallback**](http://go.microsoft.com/fwlink/p/?LinkId=296858), а метод **onCompleted** отличается тем, что строки таблицы не возвращаются.

В следующем примере кода показан еще один способ для этого. Пример удаляет существующий элемент из таблицы ToDoItem, указывая значение в поле идентификатора строки (предполагается, что он равен "37BBF396-11F0-4B39-85C8-B319C729AF6D"). 

		mToDoTable.delete("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableDeleteCallback() {
		    public void onCompleted(Exception exception, 
		            ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

<h2><a name="lookup"></a>Практическое руководство. Поиск конкретного элемента</h2>
Иногда вам требуется найти определенный элемент по его идентификатору *id* в отличие от запросов, где вы обычно получаете коллекцию элементов, отвечающих некоторым критериям. В следующем коде показано, как это сделать для случая *id* = "37BBF396-11F0-4B39-85C8-B319C729AF6D".

		mToDoTable.lookUp("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableOperationCallback<ToDoItem>() {
		    public void onCompleted(item entity, Exception exception,
		            ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Read object with ID " + entity.id);    
		        }
		    }
		});


<h2><a name="untyped"></a>Практическое руководство. Работа с нетипизированными данными</h2>

Нетипизированная модель программирования предоставляет полный контроль над сериализацией JSON. Существует несколько сценариев, в которых вы можете использовать ее, например, если таблица мобильной службы содержит множество столбцов, а вам нужно ссылаться только на небольшое их число. Для использования типизированной модели необходимо определить все столбцы таблицы мобильной службы в классе данных. Но с нетипизированной моделью требуется определить только столбцы, которые вы будете использовать.

Аналогично типизированной модели сначала вы получаете ссылку на таблицу, но в данном случае это объект [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733). Чтобы получить ссылку, вызовите метод [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) экземпляра клиента мобильных служб.


Применяется следующая перегрузка этого метода, которая используется при работе с нетипизированными моделями программирования на основе JSON:

		public class MobileServiceClient {
		    public MobileServiceJsonTable getTable(String name);
		}

Большая часть вызовов интерфейса API для доступа к данным аналогична вызовам в типизированной модели. Основное различие заключается в том, что в нетипизированной модели методы вызываются для объекта **MobileServiceJsonTable**, а не **MobileServiceTable**. Использование объекта обратного вызова и метода **onCompleted** очень похоже на типизированную модель.


### <a name="json_instance"></a>Практическое руководство. Создание экземпляра нетипизированной таблицы

После создания экземпляра клиента мобильных служб (здесь это переменная *mClient*) создается экземпляр **MobileServiceJsonTable** с помощью следующего кода.

		MobileServiceJsonTable mTable = mClient.getTable("ToDoItem");

После создания экземпляра **MobileServiceJsonTable** можно вызывать почти все методы, которые доступны в типизированной модели программирования. Однако в некоторых случаях методы принимают нетипизированный параметр, как показано в следующих примерах.

### <a name="json_insert"></a>Практическое руководство. Вставка в нетипизированную таблицу

В следующем коде показано, как вставить данные. Первый шаг - создание объекта[**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), который является частью библиотеки <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

		JsonObject task = new JsonObject();
		task.addProperty("text", "Wake up");
		task.addProperty("complete", false);
		task.addProperty("duration", 5);

Следующий шаг - вставка объекта. Функция обратного вызова, которая передается методу [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) - это экземпляр класса [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532). Обратите внимание на то, что первый параметр метода  *onCompleted* - это JsonObject.
		 
		mTable.insert(task, new TableJsonOperationCallback() {
		    public void onCompleted(JsonObject jsonObject, 
									Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object inserted with ID " + 
		        jsonObject.getAsJsonPrimitive("id").getAsString());
		        }
		    }
		});


Обратите внимание на то, как мы получаем идентификатор вставленного объекта в этом вызове метода:

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Практическое руководство. Удаление из нетипизированной таблицы

В следующем коде показано, как удалить экземпляр, в этом случае тот же экземпляр объекта **JsonObject**, который был создан в предыдущем примере *insert*. Обратите внимание, что объект обратного вызова, **TableDeleteCallback** - это тот же объект, который используется в типизированной модели программирования, а сигнатура метода **onCompleted** отличается от той, что использована в примере **вставки**.


		mTable.delete(task, new TableDeleteCallback() {
		    public void onCompleted(Exception exception, 
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

Вы также можете удалить экземпляр напрямую с помощью идентификатора: 
		
		mTable.delete(task.getAsJsonPrimitive("id").getAsString(), ...)


### <a name="json_get"></a>Практическое руководство. Получение всех строк из нетипизированной таблицы

В следующем коде показано, как получить всю таблицу. Обратите внимание, что нетипизированная модель программирования использует другой объект обратного вызова: [**TableJsonQueryCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298543).

		mTable.execute(new TableJsonQueryCallback() {
		    public void onCompleted(JsonElement result, 
									int count, 
									Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            JsonArray results = result.getAsJsonArray();
		            for(JsonElement item : results){
		                Log.i(TAG, "Read object with ID " + 
		            item.getAsJsonObject().getAsJsonPrimitive("id").getAsInt());
		            }
		        }
		    }
		});

Можно выполнить фильтрацию, сортировку и разбиение по страницам, объединяя методы с такими же именами, как и в типизированной модели программирования.


<h2><a name="binding"></a>Практическое руководство. Привязка данных к пользовательскому интерфейсу</h2>

Привязка данных состоит из трех компонентов:

- источник данных;
- макет экрана;
- адаптер, который связывает два других компонента.

В нашем примере кода мы возвращаем данные из таблицы мобильной службы *ToDoItem* в массив. Это очень распространенный шаблон для приложений, работающих с данными: запросы к базе данных обычно возвращают набор строк, которые клиент получает в списке или массиве. В этом примере массив является источником данных. 

Код указывает макет экрана, который определяет представление данных, отображаемых на устройстве. 

Два компонента соединяются адаптером, который в этом коде является расширением класса *ArrayAdapter&lt;ToDoItem&gt;*.

### <a name="layout"></a>Практическое руководство. Определение макета
 
Макет определяется несколькими фрагментами XML-кода. Учитывая существующий макет, предположим, что следующий код представляет объект **ListView**, который требуется заполнить данными с сервера.

	    <ListView
	        android:id="@+id/listViewToDo"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        tools:listitem="@layout/row_list_to_do" >
	    </ListView>
	

В приведенном выше коде атрибут *listitem* указывает идентификатор макета для отдельной строки в списке. Вот код, который определяет флажок и связанный с ним текст. Экземпляр создается один раз для каждого элемента в списке. В более сложном макете указываются дополнительные поля. Этот код находится в файле  *row_list_to_do.xml*.

		<?xml version="1.0" encoding="utf-8"?>
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:orientation="horizontal">		    
		    <CheckBox
		        android:id="@+id/checkToDoItem"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/checkbox_text" />
		</LinearLayout>
		

### <a name="adapter"></a>Практическое руководство. Определение адаптера
	
Поскольку источник данных нашего представления - это массив объектов *ToDoItem*, мы создадим адаптер как подкласс *ArrayAdapter&lt;ToDoItem&gt;*. Этот подкласс будет создавать представление для каждого объекта *ToDoItem* с помощью макета *row_list_to_do*.

В коде определяется следующий класс, который представляет собой расширение класса *ArrayAdapter&lt;E&gt;*:

		public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {



Необходимо переопределить метод *getView* адаптера. Этот код - один из примеров того, как это сделать: конкретные сведения зависят от приложения.

	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;

		final ToDoItem currentItem = getItem(position);

		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}

		row.setTag(currentItem);

		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);

		return row;
	}

Мы создаем экземпляр этого класса в действии следующим образом:

		ToDoItemAdapter mAdapter;
		mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Обратите внимание, что второй параметр конструктора ToDoItemAdapter - это ссылка на макет. После вызова конструктора идет следующий код, который сначала получает ссылку на **ListView**, а затем вызывает *setAdapter* для настройки использования созданного адаптера:

		ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
		listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>Практическое руководство. Использование адаптера

Теперь вы можете использовать привязку данных. В следующем коде показано, как получить элементы в таблице мобильной службы, очистить адаптер и вызвать метод *add*, чтобы заполнить его полученными элементами.

		mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
			public void onCompleted(List<ToDoItem> result, int count, Exception exception, ServiceFilterResponse response) {
				if (exception == null) {
					mAdapter.clear();
					for (ToDoItem item : result) {
						mAdapter.add(item);
					}
				} 
			}
		});

Адаптер также необходимо вызывать при каждом изменении таблицы *ToDoItem*, если вы хотите показать результаты выполнения. Поскольку изменения выполняются на уровне отдельных записей, обрабатываться будут отдельные строки, а не коллекция. При вставке элемента вызывается метод *add* адаптера, а при удалении - метод *remove*.


<h2><a name="authentication"></a>Практическое руководство. Проверка подлинности пользователей</h2>

Мобильные службы поддерживают аутентификацию и авторизацию пользователей с помощью различных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в разделе [Приступая к работе с проверкой подлинности](http://go.microsoft.com/fwlink/p/?LinkId=296316).

Поддерживаются два потока проверки подлинности: серверный *server* и клиентский *client*. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

Чтобы включить проверку подлинности в приложении, необходимо выполнить три действия.

<ol>
<li>Регистрация приложения для проверки подлинности у поставщика и настройка мобильных служб.</li>
<li>Ограничение разрешений таблицы только пользователями, прошедшими проверку подлинности.</li>
<li>Добавление кода проверки подлинности в приложение</li>
</ol>

Мобильные службы поддерживают следующие существующие поставщики удостоверений, которые можно использовать для проверки подлинности пользователей:

- Учетная запись Майкрософт
- Facebook
- Twitter
- Google 
- Azure Active Directory

Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Также можно использовать идентификатор пользователя, прошедшего проверку, чтобы изменять запросы. 

Первые две задачи выполняются с помощью [портала управления Azure](https://manage.windowsazure.com/). Дополнительные сведения см. в разделе [Приступая к работе с проверкой подлинности](http://go.microsoft.com/fwlink/p/?LinkId=296316).

### <a name="caching"></a>Практическое руководство. Добавление кода проверки подлинности в приложение

1.  Добавьте следующие инструкции import в файл действий приложения.

		import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
		import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2. Добавьте в метод **onCreate** класса действия следующую строку после кода, который создает объект `MobileServiceClient`: мы предполагаем, что ссылка на объект `MobileServiceClient` представляет собой *mClient*.
	
			// Login using the Google provider.
			mClient.login(MobileServiceAuthenticationProvider.Google,
					new UserAuthenticationCallback() {
						@Override
						public void onCompleted(MobileServiceUser user,
								Exception exception, ServiceFilterResponse response) {	
							if (exception == null) {
								/* User now logged in, you can get their identity via user.getUserId() */ 
							} else {
								/* Login error */
							}
						}
					});

    Этот код выполняет проверку подлинности пользователя с помощью имени входа Google. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. Без успешной проверки подлинности продолжение невозможно.

    > [AZURE.NOTE] Если используется поставщик удостоверений, отличный от Google, измените значение, передаваемое в метод **login** выше, на одно из следующих: _MicrosoftAccount_, _Facebook_, _Twitter_ или _WindowsAzureActiveDirectory_.
    </div>


3. При запуске приложения выполните вход с использованием выбранного поставщика удостоверений. 


### <a name="caching"></a>Практическое руководство. Кэширование маркеров проверки подлинности

В этом разделе показано, как сохранить в кэше маркер проверки подлинности. Это необходимо, чтобы пользователям не приходилось опять проходить аутентификацию, если приложение будет в спящем режиме, пока маркер по-прежнему действует.

Для этого необходимо сохранить идентификатор пользователя и маркер аутентификации локально на устройстве. При следующем запуске приложения проверьте кэш - если эти значения существуют, можно пропустить процедуру входа и повторно заполнить клиент этими данными. Однако это конфиденциальные данные и они должны храниться в зашифрованном виде, чтобы обеспечить безопасность в случае кражи телефона. 

В следующем фрагменте кода показано получение маркера для входа с учетной записью Майкрософт. Маркер кэшируется и перезагружается, если кэш найден. 

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
		    // Login using the provider.
		    mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
		            new UserAuthenticationCallback() {
		                @Override
		                public void onCompleted(MobileServiceUser user,
		                        Exception exception, ServiceFilterResponse response) {
		                    if (exception == null) {
		                        createTable();
		                        cacheUser(mClient.getCurrentUser());
		                    } else {
		                        createAndShowDialog("You must log in. Login Required", "Error");
		                    }
		                }
		            });
		}
	}	


	private boolean LoadCache()
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		String tmp1 = prefs.getString("tmp1", "undefined"); 
		if (tmp1 == "undefined")
			return false;
		String tmp2 = prefs.getString("tmp2", "undefined"); 
		if (tmp2 == "undefined")
			return false;
		MobileServiceUser user = new MobileServiceUser(tmp1);
		user.setAuthenticationToken(tmp2);
		mClient.setCurrentUser(user);		
		return true;
	}


	private void cacheUser(MobileServiceUser user)
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		Editor editor = prefs.edit();
		editor.putString("tmp1", user.getUserId());
		editor.putString("tmp2", user.getAuthenticationToken());
		editor.commit();
	}


Что произойдет, если срок действия маркера истечет? В этом случае при попытке использовать маркер для подключения вы получите ответ *401 Не санкционировано*. Пользователю придется войти в систему, чтобы получить новые маркеры. Можно избежать необходимости писать код для обработки таких случаев везде в приложении, которое вызывает мобильные службы, используя фильтры, которые позволяют перехватывать вызовы и ответы от мобильных служб. Код фильтра затем проверит наличие ответа 401, инициирует вход в систему при необходимости и возобновит запрос, вызвавший ответ 401.


<h2><a name="errors"></a>Практическое руководство. Обработка ошибок</h2>

Пример проверки и обработки ошибок см. <a href="https://www.windowsazure.com/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" target="_blank">здесь</a>. В этом примере проверка реализована с помощью серверных скриптов, которые возвращают исключения для ошибок, и клиентского кода, который обрабатывает исключения.

Другим подходом является предоставление обработчика ошибок *global*. Пример кода выше, который обращается к таблице мобильной службы, использует три разных объекта обратного вызова:

- **TableQueryCallback** / **TableQueryJsonCallback**
- **TableOperationCallback** / **TableJsonOperationCallback**
- **TableDeleteCallback** 

Каждый из них содержит метод **OnCompleted**, где второй параметр является объектом **java.lang.Exception**. Можно создать подкласс этих объектов обратного вызова и реализовывать собственный метод **onCompleted**, который проверяет, равно ли значение параметра NULL. Если это так, ошибки нет, и нужно просто вызвать метод <b>super.OnCompleted()</b>.

Если объект **Exception** не равен NULL, выполните общий процесс обработки ошибок, в котором отображаются более подробные сведения об ошибке. В следующем фрагменте кода показан один из способов предоставления дополнительных сведений.

		String msg = exception.getCause().getMessage();



Теперь разработчики могут использовать свои производные обратные вызовы и не беспокоиться о проверке исключений, так как они обрабатывается в централизованном месте (#2) для всех экземпляров обратного вызова.


<h2><a name="customizing"></a>Практическое руководство. Настройка клиента</h2>

### <a name="headers"></a>Практическое руководство. Настройка заголовков запроса

Вам может потребоваться присоединить настраиваемый заголовок к каждому исходящему запросу. Для этого можно настроить ServiceFilter следующим образом:

		client = client.withFilter(new ServiceFilter() {
		
		    @Override
		    public void handleRequest(ServiceFilterRequest request,
					NextServiceFilterCallback nextServiceFilterCallback,
		        	ServiceFilterResponseCallback responseCallback) {
		        request.addHeader("My-Header", "Value");      
		        nextServiceFilterCallback.onNext(request, responseCallback);
		    }
		});


### <a name="serialization"></a>Практическое руководство. Настройка сериализации

По умолчанию мобильные службы предполагают, что имен таблиц, имена столбцов и типы данных на сервере полностью соответствуют аналогам на клиенте. Но имена на сервере и клиенте могут не совпадать по множеству причин. Один из примеров - у вас есть клиент, который нужно изменить так, чтобы он использовал мобильные службы Azure вместо продукта конкурента.

Может потребоваться выполнить следующие виды настройки:
<ul>
<li>
Имена столбцов, используемые в таблице мобильной службы, не совпадают с именами, которые используются в клиенте</li>

<li>Использование таблицы мобильной службы с именем, отличным от имени класса, с которым она сопоставляется в клиенте</li>
<li>Включение автоматического использования прописных букв свойств</li>

<li>Добавление сложных свойств в объект</li>

</ul>

### <a name="columns"></a>Практическое руководство. Сопоставление различных имен клиента и сервера

Предположим, что клиентский код Java использует стандартные имена в стиле Java для свойств объекта *ToDoItem*, как показано ниже. 
<ul>
<li>mId</li>
<li>mText</li>
<li>mComplete</li>
<li>mDuration</li>

</ul>

Имена клиентов необходимо сериализовать в имена JSON, совпадающие с именами столбцов таблицы *ToDoItem* на сервере. Это реализует следующий код, использующий библиотеку <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;
 
	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Практическое руководство. Сопоставление разных имен таблиц между клиентом и мобильными службами

Сопоставить клиентское имя таблицы с именем таблицы различных мобильных служб несложно - мы просто используем одну из перегрузок функции
<a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>, как показано в следующем коде.

		mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>Практическое руководство. Автоматизация сопоставлений имен столбцов

Сопоставление имен столбцов для узкой таблицы всего с несколькими столбцами не вызывает трудностей, как мы видели в предыдущем разделе. Но предположим, что наша таблица содержит много столбцов, скажем 20 или больше. Оказывается, что можно вызвать API <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> и определить стратегию преобразования, которая применяются для каждого столбца, и избежать необходимости указывать имя каждого столбца отдельно.

Для этого мы применяем библиотеку <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>, которую клиентская библиотека Android использует для сериализации объектов Java в данные JSON, передаваемые мобильным службам Azure.

В следующем коде используется метод *setFieldNamingStrategy()*, в котором определяется метод *FieldNamingStrategy()*. Этот метод удаляет начальный символ ("m"), а затем преобразует следующий символ в нижний регистр для каждого поля. Этот код также позволяет преобразовать выходные данные JSON для печати.

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        })
	        .setPrettyPrinting());
	


Этот код должен быть выполнен перед вызовом всех методов клиентского объекта мобильных служб.

### <a name="complex"></a>Практическое руководство. Сохранение свойства объекта или массива в таблице 

На данный момент все наши примеры сериализации использовали простые типы, такие как целые числа и строки, которые легко сериализуются в формат JSON и в таблицу мобильных служб. Предположим, что нам необходимо добавить сложный объект в клиентский тип, который не сериализуется автоматически в формат JSON и таблицу. Например, может потребоваться добавить в клиентский объект массив строк. Теперь мы должны указать способ сериализации и сохранения массива в таблице мобильных служб.

Пример см. в записи блога <a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">Настройка сериализации с помощью библиотеки <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> в клиенте мобильных служб для Android</a>.

Этот общий метод можно использовать всякий раз, когда у нас есть сложный объект, который не может быть автоматически сериализован в формат JSON и таблицу мобильных служб.


## <a name="next-steps"></a>Дальнейшие действия

Ссылка Javadocs для клиентского API Android находится в [http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/package-summary.html](http://go.microsoft.com/fwlink/p/?LinkId=298735 "here")

<!-- Anchors. -->

[Что такое мобильные службы?]: #what-is
[Основные понятия]: #concepts
[Практическое руководство. Создание клиента мобильных служб]: #create-client
[Практическое руководство. Создание ссылки на таблицу]: #instantiating
[Структура API]: #api
[Практическое руководство. Запрос данных из мобильной службы]: #querying
[Фильтрация возвращаемых данных]: #filtering
[Сортировка возвращаемых данных]: #sorting
[Возврат данных на страницах]: #paging
[Выбор определенных столбцов]: #selecting
[Практическое руководство. Сцепка методов запросов]: #chaining
[Практическое руководство. Привязка данных к пользовательскому интерфейсу]: #binding
[Практическое руководство. Определение макета]: #layout
[Практическое руководство. Определение адаптера]: #adapter
[Практическое руководство. Использование адаптера]: #use-adapter
[Практическое руководство. Вставка данных в мобильную службу]: #inserting
[Практическое руководство. Обновление данных в мобильной службе]: #updating
[Практическое руководство. Удаление данных в мобильной службе]: #deleting
[Практическое руководство. Поиск конкретного элемента]: #lookup
[Практическое руководство. Работа с нетипизированными данными]: #untyped
[Практическое руководство. Проверка подлинности пользователей]: #authentication
[Кэширование маркеров проверки подлинности]: #caching
[Практическое руководство. Обработка ошибок]: #errors
[Практическое руководство. Разработка модульных тестов]: #tests
[Практическое руководство. Настройка клиента]: #customizing
[Настройка заголовков запроса]: #headers
[Настройка сериализации]: #serialization
[Дальнейшие действия]: #next-steps
[Настройка и необходимые компоненты]: #setup

<!-- Images. -->


















<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?linkid=280126
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android/
[Управляющие коды ASCII C0 и C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Интерфейс командной строки для управления таблицами мобильных служб]: http://www.windowsazure.com/ru-ru/manage/linux/other-resources/command-line-tools/#Mobile_Tables

<!--HONumber=42-->
