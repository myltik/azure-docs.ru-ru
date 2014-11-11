<properties linkid="mobile-services-how-to-xamarin-client" urlDisplayName="Xamarin" pageTitle="How to use the Xamarin Component client - Azure Mobile Services feature guide" metaKeywords="Azure Mobile Services, Xamarin, iOS, Android, .NET client" description="Learn how to use the Xamarin Component client for Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to use the Xamarin Component client for Azure Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Использование клиентского компонента Xamarin для мобильных служб Azure

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a>
    <a href="/ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>

В этом руководстве показано, как реализовать типичные сценарии с использованием клиентского компонента Xamarin для мобильных служб Azure в приложениях Xamarin для iOS и Android. В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей и обработка ошибок. Если вы не знакомы с мобильными службами, следует сначала ознакомиться с учебником "Быстрый запуск мобильных служб" ([Xamarin.iOS][Xamarin.iOS] и [Xamarin.Android][Xamarin.Android]) и с учебником "Приступая к работе с данными в .NET" ([Xamarin.iOS][1] и [Xamarin.Android][2]). Для краткого учебника потребуется [Xamarin][3], [Mobile Services SDK][Mobile Services SDK]; он поможет вам настроить учетную запись и создать свою первую мобильную службу.

## Оглавление

-   [Что такое мобильные службы?][Что такое мобильные службы?]
-   [Основные понятия][Основные понятия]
-   [Практическое руководство. Создание клиента мобильных служб][Практическое руководство. Создание клиента мобильных служб]
-   [Практическое руководство. Создание ссылки на таблицу][Практическое руководство. Создание ссылки на таблицу]
-   [Практическое руководство. Запрос данных от мобильной службы][Практическое руководство. Запрос данных от мобильной службы]

    -   [Фильтрация возвращаемых данных][Фильтрация возвращаемых данных]
    -   [Сортировка возвращаемых данных][Сортировка возвращаемых данных]
    -   [Возврат данных на страницах][Возврат данных на страницах]
    -   [Выбор определенных столбцов][Выбор определенных столбцов]
    -   [Поиск данных по идентификатору][Поиск данных по идентификатору]
-   [Практическое руководство. Вставка данных в мобильную службу][Практическое руководство. Вставка данных в мобильную службу]
-   [Практическое руководство. Изменение данных в мобильной службе][Практическое руководство. Изменение данных в мобильной службе]
-   [Практическое руководство. Удаление данных в мобильной службе][Практическое руководство. Удаление данных в мобильной службе]
-   [Практическое руководство. Проверка подлинности пользователей][Практическое руководство. Проверка подлинности пользователей]
-   [Практическое руководство. Обработка ошибок][Практическое руководство. Обработка ошибок]
-   [Практическое руководство. Работа с нетипизированными данными][Практическое руководство. Работа с нетипизированными данными]
-   [Практическое руководство. Разработка модульных тестов][Практическое руководство. Разработка модульных тестов]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

## <a name="setup"></a><span class="short-header">Настройка</span>Настройка и предварительные требования

Мы предполагаем, что вы создали мобильную службу и таблицу. Дополнительные сведения см. в разделе [Создание таблицы][Создание таблицы]. В коде, который используется в этом разделе, используется таблица `TodoItem`, которая содержит следующие столбцы: `id`, `Text` и `Complete`.

Соответствующий типизированный тип .NET на стороне клиента выглядит следующим образом:

    public class TodoItem
    {
        public string id { get; set; }
        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }
        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

Если динамическая схема включена, мобильные службы Azure автоматически создают новые столбцы на основе объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема][Динамическая схема].

## <a name="create-client"></a><span class="short-header">Создание клиента мобильных служб</span>Практическое руководство. Создание клиента мобильных служб

В следующем коде создается объект `MobileServiceClient`, который используется для доступа к мобильной службе.

    MobileServiceClient client = new MobileServiceClient( 
        "AppUrl", 
        "AppKey" 
    ); 

В приведенном выше коде замените `AppUrl` и `AppKey` на URL-адрес мобильной службы и ключ приложения соответственно. Оба варианта доступны на портале управления Azure: выберите свою мобильную службу и щелкните "Панель управления".

## <a name="instantiating"></a><span class="short-header">Создание ссылки на таблицу</span>Практическое руководство. Создание ссылки на таблицу

