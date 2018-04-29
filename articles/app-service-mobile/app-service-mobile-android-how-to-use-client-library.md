---
title: Как использовать пакет SDK для мобильных приложений Azure в клиенте Android | Документация Майкрософт
description: Сведения об использовании пакета SDK для мобильных приложений Azure в клиенте Android.
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: 868ce171206fcd74ee0ecb099b67bf81970bc9ae
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Как использовать пакет SDK для мобильных приложений Azure в клиенте Android

В этом руководстве показано, как использовать пакет SDK для мобильных приложений для клиентов Android, чтобы реализовать распространенные сценарии, в том числе:

* запрос данных (вставка, обновление и удаления);
* аутентификация;
* обработка ошибок;
* настройка клиента.

В этом руководстве описано использование клиентского пакета Android SDK.  См. дополнительные сведения о серверных пакетах SDK для мобильных приложений, включая сведения о [работе с серверными пакетами SDK для .NET][10] и [использовании серверного пакета SDK для Node.js][11].

## <a name="reference-documentation"></a>Справочная документация

[Справочник Javadocs по API][12] клиентской библиотеки Android см. на сайте GitHub.

## <a name="supported-platforms"></a>Поддерживаемые платформы

Пакет SDK для мобильных приложений Azure в клиенте Android поддерживает API уровней 19–24 (от KitKat до Nougat). Это применяется к телефонам и планшетам.  В процессе проверки подлинности, в частности, применяется общий метод сбора учетных данных, используемый на веб-платформах.  Серверный поток проверки подлинности не работает на устройствах малого форм-фактора, таких как часы.

## <a name="setup-and-prerequisites"></a>Настройка и необходимые компоненты

Ознакомьтесь с [кратким учебником по мобильным приложениям](app-service-mobile-android-get-started.md) .  Выполнение данной задачи гарантирует, что будут соблюдены все предварительные требования для разработки мобильных приложений Azure.  Кроме того, этот краткий учебник поможет вам настроить учетную запись и создать свою первую серверную часть мобильного приложения.

Если вы решите не изучать краткий учебник, то выполните следующие задачи.

