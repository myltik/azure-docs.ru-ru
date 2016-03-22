<properties
	pageTitle="Использование клиентской библиотеки Android для мобильных приложений"
	description="Использование клиентского пакета Android SDK для мобильных приложений Azure"
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="ricksal"/>


# Использование клиентской библиотеки Android для мобильных приложений

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

В этом руководстве показано, как использовать клиентский пакет Android SDK для мобильных приложений. Это средство позволяет реализовывать такие распространенные сценарии, как запрос данных (вставка, обновление и удаление), проверка подлинности пользователей, обработка ошибок и настройка клиента. Кроме того, приводятся подробные сведения об общем клиентском коде, используемом в большинстве мобильных приложений.

В этом руководстве описано использование клиентского пакета Android SDK. Дополнительные сведения о серверных пакетах SDK для мобильных приложений см. в разделе [Работа с серверными пакетами SDK для .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) или [Использование серверного пакета SDK для Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Справочная документация

Справочник Javadocs по API клиентской библиотеки Android можно найти [в GitHub](http://azure.github.io/azure-mobile-apps-android-client/).

## Настройка и необходимые компоненты

Пакет SDK для мобильных служб поддерживает Android 2.2 и более поздних версий, но мы рекомендуем использовать Android 4.2 или еще более поздние версии.

Выполните указания в руководстве по [быстрому запуску мобильных приложений](app-service-mobile-android-get-started.md), необходимые для установки Android Studio (вам нужно будет настроить учетную запись и создать серверную часть мобильного приложения). Если вы умеете это делать, вы можете пропустить оставшуюся часть этого раздела.

Если вы решили не изучать руководство по быстрому запуску, но хотите подключить приложение Android к серверной части мобильного приложения, сделайте следующее.

- [Создайте серверную часть мобильного приложения](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend) для использования с приложением Android (если таковой еще нет).
- В Android Studio [обновите файлы сборки Gradle](#gradle-build).
- [Включите разрешение INTERNET.](#enable-internet)

После этого выполните шаги, описанные в разделе "Подробный обзор".

###<a name="gradle-build"></a>Обновление файла сборки Gradle

Измените оба файла **build.gradle**:

1. Добавьте следующий код в файл **build.gradle** уровня *Project* внутри тега *buildscript*:

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. Добавьте следующий код в файл **build.gradle** уровня *Module app* внутри тега *dependencies*:

		compile 'com.microsoft.azure:azure-mobile-android:3.1'

	Сейчас последней версией является версия 3.1. Поддерживаемые версии перечислены [здесь](http://go.microsoft.com/fwlink/p/?LinkID=717034).

###<a name="enable-internet"></a>Включение разрешения INTERNET
Для доступа к Azure вам нужно включить для приложения разрешение INTERNET. Если оно не включено, добавьте следующую строку кода в файл **AndroidManifest.xml**:

	<uses-permission android:name="android.permission.INTERNET" />

## Подробный обзор

В этом разделе рассматриваются некоторые примеры кода в приложении быстрого запуска. Если вы не работали с руководством по быстрому запуску, вам нужно добавить этот код в приложение.

> [AZURE.NOTE] В коде часто используется строка MobileServices. На самом деле код ссылается на пакет SDK для мобильных приложений, это просто временный перенос уже использованных элементов.


###<a name="data-object"></a>Определение клиентских классов данных

Для доступа к данным из таблиц SQL Azure вам нужно определить клиентские классы данных, которые соответствуют таблицам в серверной части мобильного приложения. В примерах этого раздела используется таблица с именем *ToDoItem*, которая содержит следующие столбцы:

- id
- текст
- complete

Соответствующий типизированный клиентский объект выглядит так:

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Код будет находиться в файле с именем **ToDoItem.java**.

Если таблица SQL Azure содержит больше столбцов, в этот класс необходимо добавить соответствующие поля.

Например, если она содержит столбец Integer priority, вы можете добавить это поле вместе с методами задания и считывания:

		private Integer priority;

	    /**
	     * Returns the item priority
	     */
	    public Integer getPriority() {
	        return mPriority;
	    }

	    /**
	     * Sets the item priority
	     *
	     * @param priority
	     *            priority to set
	     */
	    public final void setPriority(Integer priority) {
	        mPriority = priority;
	    }

Сведения о создании дополнительных таблиц в серверной части мобильных приложений см. в разделах [Практическое руководство. Определение контроллера таблиц](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (серверная часть .NET) или [Определение таблицы с помощью динамической схемы](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (серверная часть Node.js). Для серверной части Node.js можно использовать параметр **Простые таблицы** на [портале Azure].

###<a name="create-client"></a>Практическое руководство. Создание контекста клиента

Следующий код создает объект **MobileServiceClient**, используемый для доступа к серверной части мобильного приложения. Этот код размещается в методе `onCreate` класса **Activity**, указанного в файле *AndroidManifest.xml* в качестве действия **MAIN** и категории **LAUNCHER**. В коде быстрого запуска он используется в файле **ToDoActivity.java**.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileAppUrl", // Replace with the above Site URL
				this)

В приведенном выше коде замените `MobileAppUrl` URL-адресом серверной части мобильных приложений. Этот адрес можно найти в колонке серверной части мобильных приложений на [портале Azure](https://portal.azure.com/). Для компиляции кода в этой строке также нужно добавить следующую инструкцию **import**:

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу

Самый простой способ запросить или изменить данные в серверной части — воспользоваться *типизированной моделью программирования*, так как Java является строго типизированным языком (ниже будет рассмотрена *нетипизированная* модель). Эта модель обеспечивает простую сериализацию и десериализацию в формате JSON с помощью библиотеки [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) при передаче данных между объектами клиента и таблицами Azure SQL серверной части. В итоге разработчику не нужно ничего делать, платформа сама занимается обработкой.

Для доступа к таблице сначала создайте объект [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835), вызвав метод **getTable** объекта [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). У этого метода две перегрузки.

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

В следующем коде *mClient* — это ссылка на объект MobileServiceClient.

[Первая перегрузка](http://go.microsoft.com/fwlink/p/?LinkId=296839) используется при быстром запуске, когда имя класса и имя таблицы одинаковы:

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[Вторая перегрузка](http://go.microsoft.com/fwlink/p/?LinkId=296840) используется, когда имя таблицы отличается от имени типа (первый параметр — это имя таблицы):

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Привязка данных к пользовательскому интерфейсу

Привязка данных состоит из трех компонентов:

- источник данных;
- макет экрана;
- адаптер, который связывает два эти компонента.

В нашем примере кода мы возвращаем данные из таблицы *ToDoItem* SQL Azure мобильных приложений в массив. Это очень распространенный сценарий для приложений, работающих с данными: запросы к базе данных обычно возвращают набор строк, которые клиент получает в списке или массиве. В этом примере массив является источником данных.

Код указывает макет экрана, который определяет представление данных, отображаемых на устройстве.

Два компонента соединяются адаптером, который в этом коде является расширением класса *ArrayAdapter&lt;ToDoItem&gt;*.

#### <a name="layout"></a>Практическое руководство. Определение макета

Макет определяется несколькими фрагментами XML-кода. Учитывая существующий макет, предположим, что следующий код представляет объект **ListView**, который требуется заполнить данными с сервера.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


В приведенном выше коде атрибут *listitem* указывает идентификатор макета для отдельной строки в списке. Вот код, который определяет флажок и связанный с ним текст. Экземпляр создается один раз для каждого элемента в списке. В этом макете поле **id** не отображается. В более сложном макете указываются дополнительные поля на экране. Этот код находится в файле **row\_list\_to\_do.xml**.

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


#### <a name="adapter"></a>Практическое руководство. Определение адаптера

Так как источник данных для представления — это массив объектов *ToDoItem*, мы создадим адаптер как подкласс класса *ArrayAdapter&lt;ToDoItem&gt;*. Этот подкласс будет создавать представление для каждого объекта *ToDoItem* с помощью макета *row\_list\_to\_do*.

В нашем коде мы определим следующий класс, который расширяет класс *ArrayAdapter&lt;E&gt;*:

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Необходимо переопределить метод *getView* адаптера. Этот код — один из примеров того, как это сделать: конкретные сведения зависят от приложения.

    @Override
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

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

Мы создаем экземпляр этого класса в действии следующим образом:

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Обратите внимание, что второй параметр конструктора ToDoItemAdapter — это ссылка на макет. После вызова конструктора идет следующий код, который сначала получает ссылку на **ListView**, а затем вызывает *setAdapter* для настройки использования созданного адаптера:

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>Структура API

Операции с таблицами мобильных приложений и пользовательские вызовы API являются асинхронными. Поэтому нужно использовать объекты [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) и [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) во всех асинхронных методах, связанных с обработкой запросов, вставок, обновлений и удалений. Это облегчает выполнение нескольких операций в фоновом потоке — обрабатывать несколько вложенных обратных вызовов не нужно.

Чтобы понять, как эти асинхронные API используются в приложении Android и как данные отображаются в пользовательском интерфейсе, просмотрите файл **ToDoActivity.java** в проекте быстрого запуска Android на [портале Azure].


#### <a name="use-adapter"></a>Практическое руководство. Использование адаптера

Теперь вы можете использовать привязку данных. В следующем коде показано, как получить элементы в таблице мобильной службы, очистить адаптер, а затем вызвать метод *add* для заполнения полученными элементами.


**Подлежит уточнению**: проверить код!

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

Адаптер также необходимо вызывать при каждом изменении таблицы *ToDoItem*, если вы хотите показать результаты выполнения. Поскольку изменения выполняются на уровне отдельных записей, обрабатываться будут отдельные строки, а не коллекция. При вставке элемента вызывается метод *add* адаптера, а при удалении — метод *remove*.

##<a name="querying"></a>Практическое руководство. Запрос данных из серверной части мобильного приложения

В этом разделе показано, как отправлять запросы к серверной части мобильных приложений. Этот процесс включает следующие задачи.

- [Возврат всех элементов]
- [Фильтрация возвращаемых данных]
- [Сортировка возвращаемых данных]
- [Возврат данных на страницах]
- [Выбор определенных столбцов]
- [Сцепка методов запросов](#chaining)

### <a name="showAll"></a>Практическое руководство. Возврат всех элементов из таблицы

Следующий запрос возвращает все элементы в таблице *ToDoItem*.

	List<ToDoItem> results = mToDoTable.execute().get();

Переменная *results* возвращает итоговый набор запроса в виде списка.

### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий запрос при выполнении возвращает все элементы из таблицы *ToDoItem*, где для переменной *complete* задано значение *false*. Этот код приведен в руководстве по быстрому запуску.

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable* — это ссылка на таблицу мобильной службы, созданную ранее.

Для определения фильтра вызовите метод **where** для ссылки на таблицу. После этого вызовите метод **field**, а затем — метод, который определяет логический предикат. Возможные методы предиката: **eq** (равно), **ne** (не равно), **gt** (больше), **ge** (больше или равно), **lt** (меньше), **le** (меньше или равно) и т. д. Эти методы позволяют сравнивать числовые и строковые поля с указанными значениями.

Фильтровать данные можно по датам. Следующие методы позволяют сравнить поле даты целиком или частично: **year**, **month**, **day**, **hour**, **minute** и **second**. В следующем примере добавляется фильтр для элементов, *дата выполнения* которых равна 2013.

	mToDoTable.where().year("due").eq(2013).execute().get();

Следующие методы поддерживают применение сложных фильтров к строковым полям: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** и **length**. В следующем примере отфильтровываются строки таблицы, в которых столбец *text* начинается с PRI0.

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

Следующие методы оператора поддерживают использование числовых полей: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** и **round**. В следующем примере отфильтровываются строки таблицы, в которых значение *duration* является четным числом.

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Предикаты можно объединять с помощью логических методов **and**, **or** и **not**. В следующем примере объединяются два приведенных выше примера.

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

Кроме того, логические операторы можно группировать и вкладывать друг в друга:

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

Более подробное описание и примеры фильтрации см. в статье [Исследование возможностей модели запросов клиента мобильных служб Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

Следующий код возвращает все элементы из таблицы *ToDoItem*, упорядоченные по полю *text*. *mToDoTable* — это ссылка на таблицу серверной части, созданную ранее:

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Первый параметр метода **orderBy** — это строка, совпадающая с именем поля, по которому выполняется сортировка.

Второй параметр использует перечисление **QueryOrder**, чтобы определить порядок сортировки (по возрастанию или убыванию).

Обратите внимание, что если используется фильтрация с помощью метода ***where***, метод ***where*** необходимо вызывать до метода ***orderBy***.

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах

Первый пример показывает, как выбрать 5 верхних элементов из таблицы. Следующий запрос возвращает элементы из таблицы *ToDoItem*. *mToDoTable* — это ссылка на таблицу серверной части, созданную ранее:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


А этот запрос пропускает первые 5 элементов, возвращая следующие 5 элементов:

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

В следующем фрагменте кода показано, как получить все элементы из таблицы *ToDoItems*, в которой отображаются только поля *complete* и *text*. *mToDoTable* — это ссылка на таблицу серверной части, созданную ранее.

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


Здесь параметры функции select являются строковыми именами столбцов таблицы, которые требуется вернуть.

Метод **select** должен следовать за такими методами, как **where** и **orderBy**, если они используются. Затем можно вызывать методы разбивки на страницы, такие как **top**.

### <a name="chaining"></a>Практическое руководство. Объединение методов запросов

Очевидно, что методы, используемые в таблицах серверной части с поддержкой запросов, можно объединять. Это позволяет, например, выбирать определенные столбцы или фильтрованные строки, которые сортируются и разбиваются на страницы. Можно создавать весьма сложные логические фильтры.

Это возможно благодаря тому, что методы запросов возвращают объекты **MobileServiceQuery&lt;T&gt;**, в которых в свою очередь можно вызывать дополнительные методы. Чтобы завершить серию методов и фактически выполнить запрос, вызовите метод **execute**.

Ниже приведен пример кода, где *mToDoTable* — это ссылка на таблицу *ToDoItem*.

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

Главное требование при объединении методов состоит в том, что метод *where* и предикаты должны идти первыми. После этого можно вызвать последующие методы в том порядке, который лучше всего соответствует требованиям вашего приложения.


##<a name="inserting"></a>Практическое руководство. Вставка данных в серверную часть

В следующем коде показано, как вставить новую строку в таблицу.

Сначала необходимо создать экземпляр класса *ToDoItem* и задать его свойства.

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

Затем введите следующий код:

	ToDoItem entity = mToDoTable.insert(item).get();

Возвращаемая сущность соответствует данным, вставленным в таблицу серверной части, включая идентификатор и другие значения, заданные в серверной части.

Каждая таблица мобильных приложений должна содержать столбец с именем **id**, который используется для индексирования таблицы. По умолчанию этот столбец является строковым типом данных, который необходим для поддержки автономной синхронизации. Значение по умолчанию для столбца идентификатора — идентификатор GUID, но вы можете указать другие уникальные значения, например адреса электронной почты или имена пользователей. Если строковое значение идентификатора для вставленной записи не предусмотрено, серверная часть создает новое значение GUID.

Использование строковых значений идентификаторов связано с такими преимуществами.

+ Идентификаторы можно создавать без обмена данными с базой данных.
+ Можно легко объединять записи из разных таблиц или баз данных.
+ Значения идентификаторов удобнее интегрировать с логикой приложения.

##<a name="updating"></a>Практическое руководство. Обновление данных в мобильном приложении

В следующем коде показано, как обновить данные в таблице.

    mToDoTable.update(item).get();

В этом примере *item* — это ссылка на строку в таблице *ToDoItem*, в которую были внесены изменения.

##<a name="deleting"></a>Практическое руководство. Удаление данных в мобильном приложении

В следующем коде показано, как удалить данные из таблицы, указав объект данных.

	mToDoTable.delete(item);

Удалить элемент также можно, указав поле **id** удаляемой строки.

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);


##<a name="lookup"></a>Поиск определенного элемента

В следующем коде показано, как найти элемент с определенным значением *id*.

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>Практическое руководство. Работа с нетипизированными данными

Нетипизированная модель программирования предоставляет полный контроль над сериализацией JSON. Эту модель можно использовать в рамках нескольких сценариев, например, когда таблица серверной части содержит множество столбцов, а вам нужно ссылаться только на небольшое их количество. Для использования типизированной модели необходимо определить все столбцы таблицы мобильного приложения в классе данных. Но с нетипизированной моделью требуется определить только столбцы, которые вы будете использовать.

Большая часть вызовов интерфейса API для доступа к данным аналогична вызовам в типизированной модели. Основное различие заключается в том, что в нетипизированной модели методы вызываются для объекта **MobileServiceJsonTable**, а не **MobileServiceTable**.


### <a name="json_instance"></a>Практическое руководство. Создание экземпляра нетипизированной таблицы

Аналогично типизированной модели сначала вы получаете ссылку на таблицу, но в данном случае это объект **MobileServicesJsonTable**. Чтобы получить ссылку, вызовите метод **getTable** экземпляра клиента:

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

После создания экземпляра **MobileServiceJsonTable** можно вызывать почти все методы, которые доступны в типизированной модели программирования. Однако в некоторых случаях методы принимают нетипизированный параметр, как показано в следующих примерах.

### <a name="json_insert"></a>Практическое руководство. Вставка в нетипизированную таблицу

В следующем коде показано, как вставить данные. Первый шаг — создание объекта[**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), который является частью библиотеки [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801).

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

Следующий шаг — вставка объекта.

    mJsonToDoTable.insert(jsonItem).get();


Если необходимо получить идентификатор вставленного объекта, используем следующий вызов метода:

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Практическое руководство. Удаление из нетипизированной таблицы

В следующем коде показано, как удалить экземпляр, в этом случае тот же экземпляр объекта **JsonObject**, который был создан в предыдущем примере *вставки*. Обратите внимание, что код будет таким же, как и в случае типизированной таблицы, но метод будет иметь другую подпись, так как он ссылается на **JsonObject**.


         mToDoTable.delete(jsonItem);


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



##<a name="custom-api"></a>Практическое руководство. Вызов настраиваемого интерфейса API

Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Вызовите из клиента Android метод **invokeApi** для вызова конечной точки настраиваемого API. В следующем примере показано, как вызвать конечную точку API с именем *completeAll*, которая возвращает класс коллекции с именем MarkAllResult.

	public void completeItem(View view) {

	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}

	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();
	    		}
	    	});
	    }

Метод **invokeApi** вызывается на стороне клиента, что приводит к отправке запроса POST новому настраиваемому API. Результат, возвращаемый настраиваемым интерфейсом API, отображается в диалоговом окне сообщения, как и любые ошибки. Другие версии метода **invokeApi** позволяют при необходимости отправить объект в тексте запроса, указать метод HTTP и отправить параметры запроса вместе с запросом. Кроме того, доступны нетипизированные версии **invokeApi**.

##<a name="authentication"></a>Практическое руководство. Добавление проверки подлинности в приложение

Процесс добавления этих компонентов подробно описан в руководствах.

Служба приложений поддерживает [проверку подлинности пользователей приложения](app-service-mobile-android-get-started-users.md) с помощью разных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверной части.

Поддерживается два потока аутентификации: *серверный* и *клиентский*. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с различными возможностями устройства (включая единый вход), так как он использует пакеты SDK конкретного поставщика для конкретного устройства, что нужно зафиксировать в коде.

Чтобы включить проверку подлинности в приложении, необходимо выполнить три действия.

- Регистрация приложения для проверки подлинности у поставщика и настройка серверной части мобильного приложения.
- Ограничение разрешений таблицы только пользователями, прошедшими проверку подлинности.
- Добавление кода проверки подлинности в приложение.

Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Также можно использовать идентификатор безопасности пользователя, прошедшего проверку, чтобы изменять запросы.

Первые две задачи выполняются с помощью [портала Azure](https://portal.azure.com/). Дополнительные сведения см. в разделе [Приступая к работе с аутентификацией].

### <a name="caching"></a>Практическое руководство. Добавление кода проверки подлинности в приложение

Следующий код запускает процесс входа сервера потока с помощью поставщика Google:

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Идентификатор вошедшего в систему пользователя можно получить из **MobileServiceUser** с помощью метода **getUserId**. Пример использования Futures для вызова API асинхронного входа см. в статье [Добавление проверки подлинности в приложение Android].


### <a name="caching"></a>Практическое руководство. Кэширование маркеров проверки подлинности

Для этого необходимо сохранить идентификатор пользователя и маркер аутентификации локально на устройстве. При следующем запуске приложения проверьте кэш — если эти значения существуют, можно пропустить процедуру входа и повторно заполнить клиент этими данными. Однако это конфиденциальные данные и они должны храниться в зашифрованном виде, чтобы обеспечить безопасность в случае кражи телефона.

Полный пример кэширования маркеров проверки подлинности см. в разделе [Кэширование маркеров проверки подлинности на клиенте](app-service-mobile-android-get-started-users.md#cache-tokens).

При попытке использовать маркер с истекшим сроком действия вы получите ответ *401 не санкционировано*. Пользователю придется войти в систему, чтобы получить новые маркеры. Вы можете не писать код для обработки таких случаев везде в приложении, которое вызывает мобильные службы, используя фильтры, которые позволяют перехватывать вызовы и ответы от мобильных служб. Код фильтра затем проверит наличие ответа 401, инициирует вход в систему при необходимости и возобновит запрос, вызвавший ответ 401. Вы также можете проверить маркер на предмет истечения срока действия.


## <a name="adal"></a>Практическое руководство. Проверка подлинности пользователей с помощью библиотеки проверки подлинности Active Directory

Библиотеку проверки подлинности Active Directory (ADAL) можно использовать для входа пользователей в приложение с помощью Azure Active Directory. Этот подход является более предпочтительным, чем использование методов `loginAsync()`, так как он обеспечивает более удобный интерфейс входа для пользователя и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью AAD, следуя указаниям в руководстве [Настройка приложения службы приложений для использования службы входа Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Обязательно выполните дополнительный этап регистрации собственного клиентского приложения.

2. Установите ADAL, включив в файл build.gradle следующий текст:

	repositories { mavenCentral() flatDir { dirs 'libs' } maven { url "YourLocalMavenRepoPath\\.m2\\repository" } } packagingOptions { exclude 'META-INF/MSFTSIG.RSA' exclude 'META-INF/MSFTSIG.SF' } dependencies { compile fileTree(dir: 'libs', include: ['*.jar']) compile('com.microsoft.aad:adal:1.1.1') { exclude group: 'com.android.support' } // Recent version is 1.1.1 compile 'com.android.support:support-v4:23.0.0' }

3. Включите указанный ниже код в приложение, выполнив следующие замены.

* Замените текст **INSERT-AUTHORITY-HERE** именем клиента, в котором подготовлено приложение. Используйте следующий формат: https://login.windows.net/contoso.onmicrosoft.com. Это значение можно скопировать на вкладке "Домен" в Azure Active Directory на [классическом портале Azure].

* Замените текст **INSERT-RESOURCE-ID-HERE** идентификатором клиента для серверной части мобильного приложения. Это значение можно скопировать на портале в разделе **Настройки Azure Active Directory** на вкладке **Дополнительно**.

* Замените текст **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

* Замените текст **INSERT-REDIRECT-URI-HERE** конечной точкой сайта _/.auth/login/done_, используя схему HTTPS. Это значение должно быть аналогично _https://contoso.azurewebsites.net/.auth/login/done_.

		private AuthenticationContext mContext;
		private void authenticate() {
		String authority = "INSERT-AUTHORITY-HERE";
		String resourceId = "INSERT-RESOURCE-ID-HERE";
		String clientId = "INSERT-CLIENT-ID-HERE";
		String redirectUri = "INSERT-REDIRECT-URI-HERE";
		try {
		    mContext = new AuthenticationContext(this, authority, true);
		    mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
		} catch (Exception exc) {
		    exc.printStackTrace();
		}
		}
		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
		@Override
		public void onError(Exception exc) {
		    if (exc instanceof AuthenticationException) {
		        Log.d(TAG, "Cancelled");
		    } else {
		        Log.d(TAG, "Authentication error:" + exc.getMessage());
		    }
		}
		@Override
			public void onSuccess(AuthenticationResult result) {
		    if (result == null || result.getAccessToken() == null
		            || result.getAccessToken().isEmpty()) {
		        Log.d(TAG, "Token is empty");
		    } else {
		        try {
		            JSONObject payload = new JSONObject();
		            payload.put("access_token", result.getAccessToken());
		            ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
		            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		                @Override
		                public void onFailure(Throwable exc) {
		                    exc.printStackTrace();
		                }
		                @Override
		                public void onSuccess(MobileServiceUser user) {
		            		Log.d(TAG, "Login Complete");
		                }
		            });
		        }
		        catch (Exception exc){
		            Log.d(TAG, "Authentication error:" + exc.getMessage());
		        }
		    }
		}
		};
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (mContext != null) {
		    mContext.onActivityResult(requestCode, resultCode, data);
		}
		}


## Практическое руководство. Добавление push-уведомлений в приложение

Подробные сведения см. в [этом обзоре](notification-hubs-overview.md/#integration-with-app-service-mobile-apps), где описывается, как центры уведомлений Microsoft Azure поддерживают разные виды push-уведомлений.

В [этом руководстве](app-service-mobile-android-get-started-push.md) описывается, как настроить отправку push-уведомлений при каждой вставке записи.

## Практическое руководство. Добавление автономной синхронизации в приложение
Это руководство по быстрому запуску содержит код, который реализует автономную синхронизацию. Найдите код, начинающийся со следующего комментария:

	// Offline Sync

Раскомментировав строки кода, вы реализуете автономную синхронизацию. Подобный код можно добавлять в код других мобильных приложений.

##<a name="customizing"></a>Практическое руководство. Настройка клиента

Есть несколько способов настройки стандартного поведения клиента.

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

Клиент предполагает, что все имена таблиц, имена столбцов и типы данных в серверной части точно соответствуют объектам данных, определенных в клиенте. Но имена на сервере и клиенте могут не совпадать по множеству причин. Вам может потребоваться выполнить следующие виды настройки.

- Имена столбцов, используемые в таблице мобильной службы, не совпадают с именами, которые используются в клиенте.
- Использование таблицы мобильной службы с именем, отличающимся от имени класса, с которым она сопоставляется в клиенте.
- Включение автоматического использования прописных букв для свойств.
- Добавление сложных свойств в объект.

### <a name="columns"></a>Практическое руководство. Сопоставление различных имен клиента и сервера

Предположим, что клиентский код Java использует стандартные имена в стиле Java для свойств объекта *ToDoItem*, как показано ниже.

- mId
- mText
- mComplete
- mDuration


Имена клиентов необходимо сериализовать в имена JSON, совпадающие с именами столбцов таблицы*ToDoItem* на сервере. Это реализует следующий код, использующий библиотеку [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801).

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Практическое руководство. Сопоставление разных имен таблиц между клиентом и серверной частью

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
[Возврат всех элементов]: #showAll
[Фильтрация возвращаемых данных]: #filtering
[Сортировка возвращаемых данных]: #sorting
[Возврат данных на страницах]: #paging
[Выбор определенных столбцов]: #selecting
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
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[портале Azure]: https://portal.azure.com
[Добавление проверки подлинности в приложение Android]: app-service-mobile-android-get-started-users.md
[Приступая к работе с аутентификацией]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0309_2016-->