Весь код, который обращается к данным в таблице мобильных служб или изменяет эти данные, вызывает функции объекта `MobileServiceTable`. Ссылку на таблицу можно получить, вызвав функцию [GetTable][GetTable] для экземпляра `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable = 
        client.GetTable<TodoItem>();

Это типизированная модель сериализации; обсуждение [типизированной модели сериализации][Практическое руководство. Работа с нетипизированными данными] см. ниже.

## <a name="querying"></a><span class="short-header">Запрос данных</span>Практическое руководство. Запрос данных из мобильной службы

В этом разделе описывается, как отправлять запросы мобильной службе. В подразделах описываются различные аспекты, например сортировка, фильтрация и разбиение по страницам.

### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Следующий код иллюстрирует способ фильтрации данных, включая предложение `Where` в запросе. Он возвращает все элементы таблицы `todoTable`, свойство `Complete` которых равно `false`. Функция `Where` применяет в запросе к таблице предикат фильтрации строк.

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

Инструкция `where` выше будет находить элементы, для состояния `Complete` которых задано значение false, со значением `Text`, не равным NULL.

Это же выражение можно написать с использованием нескольких строк:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Эти два способа эквивалентны и могут быть взаимозаменяемыми. Первый вариант - объединение нескольких предикатов в одном запросе - является более компактным и рекомендуемым.

Предложение `where` поддерживает операции, которые будут преобразованы в подмножество OData мобильных служб. К ним относятся операторы сравнения (==, !=, \<, \<=, \>, \>=), арифметические операторы (+, -, /, \*, %), методы, связанные с точностью чисел (Math.Floor, Math.Ceiling), строковые функции (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), свойства даты (Year, Month, Day, Hour, Minute, Second), свойства доступа к объекту, а также сочетания перечисленных операторов.

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

В следующем коде показано, как сортировать данные, включив в запрос функцию `OrderBy` или `OrderByDescending`. Он возвращает элементы таблицы `todoTable`, упорядочивая их по возрастанию значений в поле `Text`. По умолчанию сервер возвращает только первые 50 элементов.

<div class="dev-callout"><strong>Примечание.</strong> <p>Чтобы не допустить возврата всех элементов, по умолчанию используется размер страницы, устанавливаемый сервером. Это предотвращает негативное воздействие больших наборов данных на функционирование службы. </p> </div>

Можно увеличить количество возвращаемых элементов путем вызова метода `Take`, как описано в следующем разделе.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)       
    List<TodoItem> items = await query.ToListAsync();
    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       
    List<TodoItem> items = await query.ToListAsync();           

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах

В следующем коде показано, как реализовать разбиение по страницам в возвращаемых данных с помощью предложений `Take` и `Skip` в запросе. При выполнении следующего запроса будут возвращены три главных элемента в таблице.

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
            

Можно также использовать метод [IncludeTotalCount][IncludeTotalCount], чтобы запрос возвращал общее количество *всех* записей, которые должны быть возвращены без учета указанных предложений paging/limit:

    query = query.IncludeTotalCount();

Это упрощенный сценарий передачи жестко запрограммированных значений разбиения по страницам в методы `Take` и `Skip`. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице.

### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

Добавив к запросу предложение `Select`, можно задать набор свойств, который будет включен в возвращаемые результаты. Например, в следующем коде показано, как выбрать только одно поле, а также способы выбора и форматирования нескольких полей:

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

    // This query filters out the item with the ID of 25
    TodoItem item25 = await todoTable.LookupAsync(25);

## <a name="inserting"></a><span class="short-header">Вставка данных</span>Практическое руководство. Вставка данных в мобильную службу

<div class="dev-callout"><strong>Примечание.</strong> <p>Если вы хотите выполнить операцию вставки, поиска, удаления или обновления для данного типа, необходимо создать элемент с именем <strong>Id</strong> (независимо от регистра). Именно поэтому в примере класса <strong>TodoItem</strong> используется член с именем <strong>Id</strong>. При выполнении вставки не задавайте значение идентификатора, отличное от его значения по умолчанию. И наоборот, при выполнении операций обновления и удаления нужно обязательно изменить значение идентификатора, чтобы оно отличалось от значения по умолчанию.</p> </div>

В следующем коде показано, как вставить новые строки в таблицу. Параметр содержит данные, которые вставляются в качестве объекта .NET.

    await todoTable.InsertAsync(todoItem);

После получения возвращаемого значения от ожидающего вызова `todoTable.InsertAsync` идентификатор объекта на сервере заполняется значением объекта `todoItem` в клиенте.

Для вставки нетипизированных данных можно использовать Json.NET, как показано ниже. Обратите внимание, что при вставке объекта идентификатор указывать не нужно.

    JObject jo = new JObject(); 
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

При попытке вставить элемент с уже заданным полем Id от службы будет получен ответ `MobileServiceInvalidOperationException`.

## <a name="modifying"></a><span class="short-header">Изменение данных</span>Практическое руководство. Изменение данных в мобильной службе

Следующий код показывает, как добавить новую информацию в существующий экземпляр с тем же идентификатором. Параметр содержит данные, которые обновляются в качестве объекта .NET.

    await todoTable.UpdateAsync(todoItem);

Для вставки нетипизированных данных можно использовать Json.NET. Обратите внимание, что при выполнении обновления должен быть указан идентификатор, поскольку с его помощью мобильная служба определяет, какой экземпляр нужно обновить. Идентификатор можно получить из результатов вызова метода `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", 52);
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);
            