* [Создайте серверную часть мобильного приложения][13] для использования с приложением Android.
* В Android Studio [обновите файлы сборки Gradle](#gradle-build).
* [Включение разрешение INTERNET](#enable-internet).

### <a name="gradle-build"></a>Обновление файла сборки Gradle

Измените оба файла **build.gradle** :

1. Добавьте следующий код в файл *build.gradle* уровня **Project** внутри тега *buildscript*.

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Добавьте следующий код в файл *build.gradle* уровня **Module app** внутри тега *dependencies*.

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Сейчас последней версией является 3.4.0. Поддерживаемые версии см. на сайте [Bintray][14].

### <a name="enable-internet"></a>Включение разрешения INTERNET

Для доступа к Azure вам нужно включить для приложения разрешение INTERNET. Если оно не включено, добавьте следующую строку кода в файл **AndroidManifest.xml** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Создание подключения клиента

Служба мобильных приложений Azure предоставляет четыре функции для мобильных приложений:

* доступ к данным и их синхронизация в автономном режиме со службой мобильных приложений Azure;
* вызов настраиваемых API-интерфейсов, написанных с использованием пакета SDK для сервера мобильных приложений Azure;
* проверка подлинности с помощью функции проверки подлинности и авторизации в службе приложений Azure;
* регистрация push-уведомлений в Центрах уведомлений.

Чтобы использовать каждую из этих функций, сначала необходимо создать объект `MobileServiceClient`.  В мобильном клиенте необходимо создать только один объект `MobileServiceClient` (то есть следует использовать шаблон с одним элементом).  Чтобы создать объект `MobileServiceClient`, используйте следующий код:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

Параметр `<MobileAppUrl>` — это строка или URL-адрес, указывающий на серверную часть мобильного приложения.  Если серверная часть мобильного приложения расположена в службе приложений Azure, используйте безопасную версию `https://` URL-адреса.

Клиенту требуется доступ к классу Activity или Context. В приведенном примере кода за это отвечает параметр `this`.  Создание объекта MobileServiceClient должно начаться в методе `onCreate()` класса Activity, указанного в файле `AndroidManifest.xml`.

Мы советуем абстрагировать связь сервера в собственный класс (шаблон с одним элементом).  В этом случае следует передавать класс Activity в конструктор, чтобы соответствующим образом настроить службу.  Например: 

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Теперь вы можете вызвать `AzureServiceAdapter.Initialize(this);` в методе `onCreate()` основного действия.  Другие методы, которым нужен доступ к клиенту, используют `AzureServiceAdapter.getInstance();`, чтобы получить ссылку на адаптер службы.

## <a name="data-operations"></a>Операции с данными

Ядро пакета SDK для мобильных приложений Azure предоставляет доступ к данным, сохраненным в SQL Azure в серверной части мобильного приложения.  Доступ к этим данным можно получить, используя классы со строгим типом (предпочтительный способ) или нетипизированные запросы (нерекомендуемый способ).  Основная часть этого раздела посвящена использованию классов со строгим типом.

### <a name="define-client-data-classes"></a>Определение клиентских классов данных

Для доступа к данным из таблиц SQL Azure вам нужно определить клиентские классы данных, которые соответствуют таблицам в серверной части мобильного приложения. В примерах этого раздела используется таблица с именем **MyDataTable**, которая содержит следующие столбцы:

* id
* текст
* complete

Соответствующий типизированный клиентский объект находится в файле **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Включите методы Getter или Setter в каждое добавленное поле.  Если таблица SQL Azure содержит больше столбцов, в этот класс необходимо добавить соответствующие поля.  Например, если объект переноса данных (DTO) содержал целочисленный столбец Priority, то вы можете добавить это поле вместе с методами получения и задания.

```java
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
```

См. дополнительные сведения о создании дополнительных таблиц в серверной части мобильных приложений, включая сведения об [определении контроллера таблиц][15] (серверная часть .NET) и [определении таблиц с помощью динамической схемы][16] (серверная часть Node.js).

В таблице серверной части мобильных приложений Azure определено пять специальных полей, четыре из которых доступны клиентам:

* `String id` — глобальный уникальный идентификатор записи.  Мы советуем сделать этот идентификатор представлением строки объекта [UUID][17].
* `DateTimeOffset updatedAt` — дата и время последнего обновления.  Поле updatedAt устанавливается на сервере и никогда не должно задаваться в коде клиента.
* `DateTimeOffset createdAt` — дата и время создания объекта.  Поле createdAt устанавливается на сервере и никогда не должно задаваться в коде клиента.
* `byte[] version` — обычно это поле представлено в виде строки. Версия также устанавливается на сервере.
* `boolean deleted` — указывает, что запись была удалена, но еще не очищена.  Не используйте `deleted` в качестве свойства класса.

Поле `id` является обязательным.  Поля `updatedAt` и `version` используются при синхронизации в автономном режиме (для добавочной синхронизации и устранения конфликтов соответственно).  `createdAt` — это эталонное поле. Оно не используется клиентом.  Имена свойств можно передавать по сети. Они не изменяются.  Но вы можете создать сопоставление между своим объектом и этими именами, используя библиотеку [gson][3].  Например: 

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected DateTimeOffset setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Создание ссылки на таблицу

Для доступа к таблице сначала создайте объект [MobileServiceTable][8], вызвав метод **getTable** объекта [MobileServiceClient][9].  У этого метода две перегрузки.

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

В следующем коде **mClient** — это ссылка на объект MobileServiceClient.  Первая перегрузка используется при быстром запуске, когда имя класса и имя таблицы одинаковы.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Вторая перегрузка используется, когда имя таблицы отличается от имени типа (первый параметр — это имя таблицы).

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Запрос таблицы серверной части

Сначала получите ссылку на таблицу.  Затем выполните запрос к ссылке на таблицу.  Запрос сочетает в себе следующее:

* [предложение фильтра](#filtering) `.where()`;
* [предложение упорядочения](#sorting) `.orderBy()`;
* [предложение выбора поля](#selection) `.select()`;
* `.skip()` и `.top()` для [страничных результатов](#paging).

Предложения должны располагаться в таком же порядке.

### <a name="filter"></a> Фильтрация результатов

Запрос выглядит следующим образом:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Приведенный выше пример возвращает все результаты (вплоть до максимального размера страницы, заданного на сервере).  Метод `.execute()` выполняет запрос в серверной части.  Перед передачей в серверную часть мобильных приложений этот запрос преобразовывается в запрос [OData v3][19].  После получения этот запрос преобразовывается в серверной части в инструкцию SQL перед выполнением его на экземпляре SQL Azure.  Так как сетевая активность занимает некоторое время, метод `.execute()` возвращает [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Фильтрация возвращаемых данных

Следующий запрос при выполнении возвращает все элементы из таблицы **ToDoItem**, где для переменной **complete** задано значение **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** — это ссылка на таблицу мобильной службы, созданную ранее.

Для определения фильтра вызовите метод **where** для ссылки на таблицу. После метода **where** вызывается метод **field**, а затем — метод, который определяет логический предикат. Возможные методы предиката: **eq** (равно), **ne** (не равно), **gt** (больше), **ge** (больше или равно), **lt** (меньше), **le** (меньше или равно). Эти методы позволяют сравнивать числовые и строковые поля с указанными значениями.

Фильтровать данные можно по датам. Следующие методы позволяют сравнить поле даты целиком или частично: **year**, **month**, **day**, **hour**, **minute** и **second**. В следующем примере добавляется фильтр для элементов, *дата выполнения* которых равна 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Следующие методы поддерживают применение сложных фильтров к строковым полям: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** и **length**. В следующем примере отфильтровываются строки таблицы, в которых столбец *text* начинается с PRI0.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Следующие методы оператора поддерживают использование числовых полей: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** и **round**. В следующем примере отфильтровываются строки таблицы, в которых значение **duration** является четным числом.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Предикаты можно объединять с помощью логических методов **and**, **or** и **not**. В следующем примере объединяются два приведенных выше примера.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Группирование и вложение логических операторов.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Более подробное описание и примеры фильтрации см. в статье [Exploring the richness of the Mobile Services Android client query model][20] (Исследование возможностей модели запросов клиента мобильных служб Android).

### <a name="sorting"></a>Сортировка возвращаемых данных

Следующий код возвращает все элементы из таблицы **ToDoItem** , упорядоченные по полю *text* . *mToDoTable* — это ссылка на таблицу серверной части, созданную ранее:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Первый параметр метода **orderBy** — это строка, совпадающая с именем поля, по которому выполняется сортировка. Второй параметр использует перечисление **QueryOrder** , чтобы определить порядок сортировки (по возрастанию или убыванию).  Если используется фильтрация с помощью метода ***where***, то метод ***where*** необходимо вызывать до метода ***orderBy***.

### <a name="selection"></a>Выбор определенных столбцов

В следующем фрагменте кода показано, как получить все элементы из таблицы **ToDoItems**, в которой отображаются только поля **complete** и **text**. **mToDoTable** — это ссылка на таблицу серверной части, созданную ранее:

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Параметры функции select являются строковыми именами столбцов таблицы, которые требуется вернуть.  Метод **select** должен следовать за такими методами, как **where** и **orderBy**. Затем можно вызывать такие методы разбивки на страницы, как **skip** и **top**.

### <a name="paging"></a>Возврат данных на страницах

Данные **всегда** возвращаются на страницах.  Максимальное число возвращаемых записей устанавливается на сервере.  Если клиент запрашивает большее число записей, сервер возвращает только установленное максимальное количество записей.  По умолчанию максимальный размер страницы на сервере составляет 50 записей.

Первый пример показывает, как выбрать пять верхних элементов из таблицы. Следующий запрос возвращает элементы из таблицы **ToDoItem**. **mToDoTable** — это ссылка на таблицу серверной части, созданную ранее:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Приведенный ниже запрос пропускает первые пять элементов, возвращая следующие пять элементов.

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Если вы хотите получить все записи в таблице, реализуйте код для выполнения итерации по всех страницах:

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

При выполнении запроса всех записей с помощью этого метода создается как минимум два запроса к серверной части мобильных приложений.

> [!TIP]
> Выбор правильного размера страницы — это соотношение между использованием памяти во время выполнения запроса, потреблением пропускной способности и задержкой получения всех данных.  Значение по умолчанию (50 записей) подходит для всех устройств.  Если вы работаете на устройствах с большим объемом памяти, увеличьте это значение до 500.  Мы пришли к выводу, что при увеличении размера страницы до более 500 записей возникают недопустимые задержки и большие проблемы с памятью.

### <a name="chaining"></a>Практическое руководство. Объединение методов запросов

Методы, используемые в запросах таблиц серверной части, можно сцепить. Цепочка методов запроса позволяет, например, выбирать определенные столбцы отфильтрованных строк, которые сортируются и разбиваются на страницы. Можно создавать сложные логические фильтры.  Каждый метод запроса возвращает объект Query. Чтобы завершить серию методов и фактически выполнить запрос, вызовите метод **execute** . Например: 

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Методы запроса в цепочке должны быть упорядочены следующим образом:

1. методы фильтрации (**где**);
2. методы сортировки (**orderBy**);
3. методы выборки (**выберите**);
4. методы разбиения по страницам (**skip** и **top**).

## <a name="binding"></a>Привязка данных к пользовательскому интерфейсу

Привязка данных состоит из трех компонентов:

* источник данных;
* макет экрана;
* адаптер, который связывает два эти компонента.

В нашем примере кода мы возвращаем данные из таблицы **ToDoItem** SQL Azure мобильных приложений в массив. Это действие типично для приложений для обработки данных.  Запросы к базе данных часто возвращают набор строк, которые клиент получает в виде списка или массива. В этом примере массив является источником данных.  Код указывает макет экрана, который определяет представление данных, отображаемых на устройстве.  Два компонента соединяются адаптером, который в этом коде является расширением класса **ArrayAdapter&lt;ToDoItem&gt;**.

#### <a name="layout"></a>Определение макета

Макет определяется несколькими фрагментами XML-кода. С учетом существующего макета приведенный ниже код представляет объект **ListView** , который требуется заполнить данными с сервера.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

В приведенном выше коде атрибут *listitem* указывает идентификатор макета для отдельной строки в списке. Этот код задает флажок и связанный с ним текст, при этом экземпляры создаются для каждого элемента в списке. В этом макете поле **id** не отображается. В более сложном макете указываются дополнительные поля на экране. Этот код находится в файле **row_list_to_do.xml**.

```java
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
```

#### <a name="adapter"></a>Определение адаптера
Источник данных нашего представления — это массив объектов **ToDoItem**, поэтому мы создадим адаптер как подкласс **ArrayAdapter&lt;ToDoItem&gt;**. Этот подкласс создаст представление для каждого объекта **ToDoItem** с помощью макета **row_list_to_do**.  В коде мы определили следующий класс, который представляет собой расширение класса **ArrayAdapter&lt;E&gt;**:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Переопределите метод **getView** адаптера. Например: 

```
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
```

Мы создаем экземпляр этого класса в действии следующим образом:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Второй параметр конструктора ToDoItemAdapter — это ссылка на макет. Теперь можно создать экземпляр **ListView** и назначить адаптер в **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Привязка данных к пользовательскому интерфейсу с помощью адаптера

Теперь вы можете использовать привязку данных. Ниже приведен код, в котором показано, как получить элементы из таблицы и заполнить локальный адаптер возвращенными элементами.

```java
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
```

Адаптер вызывается при каждом изменении таблицы **ToDoItem** . Так как изменения выполняются на уровне отдельных записей, обрабатываться будут отдельные строки, а не коллекция. При вставке элемента вызывается метод **add** адаптера, а при удалении — метод **remove**.

Полный пример можно найти в [проекте быстрого запуска для Android][21].

## <a name="inserting"></a>Вставка данных в серверную часть

Создайте экземпляр класса *ToDoItem* и задайте его свойства.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Затем с помощью **insert()** вставьте объект.

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Возвращаемая сущность соответствует данным, вставленным в таблицу серверной части, в том числе идентификатору и другим значениям (например, поля `createdAt`, `updatedAt` и `version`), заданным в серверной части.

Для таблиц мобильных приложений требуется столбец первичного ключа **id**. Этот столбец должен быть строкой. По умолчанию столбец идентификаторов содержит значения GUID.  Можно указать другие уникальные значения, в том числе электронные адреса или имена пользователей. Если строковое значение идентификатора для вставленной записи не предусмотрено, то серверная часть создает новое значение GUID.

Использование строковых значений идентификаторов связано с такими преимуществами.

* Идентификаторы можно создавать без обмена данными с базой данных.
* Можно легко объединять записи из разных таблиц или баз данных.
* Значения идентификаторов удобнее интегрировать с логикой приложения.

Строковые значения идентификатора **НЕОБХОДИМЫ** для поддержки автономной синхронизации.  После сохранения идентификатора в серверной базе данных его изменить невозможно.

## <a name="updating"></a>Обновление данных в мобильном приложении

Чтобы обновить данные в таблице, передайте новый объект в метод **update()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

В этом примере *item* — это ссылка на строку в таблице *ToDoItem*, в которую были внесены изменения.  Строка с таким же значением **id** обновится.

## <a name="deleting"></a>Удаление данных в мобильном приложении

В следующем коде показано, как удалить данные из таблицы, указав объект данных.

```java
mToDoTable
    .delete(item);
```

Удалить элемент также можно, указав поле **id** удаляемой строки.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Поиск определенного элемента по идентификатору

Найдите элемент с определенным значением в поле **id** с помощью метода **lookUp()**.

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Практическое руководство. Работа с нетипизированными данными

Нетипизированная модель программирования предоставляет точный контроль над сериализацией JSON.  Существует несколько распространенных сценариев, в которых может потребоваться использовать нетипизированную модель программирования. Например, если таблица серверной части содержит много столбцов, и вам нужно указать ссылку только на их подмножество.  Для использования типизированной модели необходимо определить все столбцы, заданные в серверной части мобильных приложений в классе данных.  Большая часть вызовов интерфейса API для доступа к данным аналогична вызовам в типизированной модели. Основное различие заключается в том, что в нетипизированной модели методы вызываются для объекта **MobileServiceJsonTable**, а не **MobileServiceTable**.

### <a name="json_instance"></a>Создание экземпляра нетипизированной таблицы

Аналогично типизированной модели сначала вы получаете ссылку на таблицу, но в данном случае это объект **MobileServicesJsonTable** . Чтобы получить ссылку, вызовите метод **getTable** для экземпляра клиента.

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

После создания экземпляр **MobileServiceJsonTable**обладает практически таким же API, что и в типизированной модели программирования. В некоторых случаях методы принимают нетипизированный параметр вместо типизированного.

### <a name="json_insert"></a>Вставка данных в нетипизированную таблицу
В следующем коде показано, как вставить данные. Первый шаг — создание объекта [JsonObject][1], который является частью библиотеки [gson][3].

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Затем с помощью **insert()** вставьте нетипизированный объект в таблицу.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Если необходимо получить идентификатор вставленного объекта, используйте метод **getAsJsonPrimitive()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Удаление данных из нетипизированной таблицы
В следующем коде показано, как удалить экземпляр, в этом случае тот же экземпляр объекта **JsonObject** , который был создан в предыдущем примере *вставки* . Код будет таким же, как и в случае типизированной таблицы, но метод будет иметь другую сигнатуру, так как он ссылается на **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Вы также можете удалить экземпляр напрямую с помощью идентификатора:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Получение всех строк из нетипизированной таблицы
В следующем коде показано, как получить всю таблицу. Поскольку вы используете таблицы Json, можно выборочно вывести лишь некоторые столбцы таблицы.

```java
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
```

Для нетипизированной модели доступен тот же набор средств фильтрации, то есть методы фильтрации и разбиения по страницам, что и для типизированной модели.

## <a name="offline-sync"></a>Реализация синхронизации в автономном режиме

Пакет SDK для клиента мобильных приложений Azure также реализует синхронизацию данных в автономном режиме. Это стало возможным благодаря хранению копии данных сервера локально в базе данных SQLite.  Операции в автономной таблице выполняются без подключения к мобильному приложению.  Синхронизация данных в автономном режиме повышает устойчивость и производительность за счет более сложной логики устранения конфликтов.  Пакет SDK для клиента мобильных приложений Azure реализует следующие возможности:

* Добавочная синхронизация. Загружаются только обновленные и новые записи. Это позволяет уменьшить потребление пропускной способности и памяти.
* Оптимистичный параллелизм. Операции рассматриваются как успешные.  Устранение конфликтов откладывается до выполнения обновлений на сервере.
* Устранение конфликтов. Пакет SDK обнаруживает время внесения конфликтного изменения на сервере и предоставляет обработчики для отправки уведомлений пользователю.
* Обратимое удаление. Удаленные записи помечаются как удаленные, что позволяет другим устройствам обновить их автономный кэш.

### <a name="initialize-offline-sync"></a>Инициализация синхронизации в автономном режиме

Перед использованием каждую автономную таблицу необходимо определить в автономном кэше.  Как правило, определение таблицы выполняется сразу после создания клиента:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Получение ссылки на автономную таблицу кэша

Для таблицы в Интернете используйте `.getTable()`,  а для автономной таблицы — `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Все методы, доступные для таблиц в Интернете (в том числе методы фильтрации, разбиения по страницам, сортировки, вставки, обновления и удаления данных), работают одинаково хорошо и с автономными таблицами.

### <a name="synchronize-the-local-offline-cache"></a>Синхронизация локального автономного кэша

Синхронизация выполняется под управлением приложения.  Ниже приведен пример метода синхронизации.

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Если в метод `.pull(query, queryname)` добавить имя запроса, тогда выполняется добавочная синхронизация и возвращаются только записи, созданные или измененные с момента последнего успешного запроса.

### <a name="handle-conflicts-during-offline-synchronization"></a>Обработка конфликтов во время синхронизации в автономном режиме

Если конфликт происходит во время выполнения операции `.push()`, возникает исключение `MobileServiceConflictException`.   В это исключение внедрен выданный сервером элемент, который можно извлечь, добавив в исключение метод `.getItem()`.  Настройте принудительную отправку, добавив в объект MobileServiceSyncContext следующие элементы:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Когда все конфликты будут помечены должным образом, вызовите метод `.push()`, чтобы устранить их.

## <a name="custom-api"></a>Вызов настраиваемого интерфейса API

Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Вызовите из клиента Android метод **invokeApi** для вызова конечной точки настраиваемого API. В следующем примере показано, как вызвать конечную точку API с именем **completeAll**, которая возвращает класс коллекции с именем **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
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
```

Метод **invokeApi** вызывается на стороне клиента, что приводит к отправке запроса POST новому настраиваемому API. Результат, возвращаемый настраиваемым интерфейсом API, отображается в диалоговом окне сообщения, как и любые ошибки. Другие версии метода **invokeApi** позволяют при необходимости отправить объект в тексте запроса, указать метод HTTP и отправить параметры запроса вместе с запросом. Кроме того, доступны нетипизированные версии **invokeApi** .

## <a name="authentication"></a>Добавление проверки подлинности в приложение

Процесс добавления этих компонентов подробно описан в руководствах.

Служба приложений поддерживает [проверку подлинности пользователей приложения](app-service-mobile-android-get-started-users.md) с помощью разных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверной части.

Поддерживаются два потока аутентификации: **серверный** и **клиентский**. Серверный поток обеспечивает самый простой способ аутентификации, так как он использует веб-интерфейс поставщика удостоверений.  Для реализации аутентификации серверного потока не нужны дополнительные пакеты SDK. Аутентификация серверного потока не обеспечивает тесную интеграцию с мобильным устройством и рекомендуется только для подтверждения концепции.

Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK, предоставляемые конкретным поставщиком удостоверений.  Например, можно интегрировать пакет SDK для Facebook в мобильное приложение.  Мобильный клиент переключается на приложение Facebook и подтверждает ваш вход, прежде чем переключиться обратно на мобильное приложение.

Чтобы включить аутентификацию в приложении, необходимо выполнить четыре действия:

* Регистрация приложения для аутентификации в поставщике удостоверений.
* Настройка серверной части службы приложений.
* Ограничение разрешений таблицы только аутентифицированными пользователями.
* Добавление кода проверки подлинности в приложение.

Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Также можно использовать идентификатор безопасности пользователя, прошедшего проверку, чтобы изменять запросы.  Дополнительную информацию см. в разделе [Приступая к работе с проверкой подлинности] и справочной документации по пакету SDK для сервера.

### <a name="caching"></a>Серверный поток проверки подлинности

Следующий код запускает процесс входа серверного потока с помощью поставщика Google.  Из-за требований к безопасности поставщика Google необходимо выполнить дополнительную конфигурацию:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Кроме того, добавьте в основной класс Activity следующий метод:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

Параметр `GOOGLE_LOGIN_REQUEST_CODE`, определенный в основном классе Activity, используется в методе `login()` и `onActivityResult()`.  Вы можете выбрать любое уникальное число, если оно будет одинаковым в методе `login()` и `onActivityResult()`.  Если вы абстрагировали код клиента в адаптер службы (как показано выше), необходимо вызвать соответствующие методы этого адаптера.

Кроме того, необходимо также настроить проект настраиваемых вкладок.  Сначала укажите URL-адрес перенаправления.  Добавьте в файл `AndroidManifest.xml` следующий фрагмент кода:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Добавьте параметр **redirectUriScheme** в файл `build.gradle` приложения:

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Наконец, добавьте `com.android.support:customtabs:23.0.1` в список зависимостей в файле `build.gradle`:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Получите идентификатор вошедшего в систему пользователя из **MobileServiceUser** с помощью метода **getUserId**. Пример использования Futures для вызова API асинхронного входа см. в статье [Приступая к работе с проверкой подлинности].

> [!WARNING]
> В упомянутой схеме URL-адресов учитывается регистр.  Убедитесь, что во всех вхождениях `{url_scheme_of_you_app}` используется один и тот же регистр.

### <a name="caching"></a>Кэширование токенов проверки подлинности

Для этого необходимо сохранить идентификатор пользователя и маркер аутентификации локально на устройстве. При следующем запуске приложения проверьте кэш — если эти значения существуют, то можно пропустить процедуру входа и повторно заполнить клиент этими данными. Однако это конфиденциальные данные и они должны храниться в зашифрованном виде, чтобы обеспечить безопасность в случае кражи телефона.  См. полный пример [кэширования маркеров аутентификации][7].

При попытке использовать маркер с истекшим сроком действия вы получите ответ *401 не санкционировано* . Ошибки аутентификации можно обрабатывать с помощью фильтров.  Фильтры перехватывают запросы к серверной части службы приложений. Код фильтра проверяет наличие ответа 401, инициирует вход в систему и возобновляет запрос, породивший ответ 401.

### <a name="refresh"></a>Использование токенов обновления

Время существования токена, возвращенного функцией проверки подлинности и авторизации в службе приложений Azure, составляет один час.  По истечении этого периода необходимо повторно выполнить проверку подлинности пользователя.  При использовании токена с долгим временем существования, полученного в клиентском потоке проверки подлинности, это можно сделать с помощью функции проверки подлинности и авторизации в службе приложений Azure, используя этот же токен.  В процессе создается другой токен службы приложений Azure с новым временем существования.

Вы также можете зарегистрировать поставщик для использования токенов обновления.  Токен обновления не всегда доступен.  В этом случае необходимо выполнить дополнительную настройку:

* Для приложения **Azure Active Directory** настройте секрет клиента.  Укажите секрет клиента в службе приложений Azure при настройке проверки подлинности Azure Active Directory.  При вызове метода `.login()` передайте `response_type=code id_token` как параметр:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Для **Google** передайте `access_type=offline` как параметр:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Для **учетной записи Майкрософт** выберите область `wl.offline_access`.

Чтобы обновить токен, вызовите метод `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Мы советуем создать фильтр, который определяет ответ 401 от сервера и пытается обновить токен пользователя.

## <a name="log-in-with-client-flow-authentication"></a>Вход в систему с использованием клиентского потока проверки подлинности

Общая процедура входа в систему с использованием клиентского потока проверки подлинности состоит из следующих этапов:

* Настройка функции проверки подлинности и авторизации в службе приложений Azure, как и при серверном потоке проверки подлинности.
* Интеграция пакета SDK поставщика проверки подлинности для создания маркера доступа.
* Вызов метода `.login()` следующим образом:

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
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
    ```

Замените метод `onSuccess()` на любой код, который следует использовать для успешного входа.  В строке `{provider}` необходимо указать допустимое значение поставщика, например **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount** или **twitter**.  При реализации пользовательской проверки подлинности вы также можете использовать тег поставщика проверки подлинности.

### <a name="adal"></a>Проверка подлинности пользователей с помощью библиотеки проверки подлинности Active Directory (ADAL)

Библиотеку проверки подлинности Active Directory (ADAL) можно использовать для входа пользователей в приложение с помощью Azure Active Directory. Использование входа посредством клиентского потока является более предпочтительным, чем использование методов `loginAsync()` , так как он обеспечивает более естественный интерфейс входа для пользователя и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью AAD, следуя указаниям в руководстве [Настройка приложения службы приложений для использования службы входа Azure Active Directory][22]. Обязательно выполните дополнительный этап регистрации собственного клиентского приложения.
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

1. Добавьте указанный ниже код в приложение, выполнив следующие замены.

* Замените строку **INSERT-AUTHORITY-HERE** именем клиента, в котором подготовлено приложение. Его необходимо указать в формате https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Замените текст **INSERT-RESOURCE-ID-HERE** идентификатором клиента для серверной части мобильного приложения. Идентификатор клиента можно скопировать на портале в разделе **Настройки Azure Active Directory** на вкладке **Дополнительно**.
* Замените текст **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.
* Замените текст **INSERT-REDIRECT-URI-HERE** конечной точкой сайта */.auth/login/done* , используя схему HTTPS. Это значение должно быть аналогично *https://contoso.azurewebsites.net/.auth/login/done*.

```java
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
```

## <a name="filters"></a>Настройка связи между клиентом и сервером

Как правило, клиент использует HTTP-подключение на основе базовой библиотеки HTTP, входящей в пакет SDK для Android.  Используя другой тип подключения, вы сможете:

* использовать альтернативную библиотеку HTTP для настройки времени ожидания;
* предоставить индикатор выполнения;
* добавить пользовательский заголовок для поддержки функции управления API;
* настроить перехват неудачных ответов, что позволит повторно выполнить проверку подлинности;
* записывать серверные запросы в службу аналитики.

### <a name="using-an-alternate-http-library"></a>Использование альтернативной библиотеки HTTP

После создания ссылки на клиент вызовите метод `.setAndroidHttpClientFactory()`.  Это, например, позволит установить время ожидания подключения в 60 секунд (по умолчанию 10 секунд).

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Реализация фильтра хода выполнения

Реализовав атрибут `ServiceFilter`, вы сможете настроить перехват каждого запроса.  Например, следующий код позволяет обновить стандартный индикатор выполнения:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    pubic void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Этот фильтр можно присоединить к клиенту следующим образом:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Настройка заголовков запросов

Используйте атрибут `ServiceFilter` и присоедините фильтр точно так же, как и атрибут `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Настройка автоматической сериализации

Можно указать стратегию преобразования, которая применяется к каждому столбцу, с помощью API из библиотеки [gson][3]. Клиентская библиотека Android использует [gson][3] для сериализации объектов Java в данные JSON перед отправкой данных в службу приложений Azure.  В следующем коде используется метод **setFieldNamingStrategy()** для задания стратегии. Этот пример удалит начальный знак ("m"), а затем преобразует следующий знак в нижний регистр для каждого имени поля. Например, он преобразует "mId" в "id".  Реализуйте стратегию преобразования, чтобы снизить потребность в аннотациях `SerializedName()` в большинстве полей.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Этот код необходимо выполнить перед созданием ссылки на мобильный клиент с помощью **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
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
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
