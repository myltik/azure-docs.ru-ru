<properties 
	pageTitle="Работа с клиентской библиотекой Android мобильных служб" 
	description="Узнайте, как использовать клиент Android для мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="ricksal"/>


# Использование клиентской библиотеки Android для мобильных служб

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

В этом руководстве показано, как реализовать типичные сценарии с использованием клиента Android для мобильных служб Azure. В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей, обработка ошибок и настройка клиента.

Впервые осваивая мобильные службы, следует вначале изучить краткое руководство по мобильным службам [Приступая к работе с мобильными службами]. Успешное завершение этого учебника гарантирует, что у вас будет установлена служба Android Studio; она поможет вам настроить учетную запись и создать свою первую мобильную службу, а также установить Пакет SDK для мобильных служб, который поддерживает Android 2.2 или более поздние версии, но мы рекомендуем устанавливать Android 4.2 или более поздние версии.

Справочник Javadocs по API клиентской библиотеки Android находится [здесь](http://go.microsoft.com/fwlink/p/?LinkId=298735).

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Настройка и необходимые компоненты

Мы предполагаем, что вы создали мобильную службу и таблицу. Дополнительные сведения см. в разделе [Создание таблицы](http://go.microsoft.com/fwlink/p/?LinkId=298592). В коде, используемом в данном разделе, мы предполагаем, что имя таблицы — *ToDoItem* и что она содержит следующие столбцы:

- id
- текст
- complete

Соответствующий типизированный клиентский объект выглядит следующим образом:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}
	
Если динамическая схема включена, мобильные службы Azure автоматически создают новые столбцы на основе объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема](http://go.microsoft.com/fwlink/p/?LinkId=296271).

##<a name="create-client"></a>Практическое руководство. Создание клиента мобильных служб
Следующий код создает объект [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html), используемый для доступа к мобильной службе. Этот код размещается в методе `onCreate` класса Activity, указанного в *AndroidManifest.xml* в качестве главного (**MAIN**) действия в категории **LAUNCHER** (запуск).

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileServiceUrl", // Replace with the above Site URL
				"AppKey", 			// replace with the Application Key 
				this)

В приведенном выше коде замените `MobileServiceUrl` и `AppKey` на URL-адрес мобильной службы и ключ приложения соответственно. Оба эти варианты доступны на портале управления Azure: выберите свою мобильную службу и щелкните *Панель управления*.

##<a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу

Самый простой способ запросить или изменить данные в мобильной службе — использование *типизированной модели программирования*, поскольку Java является строго типизированным языком (позже мы расскажем о *нетипизированной* модели). Эта модель обеспечивает простую сериализацию и десериализацию в формате JSON с помощью библиотеки [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) при передаче данных между клиентом и мобильной службой: разработчику не нужно ничего делать, платформа сама занимается обработкой.

Первое, что необходимо сделать для запроса или изменения данных — создать объект [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835), вызвав метод **getTable** объекта [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Мы рассмотрим две перегрузки этого метода:

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

В следующем коде *mClient* представляет собой ссылку на ваш клиент мобильной службы.

[Первая перегрузка](http://go.microsoft.com/fwlink/p/?LinkId=296839) используется, когда имя класса и имя таблицы одинаковы:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[Вторая перегрузка](http://go.microsoft.com/fwlink/p/?LinkId=296840) используется, когда имя таблицы отличается от имени типа.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

## <a name="api"></a>Структура API

Начиная с версии 2.0 клиентской библиотеки, операции с таблицами мобильных служб используют объекты [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) и [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) во всех асинхронных операциях, например в методах, использующих запросы, и таких операциях, как вставка, обновление и удаление. Это облегчает выполнение нескольких операций (в фоновом потоке), так как исключает необходимость обрабатывать несколько вложенных обратных вызовов.


##<a name="querying"></a>Практическое руководство. Запрос данных от мобильной службы

В этом разделе показано, как отправлять запросы к мобильной службе. В подразделах описываются различные аспекты, например сортировка, фильтрация и разбиение по страницам. Наконец, мы обсудим, как можно объединять эти операции.

### <a name="showAll"></a>Практическое руководство. Возврат всех элементов из таблицы

Следующий код возвращает все элементы в таблице*ToDoItem*. Они отображаются в пользовательском интерфейсе в виде добавлений к адаптеру. По функциям этот код похож на код, описанный в учебнике по быстрому запуску [Приступая к работе с мобильными службами].

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {

					final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
               } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
               }
               return result;
            }
		}.execute();


