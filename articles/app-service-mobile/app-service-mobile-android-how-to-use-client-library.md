<properties
    pageTitle="Использование клиентской библиотеки Android для мобильных приложений"
    description="Использование клиентского пакета Android SDK для мобильных приложений Azure"
    services="app-service\mobile"
    documentationCenter="android"
    authors="yuaxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>



# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Использование клиентской библиотеки Android для мобильных приложений

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

В этом руководстве показано, как использовать пакет SDK для мобильных приложений для клиентов Android, чтобы реализовать распространенные сценарии, в том числе:

- запрос данных (вставка, обновление и удаления);
- аутентификация;
- обработка ошибок;
- настройка клиента. 

Кроме того, приводятся подробные сведения об общем клиентском коде, используемом в большинстве мобильных приложений.

В этом руководстве описано использование клиентского пакета Android SDK.  Дополнительные сведения о серверных пакетах SDK для мобильных приложений см. в разделе [Работа с серверными пакетами SDK для .NET][10] или [Использование серверного пакета SDK для Node.js][11].

## <a name="reference-documentation"></a>Справочная документация

Справочник [Javadocs по API][12] клиентской библиотеки Android можно найти на сайте GitHub.

## <a name="supported-platforms"></a>Поддерживаемые платформы

Пакет SDK Azure для мобильных приложений Android поддерживает API уровней 19–24 (от KitKat до Nougat).  

"Серверная" аутентификация использует WebView для представляемого пользовательского интерфейса. Если устройство не может представить пользовательский интерфейс WebView, требуется применять другие способы аутентификации, которые выходят за рамки данного продукта.  Этот пакет SDK не подходит для различного рода часов и других устройств с аналогичными ограничениями.

## <a name="setup-and-prerequisites"></a>Настройка и необходимые компоненты

Ознакомьтесь с [кратким учебником по мобильным приложениям](app-service-mobile-android-get-started.md) .  Выполнение данной задачи гарантирует, что будут соблюдены все предварительные требования для разработки мобильных приложений Azure.  Кроме того, этот краткий учебник поможет вам настроить учетную запись и создать свою первую серверную часть мобильного приложения.

Если вы решите не изучать краткий учебник, то выполните следующие задачи.