При попытке обновить элемент без заданного поля Id служба не сможет определить, какой экземпляр нужно обновить, поэтому от службы будет получен ответ `MobileServiceInvalidOperationException`. Аналогичным образом, при попытке обновить нетипизированный элемент без заданного поля Id от службы также будет получен ответ `MobileServiceInvalidOperationException`.

## <a name="deleting"></a><span class="short-header">Удаление данных</span>Практическое руководство. Удаление данных в мобильной службе

Следующий код показывает, как удалить существующий экземпляр. Экземпляр идентифицируется по полю Id, заданному в свойстве `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Для удаления нетипизированных данных можно использовать Json.NET. Обратите внимание, что при выполнении запроса на удаление должен быть указан идентификатор, поскольку с его помощью мобильная служба определяет, какой экземпляр нужно удалить. Для запроса на удаление требуется только идентификатор; другие свойства не передаются в службу; любые передаваемые свойства будут игнорироваться службой. В результате вызова функции `DeleteAsync` обычно возвращается значение `null`. Идентификатор для передачи можно получить в результате вызова метода `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", 52);
    await table.DeleteAsync(jo);
            

Если предпринимается попытка удалить элемент без заданного поля Id, служба не сможет определить, какой экземпляр нужно удалить, поэтому от службы будет получен ответ `MobileServiceInvalidOperationException`. Аналогично, при попытке удалить нетипизированный элемент, для которого не задано поле Id, от службы также будет получен ответ `MobileServiceInvalidOperationException`.

## <a name="authentication"></a><span class="short-header">Проверка подлинности</span>Практическое руководство. Проверка подлинности пользователей

Мобильные службы поддерживают аутентификацию и авторизацию пользователей с помощью различных внешних поставщиков удостоверений: Facebook, Google, учетной записи Майкрософт, Twitter и Azure Active Directory. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в руководстве "Приступая к работе с аутентификацией" ([Xamarin.iOS][4] и [Xamarin.Android][5]).

Поддерживаются два потока аутентификации: *серверный* и *клиентский*. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

### Серверный поток

Чтобы мобильная служба могла управлять аутентификацией в приложении для Магазина Windows или Windows Phone,
необходимо зарегистрировать приложение в поставщике удостоверений. Затем в вашей мобильной службе необходимо настроить код приложения и секретный код, предоставленный поставщиком. Дополнительные сведения см. в руководстве "Приступая к работе с аутентификацией" ([Xamarin.iOS][4] и [Xamarin.Android][5]).

После регистрации у поставщика удостоверений просто вызовите [метод LoginAsync][метод LoginAsync] с указанием значения [MobileServiceAuthenticationProvider][MobileServiceAuthenticationProvider] вашего поставщика. Например, следующий код запускает вход в систему через поток сервера с помощью Facebook.

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

Если используется поставщик удостоверений, отличный от Facebook, измените значение [MobileServiceAuthenticationProvider][MobileServiceAuthenticationProvider] выше на значение для вашего поставщика.

В этом случае мобильная служба управляет потоком проверки подлинности OAuth 2.0, отображая страницу входа выбранного поставщика и генерируя маркер проверки подлинности мобильных служб после успешного соединения с поставщиком удостоверений. Метод [LoginAsync][метод LoginAsync] возвращает ответ [MobileServiceUser][MobileServiceUser], в котором содержится как [userId][userId] авторизованного пользователя, так и [MobileServiceAuthenticationToken][MobileServiceAuthenticationToken] в качестве веб-маркера JSON (JWT). Этот маркер можно кэшировать и повторно использовать до истечения срока его действия. Дополнительные сведения см. в разделе [Кэширование маркера проверки подлинности][Кэширование маркера проверки подлинности].

### Клиентский поток

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

### <a name="caching"></a>Кэширование маркера аутентификации