Подобные запросы используют объект [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html).

Переменная *result* возвращает результирующий набор запроса, а код, стоящий после оператора `mToDoTable.execute().get()`, показывает, как анализировать отдельные строки.


### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий код возвращает все элементы из таблицы *ToDoItem*, поле *complete* которой имеет значение *false*. *mToDoTable* — это ссылка на таблицу мобильной службы, которую мы создали ранее.

        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = 
						mToDoTable.where().field("complete").eq(false).execute().get();
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);  
					}
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();



Для фильтрации вызовите метод [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) для ссылки на таблицу. После этого вызовите метод [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869), а затем — метод, который определяет логический предикат. Возможные методы предиката: [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) и т. д.

Этого достаточно для сравнения числовых и строковых полей с указанными значениями. Однако вы можете сделать гораздо больше.

Например, можно фильтровать данные по датам. Можно сравнить все поле даты, но также можно сравнивать части даты, используя такие методы, как [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) и [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472). Следующий частичный код добавляет фильтр для элементов, *дата выполнения* которых равна 2013.

		mToDoTable.where().year("due").eq(2013).execute().get();

Вы можете применять разнообразные сложные фильтры к строковым полям с помощью таких методов, как [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495) и [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496). Следующий частичный код фильтрует строки таблицы, столбец *text* которых начинается с "PRI0".

		mToDoTable.where().startsWith("text", "PRI0").execute().get();

Числовые поля также позволяют применять более широкий спектр сложных фильтров с помощью таких методов, как [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506) и [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507). Следующий частичный код фильтрует строки таблицы, значения *длительности* которых является четным числом.

		mToDoTable.where().field("duration").mod(2).eq(0).execute().get();


Предикаты можно объединить с помощью методов [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) и [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515). Этот частичный код объединяет два приведенных выше примера.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
					.execute().get();

Кроме того, можно группировать логические операторы и вкладывать их друг в друга, как показано в этом частичном коде:

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))
					.execute().get();

Более подробное описание и примеры фильтрации см. в статье [Исследование возможностей модели запросов клиента мобильных служб для Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

Следующий код возвращает все элементы из таблицы *ToDoItem*, упорядоченные по полю *text*. *mToDoTable* — это ссылка на таблицу мобильной службы, которую вы создали ранее.

		mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Первый параметр метода [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) — это строка, которая совпадает с именем поля, по которому выполняется сортировка.

Второй параметр использует перечисление [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521), чтобы указать, следует ли сортировать данные по возрастанию или убыванию.

Обратите внимание, что если используется фильтрация с помощью метода ***where***, метод ***where*** необходимо вызывать до метода ***orderBy***.

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах

Первый пример показывает, как выбрать 5 верхних элементов из таблицы. Этот запрос возвращает элементы из таблицы *ToDoItem*. *mToDoTable* — это ссылка на таблицу мобильной службы, которую вы создали ранее.

       final MobileServiceList<ToDoItem> result = mToDoTable.top(5).execute().get();


Далее мы определим запрос, который пропускает первые 5 элементов, а затем возвращает следующие 5 элементов.

		mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

Следующий код показывает, как получить все элементы из таблицы *ToDoItems*, но отображает только поля *complete* и *text*. *mToDoTable* — это ссылка на таблицу мобильной службы, которую мы создали ранее.

		mToDoTable.select("complete", "text").execute().get();

	
Здесь параметры функции select являются строковыми именами столбцов таблицы, которые требуется вернуть.

Метод [**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) должен следовать за такими методами, как [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) и [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313), если они используются. После него можно вызывать такие методы, как [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731).

### <a name="chaining"></a>Практическое руководство. Объединение методов запросов 

Методы, используемые в таблицах мобильных служб с поддержкой запросов, могут быть объединены. Это позволяет, например, выбирать определенные столбцы или фильтрованные строки, которые сортируются и разбиваются на страницы. Можно создавать весьма сложные логические фильтры.

Это возможно благодаря тому, что методы запросов возвращают объекты [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551), в которых в свою очередь можно вызывать дополнительные методы. Чтобы завершить серию методов и фактически выполнить запрос, вызовите метод [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554).

Ниже приведен пример кода, где *mToDoTable* — это ссылка на таблицу мобильных служб *ToDoItem*.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)				
					.execute().get();

