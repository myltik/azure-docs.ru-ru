<properties linkid="develop-mobile-how-to-guides/work-with-android-client-library" urlDisplayName="Android Client Library" pageTitle="Working with the Mobile Services Android Client Library" metaKeywords="" description="Learn how to use an Android client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the Android client library for Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Использование клиентской библиотеки Android для мобильных служб

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>

В этом руководстве показано, как реализовать типичные сценарии с использованием клиента Android для мобильных служб Azure. В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей, обработка ошибок и настройка клиента. Если вы не знакомы с мобильными службами, сначала мы рекомендуем прочитать [краткое руководство по мобильным службам][краткое руководство по мобильным службам]. Учебник quickstart поможет вам настроить учетную запись и создать свою первую мобильную службу.

Примеры написаны на Java и используют [Пакет Mobile Services SDK][Пакет Mobile Services SDK]. Чтобы изучить этот учебник, необходим [пакет Android SDK][пакет Android SDK], который включает интегрированную среду разработки Eclipse (IDE) и подключаемый модуль средств разработки Android (ADT). Пакет Mobile Services SDK поддерживает Android 2.2 и более поздние версии, но мы рекомендуем использовать в качестве цели Android 4.2 или более поздние версии.

## Оглавление

-   [Что такое мобильные службы?][Что такое мобильные службы?]
-   [Основные понятия][Основные понятия]
-   [Настройка и необходимые компоненты][Настройка и необходимые компоненты]
-   [Практическое руководство. Создание клиента мобильных служб][Практическое руководство. Создание клиента мобильных служб]
-   [Практическое руководство. Создание ссылки на таблицу][Практическое руководство. Создание ссылки на таблицу]
    -   [Структура API][Структура API]
-   [Практическое руководство. Запрос данных от мобильной службы][Практическое руководство. Запрос данных от мобильной службы]
    -   [Фильтрация возвращаемых данных][Фильтрация возвращаемых данных]
    -   [Сортировка возвращаемых данных][Сортировка возвращаемых данных]
    -   [Возврат данных на страницах][Возврат данных на страницах]
    -   [Выбор определенных столбцов][Выбор определенных столбцов]
    -   [Практическое руководство. Сцепка методов запросов][Практическое руководство. Сцепка методов запросов]
-   [Практическое руководство. Вставка данных в мобильную службу][Практическое руководство. Вставка данных в мобильную службу]
-   [Практическое руководство. Обновление данных в мобильной службе][Практическое руководство. Обновление данных в мобильной службе]
-   [Практическое руководство. Удаление данных в мобильной службе][Практическое руководство. Удаление данных в мобильной службе]
-   [Практическое руководство. Поиск конкретного элемента][Практическое руководство. Поиск конкретного элемента]
-   [Практическое руководство. Работа с нетипизированными данными][Практическое руководство. Работа с нетипизированными данными]
-   [Практическое руководство. Привязка данных к пользовательскому интерфейсу][Практическое руководство. Привязка данных к пользовательскому интерфейсу]
    -   [Практическое руководство. Определение макета][Практическое руководство. Определение макета]
    -   [Практическое руководство. Определение адаптера][Практическое руководство. Определение адаптера]
    -   [Практическое руководство. Использование адаптера][Практическое руководство. Использование адаптера]
-   [Практическое руководство. Проверка подлинности пользователей][Практическое руководство. Проверка подлинности пользователей]
    -   [Кэширование маркеров проверки подлинности][Кэширование маркеров проверки подлинности]
-   [Практическое руководство. Обработка ошибок][Практическое руководство. Обработка ошибок]
-   [Практическое руководство. Настройка клиента][Практическое руководство. Настройка клиента]
    -   [Настройка заголовков запроса][Настройка заголовков запроса]
    -   [Настройка сериализации][Настройка сериализации]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

## <a name="setup"></a><span class="short-header">Настройка</span>Настройка и предварительные требования

Мы предполагаем, что вы создали мобильную службу и таблицу. Дополнительные сведения см. в разделе [Создание таблицы][Создание таблицы]. В коде, используемом в данном разделе, мы предполагаем, что имя таблицы — *ToDoItem* и что она содержит следующие столбцы:

-   ид
-   текст
-   полная
-   за
-   продолжительность

Соответствующий типизированный клиентский объект выглядит следующим образом:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
        private Date due
        private Integer duration;
    }

Если динамическая схема включена, мобильные службы Azure автоматически создают новые столбцы на основе объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема][Динамическая схема].

## <a name="create-client"></a><span class="short-header">Создание клиента мобильных служб</span>Практическое руководство. Создание клиента мобильных служб

Следующий код создает объект [MobileServiceClient][MobileServiceClient], используемый для доступа к мобильной службе.

            MobileServiceClient mClient = new MobileServiceClient(
                    "MobileServiceUrl", // Replace with the above Site URL
                    "AppKey",           // replace with the Application Key 
                    this)

В приведенном выше коде замените `MobileServiceUrl` и `AppKey` на URL-адрес мобильной службы и ключ приложения соответственно. Оба эти варианты доступны на портале управления Azure: выберите свою мобильную службу и щелкните *Панель управления*.

## <a name="instantiating"></a><span class="short-header">Создание ссылки на таблицу</span>Практическое руководство. Создание ссылки на таблицу

Самый простой способ запросить или изменить данные в мобильной службе — использование *типизированной модели программирования*, поскольку Java является строго типизированным языком (позже мы расскажем о *нетипизированной* модели). Эта модель обеспечивает простую сериализацию и десериализацию в формате JSON с помощью библиотеки [gson][gson] при передаче данных между клиентом и мобильной службой: разработчику не нужно ничего делать, платформа сама занимается обработкой.

Первое, что необходимо сделать для запроса или изменения данных — создать объект [MobileServiceTable][MobileServiceTable], вызвав метод **getTable** объекта [**MobileServiceClient**][MobileServiceClient]. Мы рассмотрим две перегрузки этого метода:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

В следующем коде *mClient* представляет собой ссылку на ваш клиент мобильной службы.

[Первая перегрузка][Первая перегрузка] используется, когда имя класса и имя таблицы одинаковы:

        MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

[Вторая перегрузка][Вторая перегрузка] используется, когда имя таблицы отличается от имени типа.

        MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="api"></a>Структура API

Табличные операции мобильных служб используют модель асинхронного обратного вызова. У всех методов, использующих запросы и такие операции, как вставка, обновление и удаление, есть параметр, который является объектом обратного вызова. Этот объект всегда содержит метод **OnCompleted**. Метод **OnCompleted** содержит один параметр, являющийся объектом **Exception**, который можно проверить для определения успешности вызова метода. Объект **Exception** со значением NULL указывает на успешное завершение, в противном случае значение объекта **Exception** описывает причину ошибки.

Существует несколько объектов обратного вызова. То, какой из них вы будете использовать, зависит от того, будете ли вы запрашивать, изменять или удалять данные. Параметры метода *onCompleted* различаются в зависимости от того, частью какого объекта обратного вызова он является.

## <a name="querying"></a><span class="short-header">Запрос данных</span>Практическое руководство. Запрос данных из мобильной службы

В этом разделе описывается, как отправлять запросы мобильной службе. В подразделах описываются различные аспекты, например сортировка, фильтрация и разбиение по страницам. Наконец, мы обсудим, как можно объединять эти операции.

Следующий код возвращает все элементы в таблице*ToDoItem*.

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

Подобные запросы используют объект обратного вызова [**TableQueryCallback\<E\>**][**TableQueryCallback\<E\>**].

Параметр *result* возвращает результирующий набор запроса, а код внутри ветви успешного выполнения теста *exception* показывает, как анализировать отдельные строки.

### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий код возвращает все элементы из таблицы *ToDoItem*, поле *complete* которой имеет значение *false*. *mToDoTable* — это ссылка на таблицу мобильной службы, которую мы создали ранее.

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

Для фильтрации вызовите метод [**where**][**where**] для ссылки на таблицу. После этого вызовите метод [**field**][**field**], а затем — метод, который определяет логический предикат. Возможные методы предиката: [**eq**][**eq**], [**ne**][**ne**], [**gt**][**gt**], [**ge**][**ge**], [**lt**][**lt**], [**le**][**le**] и т. д.

