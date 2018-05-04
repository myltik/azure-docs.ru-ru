---
title: Работа с управляемой клиентской библиотекой мобильных приложений службы приложений (Windows) | Документация Майкрософт
description: Узнайте, как использовать клиент .NET для мобильных приложений службы приложений Azure с приложениями Windows и Xamarin.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2017
ms.author: crdun
ms.openlocfilehash: 2b7620611c4587d00612f98c86b5792984ff548b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Использование управляемого клиента для мобильных приложений Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием управляемой клиентской библиотеки для мобильных приложений службы приложений Azure в приложениях Windows и Xamarin. Если вы не знакомы с мобильными приложениями, рекомендуем сначала прочитать [краткое руководство по мобильным приложениям Azure][1]. В данном руководстве мы сосредоточимся на управляемом пакете SDK клиентской части. Дополнительные сведения о серверных пакетах SDK для мобильных приложений см. в документации по [серверному пакету SDK для .NET][2] или [серверному пакету SDK для Node.js][3].

## <a name="reference-documentation"></a>Справочная документация
Справочная документация по клиентскому пакету SDK находится в [справочнике по клиенту мобильных приложений Azure для .NET][4].
Несколько примеров клиентов доступно в [репозитории GitHub с примерами Azure][5].

## <a name="supported-platforms"></a>Поддерживаемые платформы
Платформа .NET поддерживает следующие платформы:

* выпуски Xamarin Android для API 19–24 (от KitKat до Nougat);
* выпуски Xamarin iOS для iOS 8.0 и более поздних версий;
* Универсальные приложения Windows
* Windows Phone 8.1
* Windows Phone 8.0, за исключением приложений Silverlight.

"Серверная" аутентификация использует WebView для представляемого пользовательского интерфейса.  Если устройство не может представить пользовательский интерфейс WebView, требуется применять другие способы аутентификации.  Поэтому данный пакет SDK не подходит для различного рода часов и других устройств с аналогичными ограничениями.

## <a name="setup"></a>Настройка и необходимые компоненты
Предполагается, что вы уже создали и опубликовали проект внутреннего сервера мобильных приложений, который содержит по меньшей мере одну таблицу.  В коде, который используется в этом разделе, применяется таблица `TodoItem`, которая содержит следующие столбцы: `Id`, `Text` и `Complete`. Это та же таблица, которая была создана при выполнении [краткого руководства по мобильным приложениям Azure][1].

Соответствующий типизированный тип на стороне клиента является приведенным ниже классом в C#.

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

Атрибут [JsonPropertyAttribute][6] используется для определения сопоставления *PropertyName* между полем клиента и таблицы.

Дополнительные сведения о создании таблиц в серверной части мобильных приложений см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure][7] или [Использование пакета SDK Node.js для мобильных приложений Azure][8]. При создании серверной части мобильного приложения на портале Azure с помощью краткого руководства можно использовать параметр **Простые таблицы** на [портале Azure].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Практическое руководство. Установка пакета SDK для управляемого клиента
Используйте один из следующих методов установки пакета SDK для управляемого клиента для мобильных приложений с сайта [NuGet][9].

* **Visual Studio**. Щелкните правой кнопкой мыши свой проект, выберите пункт **Управление пакетами NuGet**, найдите пакет `Microsoft.Azure.Mobile.Client` и нажмите кнопку **Установить**.
* **Xamarin Studio**. Щелкните правой кнопкой мыши проект, выберите пункт **Добавить** > **Добавить пакеты NuGet**, найдите пакет `Microsoft.Azure.Mobile.Client ` и нажмите кнопку **Добавить пакет**.

В своем основном файле действий не забудьте добавить следующий оператор **using** .

```
using Microsoft.WindowsAzure.MobileServices;
```

### <a name="symbolsource"></a>Практическое руководство. Работа с отладочными символами в Visual Studio
Символы для пространства имен Microsoft.Azure.Mobile доступны в [SymbolSource][10].  Ознакомьтесь с [инструкциями по SymbolSource][11], чтобы интегрировать SymbolSource с Visual Studio.

## <a name="create-client"></a>Создание клиента мобильных приложений
Следующий код создает объект [MobileServiceClient][12], используемый для доступа к серверной части мобильного приложения.

```
var client = new MobileServiceClient("MOBILE_APP_URL");
```