В некоторых случаях вызова способа входа в систему можно избежать первого выполнения проверки подлинности пользователя. Можно использовать местное защищенное хранилище (например, [Xamarin.Auth][Xamarin.Auth]) для кэширования удостоверения текущего пользователя при первом входе в систему; при каждом последующем входе будет выполняться проверка наличия удостоверения пользователя в кэше. Если кэш пуст, пользователь будет перенаправлен на страницу входа в систему.

    using Xamarin.Auth;
    var accountStore = AccountStore.Create(); // Xamarin.iOS
    //var accountStore = AccountStore.Create(this); // Xamarin.Android
    // After logging in
    var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
    accountStore.Save(account, "Facebook");
    // Log in 
    var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
    if (accounts.Count != 0)
    {
        user = new MobileServiceUser (accounts[0].Username);
        user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
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
    accountStore.Delete(account, "Facebook");

## <a name="errors"></a><span class="short-header">Обработка ошибок</span>Практическое руководство. Обработка ошибок

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

## <a name="untyped"></a><span class="short-header">Работа с нетипизированными данными</span>Практическое руководство. Работа с нетипизированными данными

Клиентский компонент Xamarin предназначен для строго типизированных сценариев. Тем не менее в некоторых случаях удобнее использовать менее типизированные сценарии; например, это может потребоваться при работе с объектами с открытой схемой. Этот сценарий активируется следующим образом. В запросах нужно отказаться от LINQ и использовать телеграфный формат.

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");         
    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Вы будете получать значения JSON, которые можно использовать в качестве контейнера свойств. Более подробные сведения о JToken и Json.NET см. в разделе [Json.NET][Json.NET]

## <a name="unit-testing"></a><span class="short-header">Разработка тестов</span>Практическое руководство. Разработка модульных тестов

Функция `MobileServiceClient.GetTable` и запросы возвращают значение, которое является интерфейсом. Из-за этого их легко критиковать с точки зрения целесообразности тестирования, поэтому вы можете создать `MyMockTable : IMobileServiceTable<TodoItem>` для реализации своей логики тестирования.

## <a name="nextsteps"></a>Дальнейшие действия

Теперь, когда вы закончили изучение этого концептуального раздела, узнайте более подробно, как можно выполнять дополнительные важные задачи в мобильных службах.

-   Приступая к работе с мобильными службами ([Xamarin.iOS][6] и [Xamarin.Android][7])
    Изучите основы использования мобильных служб.

-   Приступая к работе с данными ([Xamarin.iOS][8] и [Xamarin.Android][9])
    Узнайте больше о хранении данных и запросах к ним при помощи мобильных служб.

-   Приступая к работе с аутентификацией ([Xamarin.iOS][10] и [Xamarin.Android][11])
    Узнайте больше об аутентификации пользователей приложения с помощью поставщика удостоверений.

-   Проверка и изменение данных с помощью сценариев ([Xamarin.iOS][12] и [Xamarin.Android][13])
    Узнайте больше об использовании серверных сценариев в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   Уточнение запросов c разбиением по страницам ([Xamarin.iOS][14] и [Xamarin.Android][15])
    Узнайте о том, как использовать в запросах разбиения по страницам, чтобы управлять объемом данных, обрабатываемых в одном запросе.

-   Авторизация пользователей с помощью сценариев ([Xamarin.iOS][16] и [Xamarin.Android][17])
    Узнайте о том, как использовать значение идентификатора пользователя, предоставляемого мобильными службами на основе пользователя, прошедшего аутентификацию, для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. --> 
<!-- URLs. -->

  [Xamarin]: /ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin"
  [Xamarin.iOS]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios/
  [Xamarin.Android]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android/
  [1]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios/
  [2]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android/
  [3]: http://xamarin.com/download/
  [Mobile Services SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
  [Основные понятия]: #concepts
  [Практическое руководство. Создание клиента мобильных служб]: #create-client
  [Практическое руководство. Создание ссылки на таблицу]: #instantiating
  [Практическое руководство. Запрос данных от мобильной службы]: #querying
  [Фильтрация возвращаемых данных]: #filtering
  [Сортировка возвращаемых данных]: #sorting
  [Возврат данных на страницах]: #paging
  [Выбор определенных столбцов]: #selecting
  [Поиск данных по идентификатору]: #lookingup
  [Практическое руководство. Вставка данных в мобильную службу]: #inserting
  [Практическое руководство. Изменение данных в мобильной службе]: #modifying
  [Практическое руководство. Удаление данных в мобильной службе]: #deleting
  [Практическое руководство. Проверка подлинности пользователей]: #authentication
  [Практическое руководство. Обработка ошибок]: #errors
  [Практическое руководство. Работа с нетипизированными данными]: #untyped
  [Практическое руководство. Разработка модульных тестов]: #unit-testing
  [Дальнейшие действия]: #nextsteps
  [mobile-services-concepts]: ../includes/mobile-services-concepts.md
  [Создание таблицы]: http://go.microsoft.com/fwlink/?LinkId=298592
  [Динамическая схема]: http://go.microsoft.com/fwlink/?LinkId=296271
  [GetTable]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554275.aspx
  [IncludeTotalCount]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj730933.aspx
  [4]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios/
  [5]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android/
  [метод LoginAsync]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
  [MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
  [MobileServiceUser]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
  [userId]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
  [MobileServiceAuthenticationToken]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
  [Кэширование маркера проверки подлинности]: #caching
  [Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth
  [Json.NET]: http://json.codeplex.com/
  [6]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios
  [7]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android
  [8]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [9]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [10]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [11]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [12]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
  [13]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
  [14]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [15]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android
  [16]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
  [17]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