Этого достаточно для сравнения числовых и строковых полей с указанными значениями. Однако вы можете сделать гораздо больше.

Например, можно фильтровать данные по датам. Можно сравнить все поле даты, но также можно сравнивать части даты, используя такие методы, как [**year**][**year**], [**month**][**month**], [**day**][**day**], [**hour**][**hour**], [**minute**][**minute**] и [**second**][**second**]. Следующий частичный код добавляет фильтр для элементов, *дата выполнения* которых равна 2013.

        mToDoTable.where().year("due").eq(2013)

Вы можете применять разнообразные сложные фильтры к строковым полям с помощью таких методов, как [**startsWith**][**startsWith**], [**endsWith**][**endsWith**], [**concat**][**concat**], [**subString**][**subString**], [**indexOf**][**indexOf**], [**replace**][**replace**], [**toLower**][**toLower**], [**toUpper**][**toUpper**], [**trim**][**trim**] и [**length**][**length**]. Следующий частичный код фильтрует строки таблицы, столбец *text* которых начинается с "PRI0".

        mToDoTable.where().startsWith("text", "PRI0")

Числовые поля также позволяют применять более широкий спектр сложных фильтров с помощью таких методов, как [**add**][**add**], [**sub**][**sub**], [**mul**][**mul**], [**div**][**div**], [**mod**][**mod**], [**floor**][**floor**], [**ceiling**][**ceiling**] и [**round**][**round**]. Следующий частичный код фильтрует строки таблицы, значения *длительности* которых является четным числом.

        mToDoTable.where().field("duration").mod(2).eq(0)

Предикаты можно объединить с помощью методов [**and**][**and**], [**or**][**or**] и [**not**][**not**]. Этот частичный код объединяет два приведенных выше примера.

        mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")

Кроме того, можно группировать логические операторы и вкладывать их друг в друга, как показано в этом частичном коде:

        mToDoTable.where()
                    .year("due").eq(2013)
                        .and
                    (startsWith("text", "PRI0").or().field("duration").gt(10))

Более подробное описание и примеры фильтрации см. в статье [Исследование возможностей модели запросов клиента мобильных служб для Android][Исследование возможностей модели запросов клиента мобильных служб для Android].

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

Следующий код возвращает все элементы из таблицы *ToDoItem*, упорядоченные по полю *text*. *mToDoTable* — это ссылка на таблицу мобильной службы, которую вы создали ранее.

        mToDoTable.orderBy("text", QueryOrder.Ascending)
            .execute(new TableQueryCallback<ToDoItem>() { 
                /* same implementation as above */ 
            }); 

Первый параметр метода [**orderBy**][**orderBy**] — это строка, которая совпадает с именем поля, по которому выполняется сортировка.

Второй параметр использует перечисление [**QueryOrder**][**QueryOrder**], чтобы указать, следует ли сортировать данные по возрастанию или убыванию.

Обратите внимание, что если используется фильтрация с помощью метода ***where***, метод ***where*** необходимо вызывать до метода ***orderBy***.

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах

Первый пример показывает, как выбрать 5 верхних элементов из таблицы. Этот запрос возвращает элементы из таблицы *ToDoItem*. *mToDoTable* — это ссылка на таблицу мобильной службы, которую вы создали ранее.

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

Следующий код показывает, как получить все элементы из таблицы *ToDoItems*, но отображает только поля *complete* и *text*. *mToDoTable* — это ссылка на таблицу мобильной службы, которую мы создали ранее.

        mToDoTable.select("complete", "text")
                .execute(new TableQueryCallback<ToDoItem>() { 
                    /* same implementation as above */ 
            }); 

Здесь параметры функции select являются строковыми именами столбцов таблицы, которые требуется вернуть.

Метод [**select**][**select**] должен следовать за такими методами, как [**where**][1] и [**orderBy**][2], если они используются. После него можно вызывать такие методы, как [**top**][**top**].

### <a name="chaining"></a>Практическое руководство. Сцепка методов запросов