В приведенном выше коде замените `MOBILE_APP_URL` URL-адресом серверной части мобильного приложения, который можно найти в колонке серверной части мобильного приложения на [портале Azure]. Объект MobileServiceClient должен быть одноэлементным.

## <a name="work-with-tables"></a>Работа с таблицами
Ниже подробно описано, как выполнить поиск, извлечение записей и изменение данных в таблице.  В этой статье рассматриваются следующие темы:

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
* [Изменение размера страницы](#pagesize)

### <a name="instantiating"></a>Практическое руководство. Создание ссылки на таблицу
Весь код, который открывает или изменяет данные в таблице серверной части, вызывает функции для объекта `MobileServiceTable` . Получите ссылку на таблицу, вызвав метод [GetTable] , как показано ниже.

```
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Возвращаемый объект использует типизированную модель сериализации. Также поддерживается нетипизированная модель сериализации. Следующий пример [создает ссылку на нетипизированную таблицу].

```
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

В нетипизированных запросах необходимо указать соответствующую строку запроса OData.

### <a name="querying"></a>Практическое руководство. Запрос данных из мобильного приложения
В этом разделе показано, как отправлять запросы к внутреннему серверу мобильных приложений. Описаны следующие функциональные возможности:

* [Фильтрация возвращаемых данных](#filtering)
* [Сортировка возвращаемых данных](#sorting)
* [Возврат данных на страницах](#paging)
* [Выбор определенных столбцов](#selecting)
* [Поиск данных по идентификатору](#lookingup)

> [!NOTE]
> Для предотвращения возврата всех строк принудительно применяется размер страницы, управляемый сервером.  Разбиение по страницам предотвращает негативное воздействие больших наборов данных на функционирование службы.  Для возвращения более 50 строк используйте методы `Skip` и `Take`, как описано в разделе [Возвращение данных на страницах](#paging).

### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных
Следующий код иллюстрирует способ фильтрации данных путем включения предложения `Where` в запрос. Он возвращает все элементы таблицы `todoTable`, свойство `Complete` которых равно `false`. Функция [Where] применяет предикат фильтрации строк для запросов к таблице.

```
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Универсальный код ресурса (URI) запроса, отправленного в серверную часть, можно просмотреть с помощью программы проверки сообщений, такой как средства разработчика браузера или [Fiddler]. Если взглянуть на универсальный код ресурса (URI) запроса, можно отметить, что сама строка запроса изменена.

```
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Этот запрос OData с помощью пакета SDK для сервера преобразуется в SQL-запрос.

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Функция, которая передается в метод `Where` , может включать в себя произвольное число условий.

```
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Этот пример должен быть преобразован в SQL-запрос с помощью пакета SDK для сервера.

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Этот запрос также можно разбить на несколько предложений:

```
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Эти два способа эквивалентны и могут быть взаимозаменяемыми.  Первый вариант — &mdash;объединение нескольких предикатов в одном запросе&mdash; — является более компактным и рекомендуемым.

Предложение `Where` поддерживает операции, которые будут преобразованы в подмножество OData. К этим операциям относятся:

* операторы сравнения (==,! =, <, < =, >, > =);
* арифметические операторы (+, -, /, *, %);
* указание точности чисел (Math.Floor, Math.Ceiling);
* строковые функции (Length, Substring, Replace, IndexOf, StartsWith, EndsWith);
* свойства даты (Year, Month, Day, Hour, Minute, Second);
* свойства доступа к объекту;
* выражения, сочетающие в себе любые из этих операций.

Чтобы узнать, что поддерживает пакет SDK для сервера, обратитесь к [документации по OData версии 3].

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных
В следующем коде показано, как сортировать данные, включая в запрос функцию [OrderBy] или [OrderByDescending]. Он возвращает элементы таблицы `todoTable`, упорядочивая их по возрастанию значений в поле `Text`.

```
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах
По умолчанию внутренний сервер возвращает только первые 50 строк. Число возвращенных строк можно увеличить путем вызова метода [Take] . Чтобы запросить определенную "страницу" общего набора данных, возвращенного запросом, используйте метод `Take` вместе с методом [Skip] . При выполнении следующего запроса будут возвращены три главных элемента в таблице.

```
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Следующий измененный запрос пропускает первые три результата и возвращает следующие три. Данный запрос фактически выдает вторую "страницу" данных, где размер страницы составляет три элемента.

```
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Метод [IncludeTotalCount] запрашивает общее количество *всех* записей, которые должны быть возвращены, без учета указанных предложений paging/limit.

```
query = query.IncludeTotalCount();
```

В реальных приложениях можно использовать запросы, подобные вышеуказанным, с постраничным навигатором или другим совместимым пользовательским интерфейсом, позволяющим переходить между страницами.

> [!NOTE]
> Чтобы переопределить ограничение серверной части мобильного приложения в 50 строк, необходимо также применить класс [EnableQueryAttribute] к общедоступному методу GET и настроить разбиение по страницам. В этом случае максимальное количество возвращаемых строк увеличивается до 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов
Набор свойств, который войдет в результаты, можно задать, добавив в запрос предложение [Select] . Например, в следующем коде показано, как выбрать только одно поле, а также способы выбора и форматирования нескольких полей:

```
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
```

Все описанные функции являются аддитивными, поэтому мы можем создавать из них цепочки. Каждый последующий цепной вызов все больше влияет на запрос. Еще один пример:

```
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Практическое руководство. Поиск данных по идентификатору
Функцию [LookupAsync] можно использовать для поиска в базе данных объектов с определенным идентификатором.

```
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Практическое руководство. Выполнение нетипизированных запросов
При выполнении запроса с помощью объекта нетипизированной таблицы необходимо явно указать строку запроса OData, вызвав функцию [ReadAsync], как показано в следующем примере.

```
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Вы будете получать значения JSON, которые можно использовать в качестве контейнера свойств. Более подробные сведения о JToken и Newtonsoft Json.NET см. на сайте [Json.NET].

### <a name="inserting"></a>Практическое руководство. Вставка данных в серверную часть мобильных приложений
Все типы клиентов должны содержать член с именем **Id**(идентификатор), который по умолчанию является строкой. Этот **идентификатор** необходим для выполнения операций CRUD и автономной синхронизации. В следующем коде показано, как вставить новые строки в таблицу с помощью метода [InsertAsync] . Параметр содержит данные, которые вставляются в качестве объекта .NET.

```
await todoTable.InsertAsync(todoItem);
```

Если при вставке в `todoItem` не добавлено значение уникального пользовательского идентификатора, то GUID создается сервером.
Созданный идентификатор можно получить, проверив объект после возвращения вызова.

Для вставки нетипизированных данных можно использовать Json.NET.

```
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Вот пример использования электронного адреса в качестве уникального строкового идентификатора.

```
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Работа со значениями идентификаторов
Мобильные приложения поддерживают уникальные настраиваемые строковые значения для столбца **Id** таблицы. Строковое значение позволяет приложениям использовать в качестве идентификатора настраиваемые значения, такие как электронные адреса или имена пользователей.  Строковые идентификаторы предоставляют следующие преимущества.

* Идентификаторы создаются без обмена данными с базой данных.
* Можно легко объединять записи из разных таблиц или баз данных.
* Значения идентификаторов можно удобно интегрировать с логикой приложения.

Если для вставленной записи не задано значение строкового идентификатора, внутренний сервер мобильных приложений создает уникальное значение для идентификатора. Для создания собственных значений идентификатора в клиенте или серверной части можно использовать метод [Guid.NewGuid] .

```
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Практическое руководство. Изменение данных в серверной части мобильных приложений
Следующий код показывает, как обновить существующую запись с тем же идентификатором с помощью метода [UpdateAsync] . Параметр содержит данные, которые обновляются в качестве объекта .NET.

```
await todoTable.UpdateAsync(todoItem);
```

Для обновления нетипизированных данных можно использовать [Json.NET] следующим образом.

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

При выполнении обновления необходимо указать поле `id` . Серверная часть использует поле `id` , чтобы определить, какую из строк нужно обновить. Поле `id` можно получить из результатов вызова метода `InsertAsync`. При попытке обновить элемент без указания значения `id` порождается исключение `ArgumentException`.

### <a name="deleting"></a>Практическое руководство. Удаление данных в серверной части мобильных приложений
Следующий код показывает, как удалить существующий экземпляр с помощью метода [DeleteAsync] . Экземпляр идентифицируется по полю `id`, заданному в свойстве `todoItem`.

```
await todoTable.DeleteAsync(todoItem);
```

Для удаления нетипизированных данных можно использовать Json.NET следующим образом:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

При создании запроса на удаление необходимо указать идентификатор. Другие свойства не передаются в службу или игнорируются ею. В результате вызова функции `DeleteAsync` обычно возвращается значение `null`. Идентификатор для передачи можно получить в результате вызова метода `InsertAsync` . При попытке удалить элемент без указания поля `id` порождается исключение `MobileServiceInvalidOperationException`.

### <a name="optimisticconcurrency"></a>Практическое руководство. Использование оптимистичного параллелизма для устранения конфликтов
Иногда два и более клиентов могут одновременно записывать изменения в один и тот же элемент. Без механизма определения конфликтов последняя операция записи переписывала бы любые предыдущие обновления. **управлении оптимистичным параллелизмом** предполагается, что каждая транзакция может фиксироваться, поэтому не использует блокировки каких-либо ресурсов.  Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается.

Мобильные приложения поддерживают управление оптимистичным параллелизмом за счет отслеживания изменений в каждом элементе с помощью столбца системных свойств `version` , определенного для каждой таблицы в серверной части мобильных приложений. При каждом обновлении записи мобильные приложения задают новое значение свойства `version` для этой записи. При обработке каждого запроса на обновление свойство `version` записи, включенное в запрос, сравнивается с тем же свойством записи на сервере. Если версия, переданная с запросом, не соответствует серверной части, то клиентская библиотека порождает исключение `MobileServicePreconditionFailedException<T>` . Тип, включенный в исключение, является записью серверной части, которая содержит версию записи на сервере. Затем приложение может использовать эти данные, чтобы решить, следует ли повторно выполнить полученный из серверной части запрос изменения с правильным значением `version` для фиксации изменений.

Чтобы включить оптимистичный параллелизм, определите столбец в классе таблицы для системного свойства `version` . Например: 

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Приложения, в которых используются нетипизированные таблицы, включают оптимистичный параллелизм, устанавливая флаг `Version` в таблице `SystemProperties` следующим образом.

```
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Наряду с включением оптимистичного параллелизма при вызове [UpdateAsync] также необходимо перехватывать исключение `MobileServicePreconditionFailedException<T>` в коде.  Разрешите конфликт, применив правильный `version` к обновленной записи и вызвав [UpdateAsync] с разрешенной записью. В следующем коде показано, как разрешить конфликт записи после его обнаружения:

```
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
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
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
```

Дополнительные сведения см. в разделе [Автономная синхронизация данных в мобильных приложениях Azure].

### <a name="binding"></a>Практическое руководство. Привязка данных мобильных приложений к пользовательскому интерфейсу Windows
В этом разделе показано, как отображать возвращенные объекты данных с использованием элементов пользовательского интерфейса в приложении Windows.  Следующий пример кода осуществляет привязку к источнику списка с помощью запроса незавершенных элементов. При использовании [MobileServiceCollection] создается коллекция привязок, поддерживающих мобильные приложения.

```
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Некоторые элементы управления в управляемой среде выполнения Windows поддерживают интерфейс [ISupportIncrementalLoading]. Этот интерфейс позволяет элементам управления запрашивать дополнительные данные во время прокрутки, выполняемой пользователем. Предусмотрена встроенная поддержка этого интерфейса для универсальных приложений Windows через коллекцию [MobileServiceIncrementalLoadingCollection], которая автоматически обрабатывает вызовы от элементов управления. Используйте `MobileServiceIncrementalLoadingCollection` в приложениях для Windows, как показано ниже.

```
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Чтобы использовать новую коллекцию в приложениях для Windows Phone 8 и Silverlight, используйте методы расширения `ToCollection` в интерфейсах `IMobileServiceTableQuery<T>` и `IMobileServiceTable<T>`. Для загрузки данных вызовите `LoadMoreItemsAsync()`.

```
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

При использовании коллекции, созданной вызовом метода `ToCollectionAsync` или `ToCollection`, вы получите коллекцию, которую можно привязать к элементам управления пользовательского интерфейса.  Эта коллекция поддерживает разбиение по страницам.  Так как коллекция загружает данные из сети, иногда происходит сбой загрузки. Чтобы обработать подобные ошибки, переопределите метод `OnException` в классе `MobileServiceIncrementalLoadingCollection` для обработки исключений, возникающих в результате вызова метода `LoadMoreItemsAsync`.

Представьте, что в таблице есть множество полей, однако необходимо отобразить только те из них, которыми вам нужно управлять. С помощью инструкций в разделе[Выбор конкретных столбцов](#selecting)выше можно выбрать столбцы, отображаемые в пользовательском интерфейсе.

### <a name="pagesize"></a>Изменение размера страницы
По умолчанию мобильные приложения Azure выдают не больше 50 элементов на запрос.  Можно изменить параметры разбиения по страницам, увеличив максимальный размер страницы для клиента и сервера.  Чтобы увеличить размер запрошенной страницы, при использовании `PullAsync()` укажите `PullOptions`.

```
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Предположим, мы установили на сервере значение `PageSize`, равное или большее 100. Тогда каждый запрос будет возвращать до 100 элементов.

## <a name="#offlinesync"></a>Работа с автономными таблицами
Автономные таблицы используют локальное хранилище SQLite для хранения данных, которые могут использоваться в режиме "вне сети".  Во всех операциях с таблицами используется локальное хранилище SQLite, а не хранилище удаленного сервера.  Чтобы создать автономную таблицу, сначала необходимо подготовить проект.

1. В Visual Studio щелкните правой кнопкой мыши решение, выберите пункт **Управление пакетами NuGet для решения…**, а затем найдите и установите пакет NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** для всех проектов в решении.
2. (Необязательный шаг.) Чтобы обеспечить поддержку устройств Windows, установите один из следующих пакетов среды выполнения SQLite:

   * **Среда выполнения Windows 8.1**: установите [SQLite для Windows 8.1][3].
   * **Windows Phone 8.1**: установите [SQLite для Windows Phone 8.1][4].
   * **Универсальная платформа Windows**: установите [SQLite для универсальной платформы Windows][5].
3. (необязательно). Для устройств с Windows щелкните правой кнопкой мыши **Ссылки** > **Добавить ссылку…**, разверните папку **Windows** > **Расширения**, а затем включите соответствующий пакет SDK **SQLite для Windows** и пакет SDK **среды выполнения Visual C++ 2013 для Windows**.
    Имена пакетов SDK для SQLite немного отличаются в зависимости от версии платформы Windows.

Перед созданием ссылки на таблицу необходимо подготовить локальное хранилище.

```
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Обычно хранилище инициализируется сразу же после создания клиента.  Значением **OfflineDbPath** должно быть имя файла, подходящее для всех платформ, которые вы поддерживаете.  Если указан полный путь (то есть начинающийся с косой черты), то используется он.  Если путь не полный, то файл помещается в расположение для конкретной платформы.

* Для устройств iOS и Android по умолчанию выбрана папка Personal Files.
* Для устройств с Windows по умолчанию выбрана папка AppData конкретного приложения.

Ссылку на таблицу можно получить с помощью метода `GetSyncTable<>`.

```
var table = client.GetSyncTable<TodoItem>();
```

Для использования автономной таблицы не обязательно проходить аутентификацию.  Ее необходимо проходить только при обмене данными с внутренней службой.

### <a name="syncoffline"></a>Синхронизация автономной таблицы
По умолчанию автономные таблицы не синхронизируются с серверной частью.  Синхронизация происходит в два этапа.  Можно передавать изменения отдельно от скачивания новых элементов.  Ниже приведен типичный метод синхронизации.

```
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Если первый аргумент для `PullAsync` имеет значение NULL, то добавочная синхронизация не используется.  Каждая операция синхронизации извлекает все записи.

Пакет SDK выполняет неявную операцию `PushAsync()` перед извлечением записей.

Обработка конфликтов происходит в методе `PullAsync()`.  Конфликты можно обработать таким же образом, как для таблиц в сети.  Конфликт возникает, когда во время вставки, обновления или удаления вызывается `PullAsync()`. Если возникает несколько конфликтов, они объединяются в одно исключение MobileServicePushFailedException.  Каждая ошибка должна обрабатываться отдельно.

## <a name="#customapi"></a>Работа с настраиваемым API
Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API можно вызвать путем вызова одного из методов [InvokeApiAsync] для клиента. Например, следующая строка кода отправляет запрос POST API-интерфейсу **completeAll** в серверную часть:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Это вызов типизированного метода, для которого требуется определить тип возвращаемого значения **MarkAllResult**. Поддерживаются типизированные и нетипизированные методы.

Метод InvokeApiAsync() добавляет /api/ к имени API, к которому нужно совершить вызов, если имя API не начинается с "/".
Например: 

* `InvokeApiAsync("completeAll",...)` вызывает /api/completeAll на сервере.
* `InvokeApiAsync("/.auth/me",...)` вызывает /.auth/me на сервере.

InvokeApiAsync можно использовать для вызова любого веб-API, включая веб-API, не определенные с помощью мобильных приложений Azure.  При использовании InvokeApiAsync() в запросе отправляются соответствующие заголовки, включая заголовки аутентификации.

## <a name="authentication"></a>Аутентификация пользователей
Мобильные приложения поддерживают аутентификацию и авторизацию пользователей с помощью различных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике [Добавление проверки подлинности в приложение].

Поддерживаются два потока аутентификации: *управляемой клиентом* и *управляемый сервером*. Управляемый сервером поток обеспечивает самый простой способ аутентификации, так как он использует веб-интерфейс аутентификации. Управляемый клиентом поток обеспечивает более тесную интеграцию с возможностями устройства, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

> [!NOTE]
> Мы рекомендуем использовать в рабочих приложениях поток, управляемый клиентом.

Чтобы настроить аутентификацию, следует зарегистрировать приложение в одном или нескольких поставщиках удостоверений.  Поставщик удостоверений создаст идентификатор клиента и секрет клиента для вашего приложения.  Затем эти значения задаются в серверной части и используются для аутентификации и авторизации в службе приложений Azure.  Дополнительные сведения приведены в руководстве [Добавление проверки подлинности в приложение].

В этом разделе рассматриваются следующие темы.

* [Управляемая клиентом проверка подлинности.](#clientflow)
* [Управляемая сервером проверка подлинности.](#serverflow)
* [Кэширование маркера аутентификации](#caching)

### <a name="clientflow"></a>Управляемая клиентом проверка подлинности.
Приложение может самостоятельно связаться с поставщиком удостоверений и передать серверной части вашего приложения полученный маркер для проверки подлинности. Этот клиентский поток позволяет пользователям выполнять единый вход или получать дополнительные данные о пользователе от поставщика удостоверений. Клиентский поток аутентификации предпочтительнее, чем серверный, так как пакет SDK поставщика удостоверений обеспечивает более естественный интерфейс входа для пользователя и позволяет выполнять дополнительную настройку.

Доступны примеры организации некоторых клиентских потоков проверки подлинности.

* [Библиотека проверки подлинности Active Directory](#adal)
* [Facebook или Google](#client-facebook)
* [Пакет Live SDK](#client-livesdk)

#### <a name="adal"></a>Аутентификация пользователей с помощью библиотеки аутентификации Active Directory
Вы можете использовать библиотеку проверки подлинности Active Directory (ADAL) для управления входом пользователей из клиента с помощью Azure Active Directory.

1. Настройте серверную часть мобильного приложения для входа с помощью AAD, следуя указаниям в учебнике [Настройка приложения службы приложений для использования службы входа Azure Active Directory]. Обязательно выполните дополнительный этап регистрации собственного клиентского приложения.
2. Откройте проект в Visual Studio или Xamarin Studio и добавьте ссылку на пакет NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory` . Включите в диапазон поиска предварительные версии.
3. Добавьте в приложение код, соответствующий используемой платформе, который приведен ниже. В каждом коде выполните следующие замены.

   * Замените строку **INSERT-AUTHORITY-HERE** именем клиента, в котором подготовлено приложение. Его необходимо указать в формате https://login.microsoftonline.com/contoso.onmicrosoft.com. Это значение можно скопировать на вкладке "Домен" в разделе Azure Active Directory на [портале Azure].
   * Замените текст **INSERT-RESOURCE-ID-HERE** идентификатором клиента для серверной части мобильного приложения. Идентификатор клиента можно скопировать на портале в разделе **Настройки Azure Active Directory** на вкладке **Дополнительно**.
   * Замените текст **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.
   * Замените текст **INSERT-REDIRECT-URI-HERE** конечной точкой сайта */.auth/login/done* , используя схему HTTPS. Это значение должно быть аналогично *https://contoso.azurewebsites.net/.auth/login/done*.

     Ниже приведены колы для каждой платформы.

     **Windows:**

    ```
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
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
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
    ```

     **Xamarin.iOS**

    ```
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
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

     **Xamarin.Android**

    ```
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
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
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
    ```

#### <a name="client-facebook"></a>Единый вход с помощью маркера Google или Facebook
В этом фрагменте показан пример использования клиентского потока для Google или Facebook.

```
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
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
```

#### <a name="client-livesdk"></a>Единый вход с использованием учетной записи Майкрософт и пакета Live SDK
Чтобы аутентифицировать пользователей, необходимо зарегистрировать свое приложение в центре разработчиков учетных записей Майкрософт. Настройте регистрационные данные для серверной части мобильного приложения. Выполните действия, описанные в разделе [Регистрация приложения для входа с использованием учетной записи Майкрософт], чтобы создать учетную запись Майкрософт и подключить ее к серверной части мобильного приложения. Если у вас есть версии приложения Магазина Windows и Windows Phone 8/Silverlight, сначала зарегистрируйте версию для Магазина Windows.

Следующий код выполняет аутентификацию с помощью пакета Live SDK и использует возвращенный маркер для входа в серверную часть мобильного приложения.

```
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
```

Дополнительные сведения см. в документации по [пакету SDK для Windows Live].

### <a name="serverflow"></a>Управляемая сервером проверка подлинности.
После регистрации поставщика удостоверений вызовите метод [LoginAsync] для [MobileServiceClient], передав ему значение [MobileServiceAuthenticationProvider], соответствующее вашему поставщику. Например, следующий код запускает вход в систему через поток сервера с помощью Facebook.

```
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
```

Если используется поставщик удостоверений, отличный от Facebook, измените значение [MobileServiceAuthenticationProvider] на значение для своего поставщика.

При использовании серверного потока служба приложений Azure управляет потоком аутентификации OAuth, отображая страницу входа выбранного поставщика.  После получения ответа от поставщика удостоверений служба приложений Azure создает маркер аутентификации службы приложений. Метод [LoginAsync] возвращает ответ [MobileServiceUser], в котором содержится как [userId] авторизованного пользователя, так и [MobileServiceAuthenticationToken] в качестве веб-маркера JSON Web Token (JWT). Этот маркер можно поместить в кэш и повторно использовать, пока не истечет срок его действия. Дополнительные сведения см. в разделе [Кэширование маркера проверки подлинности](#caching).

### <a name="caching"></a>Кэширование маркера проверки подлинности
В некоторых случаях после первой успешной аутентификации можно не выполнять вызов метода входа в систему. Для этого следует сохранить маркер аутентификации.  Приложения Магазина Windows и UWP могут использовать [PasswordVault] для сохранения текущего маркера аутентификации после успешного входа в систему.

```
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Значение идентификатора пользователя (UserId) хранится в параметре UserName учетных данных. Маркер проверки подлинности хранится в параметре Password. При последующих запусках вы можете проверить, сохранились ли учетные данные в **PasswordVault**. В следующем примере используются сохраненные учетные данные, если они есть. В противном случае выполняется новая попытка пройти проверку подлинности в серверной части.

```
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

При выходе пользователя из приложения необходимо удалить сохраненные учетные данные, как показано ниже.

```
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Приложения Xamarin используют интерфейсы API [Xamarin.Auth], которые позволяют безопасно хранить учетные данные в объекте **Account**. Пример использования этих API см. в файле кода [AuthStore.cs], который включен в [пример совместного использования фотографий ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Если вы используете клиентский поток проверки подлинности, вы можете сохранить маркер доступа, полученный от поставщика, такого как Facebook или Twitter. Этот маркер можно использовать для запроса нового маркера проверки подлинности у серверной части:

```
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Push-уведомления
Push-уведомления рассматриваются в следующих разделах.

* [Регистрация для получения push-уведомлений](#register-for-push)
* [Получение SID пакета Магазина Windows](#package-sid)
* [Регистрация с помощью межплатформенных шаблонов](#register-xplat)

### <a name="register-for-push"></a>Практическое руководство. Регистрация для получения push-уведомлений
Клиент мобильных приложений позволяет выполнить регистрацию для получения push-уведомлений с помощью центров уведомлений Azure. При регистрации вы получаете маркер из службы push-уведомлений (PNS). Это значение необходимо указать при регистрации вместе со всеми тегами. Следующий код регистрирует ваше приложение Windows для получения push-уведомлений через службу уведомлений Windows (WNS):

```
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

При отправке push-уведомлений в WNS необходимо [получить идентификатор безопасности пакета Магазина Windows](#package-sid).  Дополнительные сведения о приложениях Windows, включая способ регистрации шаблонов, см. в разделе [Добавление push-уведомлений в приложение].

Запрос тегов от клиента не поддерживается.  Запросы тегов автоматически отбрасываются из регистрации.
Если вы хотите зарегистрировать устройство с тегами, создайте пользовательский интерфейс API, который использует API центров уведомлений для выполнения регистрации от вашего имени.  [Используйте вызов настраиваемого API](#customapi) вместо метода `RegisterNativeAsync()`.

### <a name="package-sid"></a>Практическое руководство. Получение SID пакета Магазина Windows
Для включения push-уведомлений для приложений Магазина Windows необходим SID пакета.  Для получения идентификатора безопасности пакета зарегистрируйте приложение в Магазине Windows.

Для получения этого значения выполните следующие действия:

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения Магазина Windows и щелкните **Магазин** > **Связать приложение с Магазином…**.
2. В окне мастера нажмите кнопку **Далее**, выполните вход с помощью учетной записи Майкрософт, введите имя приложения в поле **Зарезервировать новое имя приложения** и нажмите кнопку **Зарезервировать**.
3. После успешного создания регистрации приложения выберите имя приложения, нажмите кнопку **Далее**, а затем кнопку **Связать**.
4. Выполните вход в [Центр разработки для Windows] с использованием учетной записи Майкрософт. В разделе **Мои приложения**щелкните созданную регистрацию приложения.
5. Щелкните **Управление приложениями** > **Удостоверение приложения**, а затем выполните прокрутку вниз, чтобы найти ваш **идентификатор безопасности пакета**.

Во многих случаях идентификатор безопасности пакета рассматривается как универсальный код ресурса (URI). В этом случае необходимо использовать *ms-app://* в качестве схемы. Запишите версию ИД безопасности пакета, получаемую путем сцепления этого значения в качестве префикса.

Приложениям Xamarin требуется дополнительный код для регистрации приложений, работающих на платформе iOS или Android. Дополнительные сведения см. в разделе для вашей платформы:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Использование шаблонов для отправки кроссплатформенных push- уведомлений
Чтобы зарегистрировать шаблоны, используйте метод `RegisterAsync()` с шаблонами, как показано ниже.

```
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Шаблоны должны иметь тип `JObject` и могут содержать несколько шаблонов в приведенном ниже формате JSON.

```
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

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
```

Метод **RegisterAsync()** принимает также вспомогательные плитки:

```
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Во время регистрации все теги удаляются в целях безопасности. В статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure] показано, как добавлять теги для установки устройства или шаблоны в пределах установки.

Для отправки уведомлений с использованием этих зарегистрированных шаблонов ознакомьтесь со [справочниками по API].

## <a name="misc"></a>Разное
### <a name="errors"></a>Практическое руководство. Обработка ошибок
При возникновении ошибки в серверной части пакет SDK для клиента порождает исключение `MobileServiceInvalidOperationException`.  Следующий пример демонстрирует обработку исключения, возвращенного серверной частью:

```
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
```

Еще один пример работы с состояниями ошибки можно найти в [примере файлов мобильных приложений]. Пример [LoggingHandler] предоставляет обработчик делегированного входа для ведения журнала запросов к серверной части.

### <a name="headers"></a>Практическое руководство. Настройка заголовков запроса
Для поддержки определенного сценария приложения вам может потребоваться настроить связь с внутренним сервером мобильных приложений. Например, может возникнуть необходимость добавлять настраиваемый заголовок к каждому исходящему запросу или даже изменять коды состояний ответов. Вы можете использовать настраиваемый метод [DelegatingHandler], как показано в следующем примере.

```
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
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
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Добавление проверки подлинности в приложение]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Автономная синхронизация данных в мобильных приложениях Azure]: app-service-mobile-offline-data-sync.md
[Добавление push-уведомлений в приложение]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Регистрация приложения для входа с использованием учетной записи Майкрософт]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Настройка приложения службы приложений для использования службы входа Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[создает ссылку на нетипизированную таблицу]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[портале Azure]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Центр разработки для Windows]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[пакету SDK для Windows Live]: https://msdn.microsoft.com/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[справочниками по API]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[примере файлов мобильных приложений]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[документации по OData версии 3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