Главное требование при объединении методов состоит в том, что метод *where* и предикаты должны идти первыми. После этого можно вызвать последующие методы в том порядке, который лучше всего соответствует требованиям вашего приложения.


##<a name="inserting"></a>Практическое руководство. Вставка данных в мобильную службу

В следующем коде показано, как вставить новую строку в таблицу.

Сначала необходимо создать экземпляр класса *ToDoItem* и задать его свойства.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		
 Затем введите следующий код:

		// Insert the new item
	    new AsyncTask<Void, Void, Void>() {
	
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();


Этот код вставляет новый элемент и добавляет его к адаптеру, после чего он отображается в пользовательском интерфейсе.

Мобильные службы поддерживают уникальные настраиваемые строковые значения идентификатора таблицы. Это позволяет приложениям использовать настраиваемые значения, например адреса электронной почты или имена пользователей, для столбца идентификатора таблицы мобильных служб. Например, если требуется идентифицировать каждую запись по адресу электронной почты, можно использовать следующий объект JSON.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

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
+  Печатные символы: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\** (0x005C), **`** (0x0060)
+  Идентификаторы "." и ".."

Также для таблиц можно использовать целочисленные идентификаторы. Для этого необходимо создать таблицу с помощью команды `mobile table create`, используя параметр `--integerId`. Эта команда используется в интерфейсе командной строки (CLI) Azure. Дополнительные сведения об использовании CLI см. в статье [Интерфейс командной строки для управления таблицами мобильных служб].


##<a name="updating"></a>Обновление данных в мобильной службе

В следующем коде показано, как обновить данные в таблице. В этом примере *item* — это ссылка на строку в таблице *ToDoItem*, в которую были внесены изменения. Следующий метод позволяет обновить таблицу и адаптер пользовательского интерфейса.

	private void updateItem(final ToDoItem item) {
	    if (mClient == null) {
	        return;
	    }
	
	    new AsyncTask<Void, Void, Void>() {
	
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
	}

##<a name="deleting"></a>Практическое руководство. Удаление данных в мобильной службе

В следующем коде показано, как удалить данные из таблицы. Удаляет из таблицы ToDoItem существующий элемент, для которого в пользовательском интерфейсе был установлен флажок **Завершено**.

	public void checkItem(final ToDoItem item) {
		if (mClient == null) {
			return;
		}

		// Set the item as completed and update it in the table
		item.setComplete(true);
		
		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(item);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            if (item.isComplete()) {
                                mAdapter.remove(item);
                            }
                            refreshItemsFromTable();
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
	}


В следующем примере кода показан еще один способ для этого. Пример удаляет существующий элемент из таблицы ToDoItem, указывая значение в поле идентификатора строки (предполагается, что он равен 2FA404AB-E458-44CD-BC1B-3BC847EF0902). В настоящем приложении вы каким-либо образом получите идентификатор и передадите его в качестве переменной. Чтобы упростить тестирование, можно перейти на портал мобильных служб Azure для вашей службы, открыть вкладку **Данные** и скопировать идентификатор, который вы хотите протестировать.

    public void deleteItem(View view) {

        final String ID = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(ID);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            refreshItemsFromTable();
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="lookup"></a>Поиск определенного элемента
Иногда вам требуется найти определенный элемент по его *идентификатору* в отличие от запросов, где вы обычно получаете коллекцию элементов, отвечающих некоторым критериям. В следующем коде показано, как это сделать для значения *id* `0380BAFB-BCFF-443C-B7D5-30199F730335`. В настоящем приложении вы каким-либо образом получите идентификатор и передадите его в качестве переменной. Чтобы упростить тестирование, можно перейти на портал мобильных служб Azure для вашей службы, открыть вкладку **Данные** и скопировать идентификатор, который вы хотите протестировать.

    /**
     * Lookup specific item from table and UI
     */
    public void lookup(View view) {

        final String ID = "0380BAFB-BCFF-443C-B7D5-30199F730335";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final ToDoItem result = mToDoTable.lookUp(ID).get();
                    runOnUiThread(new Runnable() {
                        public void run() {
                            mAdapter.clear();
                            mAdapter.add(result);
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="untyped"></a>Практическое руководство. Работа с нетипизированными данными

Нетипизированная модель программирования предоставляет полный контроль над сериализацией JSON. Существует несколько сценариев, в которых вы можете использовать ее, например, если таблица мобильной службы содержит множество столбцов, а вам нужно ссылаться только на небольшое их число. Для использования типизированной модели необходимо определить все столбцы таблицы мобильной службы в классе данных. Но с нетипизированной моделью требуется определить только столбцы, которые вы будете использовать.

Большая часть вызовов интерфейса API для доступа к данным аналогична вызовам в типизированной модели. Основное различие заключается в том, что в нетипизированной модели методы вызываются для объекта **MobileServiceJsonTable**, а не **MobileServiceTable**.


### <a name="json_instance"></a>Практическое руководство. Создание экземпляра нетипизированной таблицы

Аналогично типизированной модели сначала вы получаете ссылку на таблицу, но в данном случае это объект [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733). Чтобы получить ссылку, вызовите метод [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) экземпляра клиента мобильных служб.

Сначала необходимо определить переменную:

    /**
     * Mobile Service Json Table used to access untyped data
     */
    private MobileServiceJsonTable mJsonToDoTable;



После создания экземпляра клиента мобильных служб в методе **onCreate** (здесь это переменная *mClient*) создается экземпляр **MobileServiceJsonTable** с помощью следующего кода.


            // Get the Mobile Service Json Table to use
            mJsonToDoTable = mClient.getTable("ToDoItem");

После создания экземпляра **MobileServiceJsonTable** можно вызывать почти все методы, которые доступны в типизированной модели программирования. Однако в некоторых случаях методы принимают нетипизированный параметр, как показано в следующих примерах.

### <a name="json_insert"></a>Практическое руководство. Вставка в нетипизированную таблицу

В следующем коде показано, как вставить данные. Первый шаг — создание объекта [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), который является частью библиотеки <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

		JsonObject item = new JsonObject();
		item.addProperty("text", "Wake up");
		item.addProperty("complete", false);

Следующий шаг — вставка объекта. Функция обратного вызова, которая передается методу [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) — это экземпляр класса [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532). Обратите внимание на то, что параметр метода *insert* — JsonObject.
		 
        // Insert the new item
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mJsonToDoTable.insert(item).get();
                    refreshItemsFromTable();
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();


Если необходимо получить идентификатор вставленного объекта, используем следующий вызов метода:

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Практическое руководство. Удаление из нетипизированной таблицы

В следующем коде показано, как удалить экземпляр, в этом случае тот же экземпляр объекта **JsonObject**, который был создан в предыдущем примере *вставки*. Обратите внимание, что код будет таким же, как и в случае типизированной таблицы, но метод будет иметь другую подпись, так как он ссылается на **JsonObject**.


         mToDoTable.delete(item);


Вы также можете удалить экземпляр напрямую с помощью идентификатора:
		
		 mToDoTable.delete(ID);



### <a name="json_get"></a>Практическое руководство. Получение всех строк из нетипизированной таблицы

В следующем коде показано, как получить всю таблицу. Поскольку вы используете таблицы Json, можно выборочно вывести лишь некоторые столбцы таблицы.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Можно выполнить фильтрацию, сортировку и разбиение по страницам, объединяя методы с такими же именами, как и в типизированной модели программирования.


##<a name="binding"></a>Привязка данных к пользовательскому интерфейсу

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
	

В приведенном выше коде атрибут *listitem* указывает идентификатор макета для отдельной строки в списке. Вот код, который определяет флажок и связанный с ним текст. Экземпляр создается один раз для каждого элемента в списке. В более сложном макете указываются дополнительные поля. Этот код находится в файле *row_list_to_do.xml*.

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
	
Так как источник данных для представления — это массив объектов *ToDoItem*, мы создадим адаптер как подкласс класса *ArrayAdapter&lt;ToDoItem&gt;*. Этот подкласс будет создавать представление для каждого объекта *ToDoItem* с помощью макета *row_list_to_do*.

В нашем коде мы определим следующий класс, который расширяет класс *ArrayAdapter&lt;E&gt;*:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Необходимо переопределить метод *getView* адаптера. Этот код — один из примеров того, как это сделать: конкретные сведения зависят от приложения.

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

Обратите внимание, что второй параметр конструктора ToDoItemAdapter — это ссылка на макет. После вызова конструктора идет следующий код, который сначала получает ссылку на **ListView**, а затем вызывает *setAdapter* для настройки использования созданного адаптера:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>Практическое руководство. Использование адаптера

Теперь вы можете использовать привязку данных. В следующем коде показано, как получить элементы в таблице мобильной службы, очистить адаптер и вызвать метод *add*, чтобы заполнить его полученными элементами.

    public void showAll(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Адаптер также необходимо вызывать при каждом изменении таблицы *ToDoItem*, если вы хотите показать результаты выполнения. Поскольку изменения выполняются на уровне отдельных записей, обрабатываться будут отдельные строки, а не коллекция. При вставке элемента вызывается метод *add* адаптера, а при удалении — метод *remove*.


##<a name="authentication"></a>Практическое руководство. Проверка подлинности пользователей

Мобильные службы поддерживают аутентификацию и авторизацию пользователей с помощью различных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверной части. Дополнительные сведения см. в разделе [Приступая к работе с аутентификацией](http://go.microsoft.com/fwlink/p/?LinkId=296316).

Поддерживается два потока аутентификации: *серверный* и *клиентский*. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

Чтобы включить проверку подлинности в приложении, необходимо выполнить три действия.

- Регистрация приложения для проверки подлинности у поставщика и настройка мобильных служб.
- Ограничение разрешений таблицы только пользователями, прошедшими проверку подлинности.
- Добавление кода проверки подлинности в приложение


Мобильные службы поддерживают следующие существующие поставщики удостоверений, которые можно использовать для проверки подлинности пользователей:

- Учетная запись Майкрософт
- Facebook
- Twitter
- Google 
- Azure Active Directory

Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Также можно использовать идентификатор пользователя, прошедшего проверку, чтобы изменять запросы.

Первые две задачи выполняются с помощью [портала управления Azure](https://manage.windowsazure.com/). Дополнительные сведения см. в разделе [Приступая к работе с аутентификацией](http://go.microsoft.com/fwlink/p/?LinkId=296316).

### <a name="caching"></a>Практическое руководство. Добавление кода проверки подлинности в приложение

1.  Добавьте следующие инструкции import в файл действий приложения.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Добавьте в метод **onCreate** класса действия следующую строку после кода, который создает объект `MobileServiceClient`: предполагается, что ссылка на объект `MobileServiceClient` представляет собой *mClient*.
	
	    // Login using the Google provider.
	    
		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}   		
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();	
    		}
    	}); 

    Этот код выполняет проверку подлинности пользователя с помощью имени входа Google. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. Без успешной проверки подлинности продолжение невозможно.

    > [AZURE.NOTE]Если вы используете поставщик удостоверений, отличный от Google, измените значение, переданное в методе **login**, выше на одно из следующих: _MicrosoftAccount_, _Facebook_, _Twitter_ или _WindowsAzureActiveDirectory_.


3. При запуске приложения выполните вход с использованием выбранного поставщика удостоверений.


### <a name="caching"></a>Практическое руководство. Кэширование маркеров проверки подлинности

В этом разделе показано, как кэшировать маркер проверки подлинности. Это необходимо, чтобы пользователям не приходилось опять проходить аутентификацию, если приложение будет в спящем режиме, пока маркер по-прежнему действует.

Для этого необходимо сохранить идентификатор пользователя и маркер аутентификации локально на устройстве. При следующем запуске приложения проверьте кэш — если эти значения существуют, можно пропустить процедуру входа и повторно заполнить клиент этими данными. Однако это конфиденциальные данные и они должны храниться в зашифрованном виде, чтобы обеспечить безопасность в случае кражи телефона.

В следующем фрагменте кода показано получение маркера для входа с учетной записью Майкрософт. Маркер кэшируется и перезагружается, если кэш найден.

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
			    // Login using the Google provider.    
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
		
		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}   		
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();	
		    		}
		    	});		}
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


Что произойдет, если срок действия маркера истекает? В этом случае при попытке использовать маркер для подключения вы получите ответ *401 unauthorized* (нет доступа). Пользователю придется войти в систему, чтобы получить новые маркеры. Вы можете не писать код для обработки таких случаев везде в приложении, которое вызывает мобильные службы, используя фильтры, которые позволяют перехватывать вызовы и ответы от мобильных служб. Код фильтра затем проверит наличие ответа 401, инициирует вход в систему при необходимости и возобновит запрос, вызвавший ответ 401.


##<a name="customizing"></a>Практическое руководство. Настройка клиента

Есть несколько способов для настройки клиента мобильных служб по умолчанию.

### <a name="headers"></a>Практическое руководство. Настройка заголовков запроса

Вам может потребоваться присоединить настраиваемый заголовок к каждому исходящему запросу. Для этого можно настроить **ServiceFilter** следующим образом.

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request, 
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Практическое руководство. Настройка сериализации

По умолчанию мобильные службы предполагают, что имен таблиц, имена столбцов и типы данных на сервере полностью соответствуют аналогам на клиенте. Но имена на сервере и клиенте могут не совпадать по множеству причин. Один из примеров: у вас есть клиент, который нужно изменить так, чтобы он использовал мобильные службы Azure вместо продукта конкурента.

Может потребоваться выполнить следующие виды настройки:

- Имена столбцов, используемые в таблице мобильной службы, не совпадают с именами, которые используются в клиенте
- Использование таблицы мобильной службы с именем, отличным от имени класса, с которым она сопоставляется в клиенте
- Включение автоматического использования прописных букв свойств
- Добавление сложных свойств в объект

### <a name="columns"></a>Практическое руководство. Сопоставление различных имен клиента и сервера

Предположим, что клиентский код Java использует стандартные имена в стиле Java для свойств объекта *ToDoItem*, как показано ниже.

- mId
- mText
- mComplete
- mDuration


Имена клиентов необходимо сериализовать в имена JSON, совпадающие с именами столбцов таблицы*ToDoItem* на сервере. Это делает следующий код, использующий библиотеку <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;
 
	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Практическое руководство. Сопоставление разных имен таблиц между клиентом и мобильными службами

Сопоставить клиентское имя таблицы с другим именем таблицы мобильных служб несложно: для этого достаточно использовать одно из переопределений функции <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>, как показано в следующем коде.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>Практическое руководство. Автоматизация сопоставления имен столбцов

Сопоставление имен столбцов для узкой таблицы всего с несколькими столбцами не вызывает трудностей, как мы видели в предыдущем разделе. Но предположим, что наша таблица содержит много столбцов, скажем 20 или больше. Оказывается, можно вызвать API <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> и определить стратегию преобразования, которая применяется для каждого столбца, чтобы не указывать имя каждого столбца отдельно.

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

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Return all Items]: #showAll
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-android-get-started.md
[Управляющие коды ASCII C0 и C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
 

<!---HONumber=July15_HO3-->