Методы, используемые в таблицах мобильных служб с поддержкой запросов, могут быть объединены. Это позволяет, например, выбирать определенные столбцы или фильтрованные строки, которые сортируются и разбиваются на страницы. Можно создавать весьма сложные логические фильтры.

Это возможно из-за того, что используемые методы запроса возвращают объекты [**MobileServiceQuery\<T\>**][**MobileServiceQuery\<T\>**], в которых в свою очередь можно вызывать дополнительные методы. Чтобы завершить серию методов и фактически выполнить запрос, вызовите метод [**execute**][**execute**].

Ниже приведен пример кода, где *mToDoTable* — это ссылка на таблицу мобильных служб *ToDoItem*.

        mToDoTable.where().year("due").eq(2013)
                        .and().startsWith("text", "PRI0")
                        .or().field("duration").gt(10)
                    .select("id", "complete", "text", "duration")
                    .orderBy(duration, QueryOrder.Ascending).top(20)                
                    .execute(new TableQueryCallback<ToDoItem>() { 
                        /* code to execute */ 
                });

Главное требование при объединении методов состоит в том, что метод *where* и предикаты должны идти первыми. После этого можно вызвать последующие методы в том порядке, который лучше всего соответствует требованиям вашего приложения.

## <a name="inserting"></a><span class="short-header">Вставка данных</span>Практическое руководство. Вставка данных в мобильную службу

В следующем коде показано, как вставить новые строки в таблицу.

Сначала необходимо создать экземпляр класса *ToDoItem* и задать его свойства.

        ToDoItem mToDoItem = new ToDoItem();
        mToDoItem.text = "Test Program";
        mToDoItem.complete = false;
        mToDoItem.duration = 5; 
        

Затем вызовите метод [**insert**][**insert**].

        mToDoTable.insert(mToDoItem, new TableOperationCallback<ToDoItem>() {
            public void onCompleted(ToDoItem entity, 
                                Exception exception, 
                                ServiceFilterResponse response) {   
                if (exception == null) {
                        Log.i(TAG, "Read object with ID " + entity.id);  
                } 
            }
        });

Для операций **insert** объектом обратного вызова является [**TableOperationCallback\<ToDoItem\>**][**TableOperationCallback\<ToDoItem\>**].

Параметр entity метода **onCompleted** содержит только что вставленный объект. В следующем примере показано, как получить доступ к *идентификатору* вставленной строки.

Мобильные службы поддерживают уникальные настраиваемые строковые значения идентификатора таблицы. Это позволяет приложениям использовать настраиваемые значения, например, адреса электронной почты или имена пользователей, для столбца идентификатора таблицы мобильных служб. Например, если требуется идентифицировать каждую запись по адресу электронной почты, можно использовать следующий объект JSON.

        ToDoItem mToDoItem = new ToDoItem();
        mToDoItem.id = "myemail@mydomain.com";
        mToDoItem.text = "Test Program";
        mToDoItem.complete = false;
        mToDoItem.duration = 5; 

Если значение идентификатора строки не указано при вставке новых записей в таблицу, мобильные службы создают уникальное значение идентификатора.

Поддержка строковых идентификаторов предоставляет разработчикам следующие преимущества:

-   Идентификаторы можно создавать без обмена данными с базой данных.
-   Можно легко объединять записи из разных таблиц или баз данных.
-   Значения идентификаторов можно удобно интегрировать с логикой приложения.

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

-   Управляющие символы: [0x0000-0x001F] и [0x007F-0x009F]. Дополнительные сведения см. в статье [Управляющие коды ASCII C0 и C1][Управляющие коды ASCII C0 и C1].
-   Печатные символы: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Идентификаторы "." и ".."

Также для таблиц можно использовать целочисленные идентификаторы. Для этого необходимо создать таблицу с помощью команды `mobile table create`, используя параметр `--integerId`. Эта команда используется в интерфейсе командной строки (CLI) Azure. Дополнительные сведения об использовании CLI см. в статье [Интерфейс командной строки для управления таблицами мобильных служб][Интерфейс командной строки для управления таблицами мобильных служб].