- [Создайте серверную часть мобильного приложения][13] для использования с приложением Android.
- В Android Studio [обновите файлы сборки Gradle](#gradle-build).
- [Включение разрешение INTERNET](#enable-internet).

###<a name="<a-name="gradle-build"></a>update-the-gradle-build-file"></a><a name="gradle-build"></a>Обновление файла сборки Gradle

Измените оба файла **build.gradle** :

1. Добавьте следующий код в файл *build.gradle* уровня **Project** внутри тега *buildscript*.

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Добавьте следующий код в файл *build.gradle* уровня **Module app** внутри тега *dependencies*.

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    Сейчас последней версией является версия 3.1.0. Поддерживаемые версии перечислены [здесь][14].

###<a name="<a-name="enable-internet"></a>enable-internet-permission"></a><a name="enable-internet"></a>Включение разрешения INTERNET

Для доступа к Azure вам нужно включить для приложения разрешение INTERNET. Если оно не включено, добавьте следующую строку кода в файл **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>Подробный обзор

В этом разделе рассматриваются некоторые фрагменты кода из приложения быстрого запуска, которые относятся к использованию мобильных приложений Azure.  

###<a name="<a-name="data-object"></a>define-client-data-classes"></a><a name="data-object"></a>Определение клиентских классов данных

Для доступа к данным из таблиц SQL Azure вам нужно определить клиентские классы данных, которые соответствуют таблицам в серверной части мобильного приложения. В примерах этого раздела используется таблица с именем **ToDoItem**, которая содержит следующие столбцы:

- id
- текст
- complete

Ниже приведен соответствующий типизированный клиентский объект.

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

Код находится в файле **ToDoItem.java**.

Если таблица SQL Azure содержит больше столбцов, в этот класс необходимо добавить соответствующие поля.  Например, если объект переноса данных (DTO) содержал целочисленный столбец Priority, то вы можете добавить это поле вместе с методами получения и задания.

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

Сведения о создании дополнительных таблиц в серверной части мобильных приложений см. в разделах [Практическое руководство. Определение контроллера таблиц][15] (серверная часть .NET) или [Определение таблицы с помощью динамической схемы][16] (серверная часть Node.js). Для серверной части Node.js можно использовать параметр **Простые таблицы** на [портале Azure].

###<a name="<a-name="create-client"></a>how-to:-create-the-client-context"></a><a name="create-client"></a>Практическое руководство. Создание контекста клиента

Следующий код создает объект **MobileServiceClient** , используемый для доступа к серверной части мобильного приложения. Этот код размещается в методе `onCreate` класса **Activity**, указанного в файле *AndroidManifest.xml* в качестве действия **MAIN** и категории **LAUNCHER**. В коде быстрого запуска он используется в файле **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

В приведенном выше коде замените `MobileAppUrl` URL-адресом серверной части мобильного приложения. Этот адрес можно найти в колонке серверной части мобильного приложения на [портале Azure]. Для компиляции этой строки кода также нужно добавить следующую инструкцию **import**.

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="<a-name="instantiating"></a>how-to:-create-a-table-reference"></a><a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу

Самый простой способ запросить или изменить данные в серверной части — воспользоваться *типизированной моделью программирования*, так как Java является строго типизированным языком. Эта модель обеспечивает прозрачную сериализацию и десериализацию в формате JSON с помощью библиотеки [gson][3] при передаче данных между объектами клиента и таблицами Azure SQL серверной части.

Для доступа к таблице сначала создайте объект [MobileServiceTable][8], вызвав метод **getTable** объекта [MobileServiceClient][9].  У этого метода две перегрузки.

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

В следующем коде **mClient** — это ссылка на объект MobileServiceClient.  Первая перегрузка используется при быстром запуске, когда имя класса и имя таблицы одинаковы.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

Вторая перегрузка используется, когда имя таблицы отличается от имени типа (первый параметр — это имя таблицы).

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="<a-name="binding"></a>how-to:-bind-data-to-the-user-interface"></a><a name="binding"></a>Привязка данных к пользовательскому интерфейсу

Привязка данных состоит из трех компонентов:

- источник данных;
- макет экрана;
- адаптер, который связывает два эти компонента.

В нашем примере кода мы возвращаем данные из таблицы **ToDoItem** SQL Azure мобильных приложений в массив. Это действие типично для приложений для обработки данных.  Запросы к базе данных часто возвращают набор строк, которые клиент получает в виде списка или массива. В этом примере массив является источником данных.

Код указывает макет экрана, который определяет представление данных, отображаемых на устройстве.  Два компонента соединяются адаптером, который в этом коде является расширением класса **ArrayAdapter&lt;ToDoItem&gt;**.

#### <a name="<a-name="layout"></a>how-to:-define-the-layout"></a><a name="layout"></a>Практическое руководство. Определение макета

Макет определяется несколькими фрагментами XML-кода. С учетом существующего макета приведенный ниже код представляет объект **ListView** , который требуется заполнить данными с сервера.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

В приведенном выше коде атрибут *listitem* указывает идентификатор макета для отдельной строки в списке. Этот код задает флажок и связанный с ним текст, при этом экземпляры создаются для каждого элемента в списке. В этом макете поле **id** не отображается. В более сложном макете указываются дополнительные поля на экране. Этот код находится в файле **row_list_to_do.xml**.

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


#### <a name="<a-name="adapter"></a>how-to:-define-the-adapter"></a><a name="adapter"></a>Практическое руководство. Определение адаптера

Источник данных нашего представления — это массив объектов **ToDoItem**, поэтому мы создадим адаптер как подкласс **ArrayAdapter&lt;ToDoItem&gt;**. Этот подкласс создаст представление для каждого объекта **ToDoItem** с помощью макета **row_list_to_do**.

В коде определяется следующий класс, который представляет собой расширение класса **ArrayAdapter&lt;E&gt;**.

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Переопределите метод **getView** адаптера. Например:

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

Второй параметр конструктора ToDoItemAdapter — это ссылка на макет. Теперь можно создать экземпляр **ListView** и назначить адаптер в **ListView**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="<a-name="api"></a>the-api-structure"></a><a name="api"></a>Структура API

Табличные операции с мобильными приложениями и вызовы пользовательских API выполняются асинхронно. Используйте объекты [Future] и [AsyncTask] для асинхронных методов, включающих в себя запросы, а также операции вставки, обновления и удаления. Использование объектов Future облегчает выполнение нескольких операций в фоновом потоке — обрабатывать несколько вложенных обратных вызовов не нужно.

С примером можно ознакомиться в файле **ToDoActivity.java** проекта быстрого запуска для Android, доступного на [портале Azure] .

#### <a name="<a-name="use-adapter"></a>how-to:-use-the-adapter"></a><a name="use-adapter"></a>Практическое руководство. Использование адаптера

Теперь вы можете использовать привязку данных. Ниже приведен код, в котором показано, как получить элементы из таблицы и заполнить локальный адаптер возвращенными элементами.

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

Адаптер вызывается при каждом изменении таблицы **ToDoItem** . Так как изменения выполняются на уровне отдельных записей, обрабатываться будут отдельные строки, а не коллекция. При вставке элемента вызывается метод **add** адаптера, а при удалении — метод **remove**.

##<a name="<a-name="querying"></a>how-to:-query-data-from-your-mobile-app-backend"></a><a name="querying"></a>Практическое руководство. Запрос данных из серверной части мобильного приложения

В этом разделе показано, как отправлять запросы к серверной части мобильных приложений. Этот процесс включает следующие задачи.

- [Возврат всех элементов]
- [Фильтрация возвращаемых данных]
- [Сортировка возвращаемых данных]
- [Возврат данных на страницах]
- [Выбор определенных столбцов]
- [Сцепка методов запросов](#chaining)

### <a name="<a-name="showall"></a>how-to:-return-all-items-from-a-table"></a><a name="showAll"></a>Практическое руководство. Возврат всех элементов из таблицы

Следующий запрос возвращает все элементы в таблице **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

Переменная *results* возвращает итоговый набор запроса в виде списка.

### <a name="<a-name="filtering"></a>how-to:-filter-returned-data"></a><a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий запрос при выполнении возвращает все элементы из таблицы **ToDoItem**, где для переменной **complete** задано значение **false**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** — это ссылка на таблицу мобильной службы, созданную ранее.

Для определения фильтра вызовите метод **where** для ссылки на таблицу. После метода **where** вызывается метод **field**, а затем — метод, который определяет логический предикат. Возможные методы предиката: **eq** (равно), **ne** (не равно), **gt** (больше), **ge** (больше или равно), **lt** (меньше), **le** (меньше или равно). Эти методы позволяют сравнивать числовые и строковые поля с указанными значениями.

Фильтровать данные можно по датам. Следующие методы позволяют сравнить поле даты целиком или частично: **year**, **month**, **day**, **hour**, **minute** и **second**. В следующем примере добавляется фильтр для элементов, *дата выполнения* которых равна 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

Следующие методы поддерживают применение сложных фильтров к строковым полям: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** и **length**. В следующем примере отфильтровываются строки таблицы, в которых столбец *text* начинается с PRI0.

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

Следующие методы оператора поддерживают использование числовых полей: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** и **round**. В следующем примере отфильтровываются строки таблицы, в которых значение **duration** является четным числом.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Предикаты можно объединять с помощью логических методов **and**, **or** и **not**. В следующем примере объединяются два приведенных выше примера.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Группирование и вложение логических операторов.

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Более подробное описание и примеры фильтрации см. в статье [Exploring the richness of the Mobile Services Android client query model](http://hashtagfail.com/post/46493261719/mobile-services-android-querying) (Исследование возможностей модели запросов клиента мобильных служб Android).

### <a name="<a-name="sorting"></a>how-to:-sort-returned-data"></a><a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

Следующий код возвращает все элементы из таблицы **ToDoItem** , упорядоченные по полю *text* . *mToDoTable* — это ссылка на таблицу серверной части, созданную ранее:

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Первый параметр метода **orderBy** — это строка, совпадающая с именем поля, по которому выполняется сортировка. Второй параметр использует перечисление **QueryOrder** , чтобы определить порядок сортировки (по возрастанию или убыванию).  Если используется фильтрация с помощью метода ***where***, то метод ***where*** необходимо вызывать до метода ***orderBy***.

### <a name="<a-name="paging"></a>how-to:-return-data-in-pages"></a><a name="paging"></a>Практическое руководство. Возврат данных на страницах

Первый пример показывает, как выбрать пять верхних элементов из таблицы. Следующий запрос возвращает элементы из таблицы **ToDoItem**. **mToDoTable** — это ссылка на таблицу серверной части, созданную ранее:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Приведенный ниже запрос пропускает первые пять элементов, возвращая следующие пять элементов.

    mToDoTable.skip(5).top(5).execute().get();

### <a name="<a-name="selecting"></a>how-to:-select-specific-columns"></a><a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

В следующем фрагменте кода показано, как получить все элементы из таблицы **ToDoItems**, в которой отображаются только поля **complete** и **text**. **mToDoTable** — это ссылка на таблицу серверной части, созданную ранее:

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Параметры функции select являются строковыми именами столбцов таблицы, которые требуется вернуть.

Метод **select** должен следовать за такими методами, как **where** и **orderBy**. Затем можно вызывать такие методы разбивки на страницы, как **top**.

### <a name="<a-name="chaining"></a>how-to:-concatenate-query-methods"></a><a name="chaining"></a>Практическое руководство. Объединение методов запросов

Методы, используемые в запросах таблиц серверной части, можно сцепить. Цепочка методов запроса позволяет, например, выбирать определенные столбцы отфильтрованных строк, которые сортируются и разбиваются на страницы. Можно создавать сложные логические фильтры.
Каждый метод запроса возвращает объект Query. Чтобы завершить серию методов и фактически выполнить запрос, вызовите метод **execute** . Например:

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

Методы запроса в цепочке должны быть упорядочены следующим образом:

1. методы фильтрации (**где**);
2. методы сортировки (**orderBy**);
3. методы выборки (**выберите**);
4. методы разбиения по страницам (**skip** и **top**).

##<a name="<a-name="inserting"></a>how-to:-insert-data-into-the-backend"></a><a name="inserting"></a>Практическое руководство. Вставка данных в серверную часть

Создайте экземпляр класса *ToDoItem* и задайте его свойства.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Затем с помощью **insert()** вставьте объект.

    ToDoItem entity = mToDoTable.insert(item).get();

Возвращаемая сущность соответствует данным, вставленным в таблицу серверной части, включая идентификатор и другие значения, заданные в серверной части.

Для таблиц мобильных приложений требуется столбец первичного ключа **id**. По умолчанию это столбец строковых значений. По умолчанию столбец идентификаторов содержит значения GUID.  Можно указать другие уникальные значения, в том числе электронные адреса или имена пользователей. Если строковое значение идентификатора для вставленной записи не предусмотрено, то серверная часть создает новое значение GUID.

Использование строковых значений идентификаторов связано с такими преимуществами.

+ Идентификаторы можно создавать без обмена данными с базой данных.
+ Можно легко объединять записи из разных таблиц или баз данных.
+ Значения идентификаторов удобнее интегрировать с логикой приложения.

Строковые значения идентификатора **НЕОБХОДИМЫ** для поддержки автономной синхронизации.

##<a name="<a-name="updating"></a>how-to:-update-data-in-a-mobile-app"></a><a name="updating"></a>Практическое руководство. Обновление данных в мобильном приложении

Чтобы обновить данные в таблице, передайте новый объект в метод **update()** .

    mToDoTable.update(item).get();

В этом примере *item* — это ссылка на строку в таблице *ToDoItem*, в которую были внесены изменения.
Строка с таким же значением **id** обновится.

##<a name="<a-name="deleting"></a>how-to:-delete-data-in-a-mobile-app"></a><a name="deleting"></a>Практическое руководство. Удаление данных в мобильном приложении

В следующем коде показано, как удалить данные из таблицы, указав объект данных.

    mToDoTable.delete(item);

Удалить элемент также можно, указав поле **id** удаляемой строки.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="<a-name="lookup"></a>how-to:-look-up-a-specific-item"></a><a name="lookup"></a>Поиск определенного элемента

Найдите элемент с определенным значением в поле **id** с помощью метода **lookUp()**.

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="<a-name="untyped"></a>how-to:-work-with-untyped-data"></a><a name="untyped"></a>Практическое руководство. Работа с нетипизированными данными

Нетипизированная модель программирования предоставляет точный контроль над сериализацией JSON.  Существует несколько распространенных сценариев, в которых может потребоваться использовать нетипизированную модель программирования. Например, если таблица серверной части содержит много столбцов, и вам нужно указать ссылку только на их подмножество.  Для использования типизированной модели необходимо определить все столбцы таблицы мобильного приложения в классе данных.  

Большая часть вызовов интерфейса API для доступа к данным аналогична вызовам в типизированной модели. Основное различие заключается в том, что в нетипизированной модели методы вызываются для объекта **MobileServiceJsonTable**, а не **MobileServiceTable**.

### <a name="<a-name="json_instance"></a>how-to:-create-an-instance-of-an-untyped-table"></a><a name="json_instance"></a>Практическое руководство. Создание экземпляра нетипизированной таблицы

Аналогично типизированной модели сначала вы получаете ссылку на таблицу, но в данном случае это объект **MobileServicesJsonTable** . Чтобы получить ссылку, вызовите метод **getTable** для экземпляра клиента.

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

После создания экземпляр **MobileServiceJsonTable**обладает практически таким же API, что и в типизированной модели программирования. В некоторых случаях методы принимают нетипизированный параметр вместо типизированного.

### <a name="<a-name="json_insert"></a>how-to:-insert-into-an-untyped-table"></a><a name="json_insert"></a>Практическое руководство. Вставка в нетипизированную таблицу

В следующем коде показано, как вставить данные. Первый шаг — создание объекта [JsonObject][1], который является частью библиотеки [gson][3].

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Затем с помощью **insert()** вставьте нетипизированный объект в таблицу.

    mJsonToDoTable.insert(jsonItem).get();

Если необходимо получить идентификатор вставленного объекта, используйте метод **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="<a-name="json_delete"></a>how-to:-delete-from-an-untyped-table"></a><a name="json_delete"></a>Практическое руководство. Удаление из нетипизированной таблицы

В следующем коде показано, как удалить экземпляр, в этом случае тот же экземпляр объекта **JsonObject** , который был создан в предыдущем примере *вставки* . Код будет таким же, как и в случае типизированной таблицы, но метод будет иметь другую сигнатуру, так как он ссылается на **JsonObject**.

         mToDoTable.delete(jsonItem);

Вы также можете удалить экземпляр напрямую с помощью идентификатора:

         mToDoTable.delete(ID);

### <a name="<a-name="json_get"></a>how-to:-return-all-rows-from-an-untyped-table"></a><a name="json_get"></a>Практическое руководство. Получение всех строк из нетипизированной таблицы

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

Для нетипизированной модели доступен тот же набор средств фильтрации, то есть методы фильтрации и разбиения по страницам, что и для типизированной модели.

##<a name="<a-name="custom-api"></a>how-to:-call-a-custom-api"></a><a name="custom-api"></a>Практическое руководство. Вызов настраиваемого интерфейса API

Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Вызовите из клиента Android метод **invokeApi** для вызова конечной точки настраиваемого API. В следующем примере показано, как вызвать конечную точку API с именем **completeAll**, которая возвращает класс коллекции с именем **MarkAllResult**.

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

Метод **invokeApi** вызывается на стороне клиента, что приводит к отправке запроса POST новому настраиваемому API. Результат, возвращаемый настраиваемым интерфейсом API, отображается в диалоговом окне сообщения, как и любые ошибки. Другие версии метода **invokeApi** позволяют при необходимости отправить объект в тексте запроса, указать метод HTTP и отправить параметры запроса вместе с запросом. Кроме того, доступны нетипизированные версии **invokeApi** .

##<a name="<a-name="authentication"></a>how-to:-add-authentication-to-your-app"></a><a name="authentication"></a>Практическое руководство. Добавление проверки подлинности в приложение

Процесс добавления этих компонентов подробно описан в руководствах.

Служба приложений поддерживает [проверку подлинности пользователей приложения](app-service-mobile-android-get-started-users.md) с помощью разных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверной части.

Поддерживаются два потока аутентификации: **серверный** и **клиентский**. Серверный поток обеспечивает самый простой способ аутентификации, так как он использует веб-интерфейс поставщика удостоверений.  Для реализации аутентификации серверного потока не нужны дополнительные пакеты SDK. Аутентификация серверного потока не обеспечивает тесную интеграцию с мобильным устройством и рекомендуется только для подтверждения концепции.

Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK, предоставляемые конкретным поставщиком удостоверений.  Например, можно интегрировать пакет SDK для Facebook в мобильное приложение.  Мобильный клиент переключается на приложение Facebook и подтверждает ваш вход, прежде чем переключиться обратно на мобильное приложение.

Чтобы включить аутентификацию в приложении, необходимо выполнить четыре действия:

- Регистрация приложения для аутентификации в поставщике удостоверений.
- Настройка серверной части службы приложений.
- Ограничение разрешений таблицы только аутентифицированными пользователями.
- Добавление кода проверки подлинности в приложение.

Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Также можно использовать идентификатор безопасности пользователя, прошедшего проверку, чтобы изменять запросы.  Дополнительную информацию см. в разделе [Приступая к работе с проверкой подлинности] и справочной документации по пакету SDK для сервера.

### <a name="<a-name="caching"></a>how-to:-add-authentication-code-to-your-app"></a><a name="caching"></a>Практическое руководство. Добавление кода проверки подлинности в приложение

Следующий код запускает процесс входа серверного потока с помощью поставщика Google.

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Получите идентификатор вошедшего в систему пользователя из **MobileServiceUser** с помощью метода **getUserId**. Пример использования Futures для вызова API асинхронного входа см. в статье [Добавление проверки подлинности в приложение Android].

### <a name="<a-name="caching"></a>how-to:-cache-authentication-tokens"></a><a name="caching"></a>Практическое руководство. Кэширование маркеров проверки подлинности

Для этого необходимо сохранить идентификатор пользователя и маркер аутентификации локально на устройстве. При следующем запуске приложения проверьте кэш — если эти значения существуют, то можно пропустить процедуру входа и повторно заполнить клиент этими данными. Однако это конфиденциальные данные и они должны храниться в зашифрованном виде, чтобы обеспечить безопасность в случае кражи телефона.

Полный пример кэширования маркеров аутентификации см. в разделе [Кэширование маркеров проверки подлинности на клиенте][7].

При попытке использовать маркер с истекшим сроком действия вы получите ответ *401 не санкционировано* . Ошибки аутентификации можно обрабатывать с помощью фильтров.  Фильтры перехватывают запросы к серверной части службы приложений. Код фильтра проверяет наличие ответа 401, инициирует вход в систему и возобновляет запрос, породивший ответ 401. 

## <a name="<a-name="adal"></a>how-to:-authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Практическое руководство. Проверка подлинности пользователей с помощью библиотеки проверки подлинности Active Directory

Библиотеку проверки подлинности Active Directory (ADAL) можно использовать для входа пользователей в приложение с помощью Azure Active Directory. Использование входа посредством клиентского потока является более предпочтительным, чем использование методов `loginAsync()` , так как он обеспечивает более естественный интерфейс входа для пользователя и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью AAD, следуя указаниям в учебнике [Настройка приложения службы приложений для использования службы входа Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Обязательно выполните дополнительный этап регистрации собственного клиентского приложения.

2. Установите ADAL, добавив в файл build.gradle следующие определения.

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Добавьте указанный ниже код в приложение, выполнив следующие замены.

* Замените строку **INSERT-AUTHORITY-HERE** именем клиента, в котором подготовлено приложение. Формат должен быть следующим: https://login.windows.net/contoso.onmicrosoft.com. Это значение можно скопировать на вкладке "Домен" в разделе Azure Active Directory на [классическом портале Azure].

* Замените текст **INSERT-RESOURCE-ID-HERE** идентификатором клиента для серверной части мобильного приложения. Идентификатор клиента можно скопировать на портале в разделе **Настройки Azure Active Directory** на вкладке **Дополнительно**.

* Замените текст **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

* Замените текст **INSERT-REDIRECT-URI-HERE** конечной точкой сайта _/.auth/login/done_ , используя схему HTTPS. Это значение должно быть похоже на _https://contoso.azurewebsites.net/.auth/login/done_.

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

## <a name="how-to:-add-push-notification-to-your-app"></a>Практическое руководство. Добавление push-уведомлений в приложение

Подробные сведения см. в [этом обзоре][6], в котором объясняется, как центры уведомлений Microsoft Azure поддерживают разные виды push-уведомлений.  В [этом руководстве][5] показано, как настроить отправку push-уведомлений при каждой вставке записи.

## <a name="how-to:-add-offline-sync-to-your-app"></a>Практическое руководство. Добавление автономной синхронизации в приложение

Это руководство по быстрому запуску содержит код, который реализует автономную синхронизацию. Найдите код, начинающийся с приведенного ниже комментария.

    // Offline Sync

Раскомментировав строки кода, вы реализуете автономную синхронизацию. Подобный код можно добавлять в код других мобильных приложений.

##<a name="<a-name="customizing"></a>how-to:-customize-the-client"></a><a name="customizing"></a>Практическое руководство. Настройка клиента

Есть несколько способов настройки стандартного поведения клиента.

### <a name="<a-name="headers"></a>how-to:-customize-request-headers"></a><a name="headers"></a>Практическое руководство. Настройка заголовков запроса

Настройте **ServiceFilter** для добавления настраиваемого заголовка HTTP для каждого запроса.

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="<a-name="serialization"></a>how-to:-customize-serialization"></a><a name="serialization"></a>Практическое руководство. Настройка сериализации

Клиент предполагает, что все имена таблиц, имена столбцов и типы данных в серверной части точно соответствуют объектам данных, определенным в клиенте. Имена на сервере и клиенте могут не совпадать по множеству причин. Вам может потребоваться выполнить следующие виды настройки.

- Имена столбцов, используемые в таблице службы приложений, не совпадают с именами, которые используются в клиенте.
- Используйте таблицу службы приложений с именем, отличающимся от имени класса, с которым она сопоставляется в клиенте.
- Включение автоматического использования прописных букв для свойств.
- Добавление сложных свойств в объект.

### <a name="<a-name="columns"></a>how-to:-map-different-client-and-server-names"></a><a name="columns"></a>Практическое руководство. Сопоставление различных имен клиента и сервера

Предположим, что клиентский код Java использует стандартные имена в стиле Java для свойств объекта **ToDoItem** , как показано ниже.

- mId
- mText
- mComplete
- mDuration

Сериализуйте имена клиентов в имена JSON, совпадающие с именами столбцов таблицы **ToDoItem** на сервере. В следующем коде используется библиотека [gson][3] для добавлений заметок к свойствам.

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="<a-name="table"></a>how-to:-map-different-table-names-between-the-client-and-the-backend"></a><a name="table"></a>Практическое руководство. Сопоставление разных имен таблиц между клиентом и серверной частью

Сопоставьте имя клиентской таблицы с именем таблицы различных мобильных служб, используя переопределение метода [getTable()][4].

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="<a-name="conversions"></a>how-to:-automate-column-name-mappings"></a><a name="conversions"></a>Практическое руководство. Автоматизация сопоставления имен столбцов

Можно указать стратегию преобразования, которая применяется к каждому столбцу, с помощью API из библиотеки [gson][3]. Клиентская библиотека Android использует [gson][3] для сериализации объектов Java в данные JSON перед передачей данных в службу приложений Azure.  В следующем коде используется метод **setFieldNamingStrategy()** для задания стратегии. Этот пример удалит начальный знак ("m"), а затем преобразует следующий знак в нижний регистр для каждого имени поля. Например, он преобразует "mId" в "id".

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Этот код должен быть выполнен перед использованием **MobileServiceClient**.

### <a name="<a-name="complex"></a>how-to:-store-an-object-or-array-property-into-a-table"></a><a name="complex"></a>Практическое руководство. Сохранение свойства объекта или массива в таблице

Пока что наши примеры сериализации использовали простые типы, такие как целочисленный и строковый.  Простые типы легко сериализовать в JSON.  Если необходимо добавить сложный объект, который не сериализуется автоматически в формат JSON, то необходимо предоставить метод сериализации в JSON.  Пример предоставления пользовательской сериализации в JSON см. в записи блога [Customizing serialization using the gson library in the Mobile Services Android client][2] (Настройка сериализации с помощью библиотеки gson в клиенте мобильных служб для Android).

<!-- Anchors. -->

[Что такое мобильные службы]: #what-is
[Основные понятия]: #concepts
[Практическое руководство. Создание клиента мобильных служб]: #create-client
[Практическое руководство. Создание ссылки на таблицу]: #instantiating
[Структура API]: #api
[Практическое руководство. Запрос данных от мобильной службы]: #querying
[Возврат всех элементов]: #showAll
[Фильтрация возвращаемых данных]: #filtering
[Сортировка возвращаемых данных]: #sorting
[Возврат данных на страницах]: #paging
[Выбор определенных столбцов]: #selecting
[Практическое руководство. Объединение методов запросов]: #chaining
[Привязка данных к пользовательскому интерфейсу]: #binding
[Практическое руководство. Определение макета]: #layout
[Практическое руководство. Определение адаптера]: #adapter
[Практическое руководство. Использование адаптера]: #use-adapter
[Практическое руководство. Вставка данных в мобильную службу]: #inserting
[How to: update data in a mobile service]: #updating
[Практическое руководство. Удаление данных в мобильной службе]: #deleting
[Поиск определенного элемента]: #lookup
[Практическое руководство. Работа с нетипизированными данными]: #untyped
[Практическое руководство. Проверка подлинности пользователей]: #authentication
[Кэширование маркеров аутентификации]: #caching
[Практическое руководство. Обработка ошибок]: #errors
[Практическое руководство. Разработка модульных тестов]: #tests
[Практическое руководство. Настройка клиента]: #customizing
[Настройка заголовков запросов]: #headers
[Настраиваемая сериализация]: #serialization
[Next Steps]: #next-steps
[Настройка и необходимые компоненты]: #setup

<!-- Images. -->

<!-- URLs. -->
[Приступая к работе с мобильными приложениями Azure]: app-service-mobile-android-get-started.md
[Управляющие коды ASCII C0 и C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Пакет SDK для мобильных служб для Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[портал Azure]: https://portal.azure.com
[Приступая к работе с проверкой подлинности]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html


<!--HONumber=Oct16_HO2-->