## <a name="updating"></a><span class="short-header">Обновление данных</span>Практическое руководство. Обновление данных в мобильной службе

В следующем коде показано, как обновить данные в таблице. В этом примере *mToDoItem* — это ссылка на элемент в таблице *ToDoItem*, мы обновляем ее свойство *duration*.

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

## <a name="deleting"></a><span class="short-header">Удаление данных</span>Практическое руководство. Удаление данных в мобильной службе

В следующем коде показано, как удалить данные из таблицы. Пример удаляет существующий элемент из таблицы ToDoItem, используя ссылку на элемент, в этом случае это *mToDoItem*.

        mToDoTable.delete(mToDoItem, new TableDeleteCallback() {
            public void onCompleted(Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

Обратите внимание, что при *удалении* объект обратного вызова — это [**TableDeleteCallback**][**TableDeleteCallback**], а метод**onCompleted** отличается тем, что строки таблицы не возвращаются.

В следующем примере кода показан еще один способ для этого. Пример удаляет существующий элемент из таблицы ToDoItem, указывая значение в поле идентификатора строки (предполагается, что он равен "37BBF396-11F0-4B39-85C8-B319C729AF6D").

        mToDoTable.delete("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableDeleteCallback() {
            public void onCompleted(Exception exception, 
                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

## <a name="lookup"></a><span class="short-header">Поиск данных</span>Практическое руководство. Поиск конкретного элемента

Иногда вам требуется найти определенный элемент по его *идентификатору* в отличие от запросов, где вы обычно получаете коллекцию элементов, отвечающих некоторым критериям. В следующем коде показано, как это сделать для *идентификатора* "37BBF396-11F0-4B39-85C8-B319C729AF6D".

        mToDoTable.lookUp("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableOperationCallback<ToDoItem>() {
            public void onCompleted(item entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Read object with ID " + entity.id);    
                }
            }
        });

## <a name="untyped"></a><span class="short-header">Работа с нетипизированными данными</span>Практическое руководство. Работа с нетипизированными данными

Нетипизированная модель программирования предоставляет полный контроль над сериализацией JSON. Существует несколько сценариев, в которых вы можете использовать ее, например, если таблица мобильной службы содержит множество столбцов, а вам нужно ссылаться только на небольшое их число. Для использования типизированной модели необходимо определить все столбцы таблицы мобильной службы в классе данных. Но с нетипизированной моделью требуется определить только столбцы, которые вы будете использовать.

Аналогично типизированной модели сначала вы получаете ссылку на таблицу, но в данном случае это объект [MobileServicesJsonTable][MobileServicesJsonTable]. Чтобы получить ссылку, вызовите метод [getTable()][getTable()] экземпляра клиента мобильных служб.

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

В следующем коде показано, как вставить данные. Первый шаг — создание объекта  [**JsonObject**][**JsonObject**], который является частью библиотеки [gson][gson].

        JsonObject task = new JsonObject();
        task.addProperty("text", "Wake up");
        task.addProperty("complete", false);
        task.addProperty("duration", 5);

Следующий шаг — вставка объекта. Функция обратного вызова, которая передается методу [**insert**][3] — это экземпляр класса [**TableJsonOperationCallback**][**TableJsonOperationCallback**]. Обратите внимание на то, что первый параметр метода *onCompleted* — это JsonObject.

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

В следующем коде показано, как удалить экземпляр, в этом случае тот же экземпляр объекта **JsonObject**, который был создан в предыдущем примере *вставки*. Обратите внимание, что объект обратного вызова, **TableDeleteCallback** — это тот же объект, который используется в типизированной модели программирования, а сигнатура метода **onCompleted** отличается от той, что использована в примере **вставки**.

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

В следующем коде показано, как получить всю таблицу. Обратите внимание, что нетипизированная модель программирования использует другой объект обратного вызова: [**TableJsonQueryCallback**][**TableJsonQueryCallback**].

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

## <a name="binding"></a><span class="short-header">Привязка данных</span>Практическое руководство. Привязка данных к пользовательскому интерфейсу

Привязка данных состоит из трех компонентов:

-   источник данных;
-   макет экрана;
-   адаптер, который связывает два других компонента.

В нашем примере кода мы возвращаем данные из таблицы мобильной службы *ToDoItem* в массив. Это очень распространенный шаблон для приложений, работающих с данными: запросы к базе данных обычно возвращают набор строк, которые клиент получает в списке или массиве. В этом примере массив является источником данных.

Код указывает макет экрана, который определяет представление данных, отображаемых на устройстве.

Два компонента соединяются адаптером, который в этом коде является расширением класса *ArrayAdapter\<ToDoItem\>*.

### <a name="layout"></a>Практическое руководство. Определение макета

Макет определяется несколькими фрагментами XML-кода. Учитывая существующий макет, предположим, что следующий код представляет объект **ListView**, который требуется заполнить данными с сервера.

        <ListView
            android:id="@+id/listViewToDo"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            tools:listitem="@layout/row_list_to_do" >
        </ListView>

В приведенном выше коде атрибут *listitem* указывает идентификатор макета для отдельной строки в списке. Вот код, который определяет флажок и связанный с ним текст. Экземпляр создается один раз для каждого элемента в списке. В более сложном макете указываются дополнительные поля. Этот код находится в файле *row\_list\_to\_do.xml*.

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

Поскольку источник данных нашего представления — это массив объектов *ToDoItem*, мы создадим адаптер как подкласс класса *ArrayAdapter\<ToDoItem\>*. Этот подкласс будет создавать представление для каждого объекта *ToDoItem* с помощью макета *row\_list\_to\_do*.

В нашем коде мы определим следующий класс, который расширяет класс *ArrayAdapter\<E\>*:

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

Адаптер также необходимо вызывать при каждом изменении таблицы *ToDoItem*, если вы хотите показать результаты выполнения. Поскольку изменения выполняются на уровне отдельных записей, обрабатываться будут отдельные строки, а не коллекция. При вставке элемента вызывается метод *add* адаптера, а при удалении — метод *remove*.

## <a name="authentication"></a><span class="short-header">Проверка подлинности</span>Практическое руководство. Проверка подлинности пользователей

Мобильные службы поддерживают аутентификацию и авторизацию пользователей с помощью различных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в разделе [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

Поддерживается два потока аутентификации: *серверный* и *клиентский*. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

Чтобы включить проверку подлинности в приложении, необходимо выполнить три действия.

1.  Регистрация приложения для проверки подлинности у поставщика и настройка мобильных служб.
2.  Ограничение разрешений таблицы только пользователями, прошедшими проверку подлинности.
3.  Добавление кода проверки подлинности в приложение

Мобильные службы поддерживают следующие существующие поставщики удостоверений, которые можно использовать для проверки подлинности пользователей:

-   Учетная запись Майкрософт
-   Facebook
-   Twitter
-   Google
-   Azure Active Directory

Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Также можно использовать идентификатор пользователя, прошедшего проверку, чтобы изменять запросы.

Первые две задачи выполняются с помощью [портала управления Azure][портала управления Azure]. Дополнительные сведения см. в разделе [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

### <a name="caching"></a>Практическое руководство. Добавление кода проверки подлинности в приложение

1.  Добавьте следующие инструкции import в файл действий приложения.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Добавьте в метод **onCreate** следующую строку после кода, который создает объект `MobileServiceClient`. Мы предполагаем, что ссылка на объект `MobileServiceClient` — *mClient*.

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

    <div class="dev-callout"><b>Примечание.</b>
<p>Если используется поставщик удостоверений, отличный от Google, измените значение, передаваемое в метод <strong>login</strong> выше, на одно из следующих:. <i>MicrosoftAccount</i>, <i>Facebook</i>, <i>Twitter</i> или <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  При запуске приложения выполните вход с использованием выбранного поставщика удостоверений.

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

Что произойдет, если срок действия маркера истекает? В этом случае при попытке использовать маркер для подключения вы получите ответ *401 unauthorized* (нет доступа). Пользователю придется войти в систему, чтобы получить новые маркеры. Можно избежать необходимости писать код для обработки таких случаев везде в приложении, которое вызывает мобильные службы, используя фильтры, которые позволяют перехватывать вызовы и ответы от мобильных служб. Код фильтра затем проверит наличие ответа 401, инициирует вход в систему при необходимости и возобновит запрос, вызвавший ответ 401.

## <a name="errors"></a><span class="short-header">Обработка ошибок</span>Практическое руководство. Обработка ошибок

Пример проверки и обработки ошибок см. [здесь][здесь]. В этом примере проверка реализована с помощью серверных скриптов, которые возвращают исключения для ошибок, и клиентского кода, который обрабатывает исключения.

Другим подходом является предоставление *глобального* обработчика ошибок. Пример кода выше, который обращается к таблице мобильной службы, использует три разных объекта обратного вызова:

-   **TableQueryCallback** и **TableQueryJsonCallback**
-   **TableOperationCallback** и **TableJsonOperationCallback**
-   **TableDeleteCallback**

Каждый из них содержит метод **OnCompleted**, где второй параметр является объектом **java.lang.Exception**. Можно создать подкласс этих объектов обратного вызова и реализовывать собственный метод **onCompleted**, который проверяет, равно ли значение параметра NULL. Если это так, ошибки нет, и нужно просто вызвать метод **super.OnCompleted()**.

Если объект **Exception** не равен NULL, выполните общий процесс обработки ошибок, в котором отображаются более подробные сведения об ошибке. В следующем фрагменте кода показан один из способов предоставления дополнительных сведений.

        String msg = exception.getCause().getMessage();

Теперь разработчики могут использовать свои обратные вызовы из подклассов и не беспокоиться о проверке исключений, так как они обрабатываются в централизованном месте (\#2) для всех экземпляров обратного вызова.

## <a name="customizing"></a><span class="short-header">Настройка клиента</span>Практическое руководство. Настройка клиента

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

По умолчанию мобильные службы предполагают, что имен таблиц, имена столбцов и типы данных на сервере полностью соответствуют аналогам на клиенте. Но имена на сервере и клиенте могут не совпадать по множеству причин. Один из примеров — у вас есть клиент, который нужно изменить так, чтобы он использовал мобильные службы Azure вместо продукта конкурента.

Может потребоваться выполнить следующие виды настройки:

-   Имена столбцов, используемые в таблице мобильной службы, не совпадают с именами, которые используются в клиенте.
-   Использование таблицы мобильной службы с именем, отличным от имени класса, с которым она сопоставляется в клиенте
-   Включение автоматического использования прописных букв свойств
-   Добавление сложных свойств в объект

### <a name="columns"></a>Практическое руководство. Сопоставление различных имен клиента и сервера

Предположим, что клиентский код Java использует стандартные имена в стиле Java для свойств объекта*ToDoItem*, как показано ниже.

-   mId
-   mText
-   mComplete
-   mDuration

Имена клиентов необходимо сериализовать в имена JSON, совпадающие с именами столбцов таблицы*ToDoItem* на сервере. Это реализует следующий код, использующий библиотеку [gson][gson].

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Практическое руководство. Сопоставление разных имен таблиц между клиентом и мобильными службами

Сопоставление клиентского имени таблицы с другим именем таблицы мобильных служб несложно — мы просто используем одну из перегрузок функции [getTable()][Вторая перегрузка], как показано в следующем коде.

        mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Практическое руководство. Автоматизация сопоставлений имен столбцов

Сопоставление имен столбцов для узкой таблицы всего с несколькими столбцами не вызывает трудностей, как мы видели в предыдущем разделе. Но предположим, что наша таблица содержит много столбцов, скажем 20 или 30. Оказывается, можно вызвать API [gson][gson] и определить стратегию преобразования, которая применяются для каждого столбца, и избежать необходимости указывать имя каждого столбца отдельно.

Для этого мы применяем библиотеку [gson][gson], которую клиентская библиотека Android использует для сериализации объектов Java в данные JSON, передаваемые мобильным службам Azure.

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

Пример см. в записи блога [Настройка сериализации с помощью библиотеки][Настройка сериализации с помощью библиотеки] [gson][gson] в клиенте мобильных служб для Android</a>.

Этот общий метод можно использовать всякий раз, когда у нас есть сложный объект, который не может быть автоматически сериализован в формат JSON и таблицу мобильных служб.

## <a name="next-steps"></a>Дальнейшие действия

Справочник Javadocs по API клиента Android находится [][]<http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/package-summary.html></a>



  [краткое руководство по мобильным службам]: /ru-ru/develop/mobile/tutorials/get-started-android/
  [Пакет Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?linkid=280126
  [пакет Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125&clcid=0x409
  [Основные понятия]: #concepts
  [Настройка и необходимые компоненты]: #setup
  [Практическое руководство. Создание клиента мобильных служб]: #create-client
  [Практическое руководство. Создание ссылки на таблицу]: #instantiating
  [Структура API]: #api
  [Практическое руководство. Запрос данных от мобильной службы]: #querying
  [Фильтрация возвращаемых данных]: #filtering
  [Сортировка возвращаемых данных]: #sorting
  [Возврат данных на страницах]: #paging
  [Выбор определенных столбцов]: #selecting
  [Практическое руководство. Сцепка методов запросов]: #chaining
  [Практическое руководство. Вставка данных в мобильную службу]: #inserting
  [Практическое руководство. Обновление данных в мобильной службе]: #updating
  [Практическое руководство. Удаление данных в мобильной службе]: #deleting
  [Практическое руководство. Поиск конкретного элемента]: #lookup
  [Практическое руководство. Работа с нетипизированными данными]: #untyped
  [Практическое руководство. Привязка данных к пользовательскому интерфейсу]: #binding
  [Практическое руководство. Определение макета]: #layout
  [Практическое руководство. Определение адаптера]: #adapter
  [Практическое руководство. Использование адаптера]: #use-adapter
  [Практическое руководство. Проверка подлинности пользователей]: #authentication
  [Кэширование маркеров проверки подлинности]: #caching
  [Практическое руководство. Обработка ошибок]: #errors
  [Практическое руководство. Настройка клиента]: #customizing
  [Настройка заголовков запроса]: #headers
  [Настройка сериализации]: #serialization
  [Дальнейшие действия]: #next-steps
  [mobile-services-concepts]: ../includes/mobile-services-concepts.md
  [Создание таблицы]: http://go.microsoft.com/fwlink/p/?LinkId=298592
  [Динамическая схема]: http://go.microsoft.com/fwlink/p/?LinkId=296271
  [MobileServiceClient]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
  [gson]: %20http://go.microsoft.com/fwlink/p/?LinkId=290801
  [MobileServiceTable]: http://go.microsoft.com/fwlink/p/?LinkId=296835
  [Первая перегрузка]: http://go.microsoft.com/fwlink/p/?LinkId=296839
  [Вторая перегрузка]: http://go.microsoft.com/fwlink/p/?LinkId=296840
  [**eq**]: http://go.microsoft.com/fwlink/p/?LinkId=298461
  [**ne**]: http://go.microsoft.com/fwlink/p/?LinkId=298462
  [**le**]: http://go.microsoft.com/fwlink/p/?LinkId=298466
  [Исследование возможностей модели запросов клиента мобильных служб для Android]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=296296
  [2]: http://go.microsoft.com/fwlink/p/?LinkId=296313
  [Управляющие коды ASCII C0 и C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
  [Интерфейс командной строки для управления таблицами мобильных служб]: http://www.windowsazure.com/ru-ru/manage/linux/other-resources/command-line-tools/#Mobile_Tables
  [MobileServicesJsonTable]: http://go.microsoft.com/fwlink/p/?LinkId=298733
  [3]: http://go.microsoft.com/fwlink/p/?LinkId=298535
  [Приступая к работе с аутентификацией]: http://go.microsoft.com/fwlink/p/?LinkId=296316
  [портала управления Azure]: https://manage.windowsazure.com/
  [здесь]: https://www.windowsazure.com/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
  [Настройка сериализации с помощью библиотеки]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
  []: http://go.microsoft.com/fwlink/p/?LinkId=298735 "здесь"
