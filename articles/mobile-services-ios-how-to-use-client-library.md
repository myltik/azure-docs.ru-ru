<properties linkid="mobile-services-how-to-ios-client" urlDisplayName="iOS Client Library" pageTitle="How to use the iOS client library - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service iOS client library, iOS client library" description="Learn how to use the iOS client library for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the iOS client library for Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Использование клиентской библиотеки iOS для мобильных служб

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>

В этом руководстве показано, как реализовать типичные сценарии с использованием клиента iOS для мобильных служб Azure. Примеры написаны на objective-C и используют [пакет Mobile Services SDK][пакет Mobile Services SDK]. Для работы с данным учебником также требуется пакет [iOS SDK][iOS SDK]. В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей и обработка ошибок. Если вы не знакомы с мобильными службами, сначала мы рекомендуем прочитать [краткое руководство по мобильным службам][краткое руководство по мобильным службам]. Учебник quickstart поможет вам настроить учетную запись и создать свою первую мобильную службу.

## Оглавление

-   [Что такое мобильные службы?][Что такое мобильные службы?]
-   [Основные понятия][Основные понятия]
-   [Настройка и необходимые компоненты][Настройка и необходимые компоненты]
-   [Практическое руководство. Создание клиента мобильных служб][Практическое руководство. Создание клиента мобильных служб]
-   [Практическое руководство. Создание ссылки на таблицу][Практическое руководство. Создание ссылки на таблицу]
-   [Практическое руководство. Запрос данных от мобильной службы][Практическое руководство. Запрос данных от мобильной службы]

    -   [Фильтрация возвращаемых данных][Фильтрация возвращаемых данных]
    -   [Использование объекта MSQuery][Использование объекта MSQuery]
    -   [Выбор определенных столбцов][Выбор определенных столбцов]
-   [Практическое руководство. Вставка данных в мобильную службу][Практическое руководство. Вставка данных в мобильную службу]
-   [Практическое руководство. Изменение данных в мобильной службе][Практическое руководство. Изменение данных в мобильной службе]
-   [Практическое руководство. Привязка данных к пользовательскому интерфейсу][Практическое руководство. Привязка данных к пользовательскому интерфейсу]
-   [Практическое руководство. Проверка подлинности пользователей][Практическое руководство. Проверка подлинности пользователей]
-   [Практическое руководство. Обработка ошибок][Практическое руководство. Обработка ошибок]

<!--- [How to: Design unit tests] - [How to: Customize the client]     - [Customize request headers]     - [Customize data type serialization] - [Next steps][Next steps]-->

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

## <a name="Setup"></a>Настройка и необходимые компоненты

В этом руководстве предполагается, что вы создали мобильную службу и таблицу. Дополнительные сведения см. в разделе [Создание таблицы][Создание таблицы] или повторно используйте таблицу `ToDoItem`, созданную в учебнике [Начало работы с мобильными службами][краткое руководство по мобильным службам]. В примерах данного раздела используется таблица с именем `ToDoItem`, которая имеет следующие столбцы:

-   `id`
-   `text`
-   `complete`
-   `duration`

Если вы в первый раз создаете приложение для iOS, добавьте `WindowsAzureMobileServices.framework` в параметр [**Связать двоичные файлы с библиотеками**][**Связать двоичные файлы с библиотеками**] приложения. На этом этапе нажмите "Добавить другие…", перейдите в расположение загруженного пакета SDK мобильных служб Microsoft Azure и выберите это расположение.

Кроме того, необходимо добавить следующую ссылку в соответствующие файлы или PCH-файл приложения.

    #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

## <a name="create-client"></a><span class="short-header"> Создание клиента</span>Практическое руководство. Создание клиента мобильных служб

Следующий код создает объект клиента мобильной службы, используемый для доступа к мобильной службе.

    MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

В приведенном выше коде замените `MobileServiceUrl` и `AppKey` на URL-адрес мобильной службы и ключ приложения мобильной службы. Чтобы определить эти параметры для мобильной службы, на портале управления выберите мобильную службу, а затем щелкните **Панель мониторинга**.

Можно также создать клиент на основе объекта **NSURL**, который представляет URL-адрес службы, как показано ниже:

    MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];      

## <a name="table-reference"></a><span class="short-header">Создание ссылки на таблицу</span>Практическое руководство. Создание ссылки на таблицу

Для получения данных из вашей мобильной связи необходимо получить ссылку на таблицу, элементы которой требуется запрашивать, обновлять или удалять. В следующем примере `ToDoItem` является именем таблицы:

    MSTable *table = [client tableWithName:@"ToDoItem"]; 

## <a name="querying"></a><span class="short-header">Запрос данных</span>Практическое руководство. Запрос данных из мобильной службы

После создания объекта MSTable можно создать запрос. Следующий простой запрос возвращает все элементы таблицы ToDoItem.

    [table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
            for(NSDictionary *item in items) {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
            }
        }
    }];

Обратите внимание, что в этом случае мы просто записываем текст задачи в журнал.

В обратном вызове предоставляются следующие параметры:

-   *items*: массив **NSArray** записей, соответствующих запросу.
-   *totalCount*: общее число элементов на всех страницах запроса, а не только те, которые возвращаются на текущей странице. Это значение равно -1, если вы явно не запросили общее число в запросе. Дополнительные сведения см. в разделе [Возврат данных на страницах][Возврат данных на страницах].
-   *error*: любая произошедшая ошибка; в противном случае — `nil`.

### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Для фильтрации результатов можно использовать различные способы.

Самый распространенный из них — использование NSPredicate для фильтрации результатов.

    [table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

Следующий предикат возвращает только незаполненные элементы в таблице ToDoItem:

    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
    [table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
            for(NSDictionary *item in items) {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
            }
        }
    }];

Отдельную запись можно получить с помощью ее идентификатора.

    [table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
        }
    }];

Обратите внимание, что в этом случае параметры обратного вызова немного отличаются. Вместо получения массива результатов и количества (необязательно) возвращается только одна запись.

### <a name="query-object"></a>Использование объекта MSQuery

Используйте объект **MSQuery** для более сложных запросов, чем простая фильтрация строк, например, изменение порядка сортировки результатов или ограничение количества возвращаемых записей данных. В двух следующих примерах показано, как создать экземпляр объекта MSQuery:

    MSQuery *query = [table query]; 

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

Объект MSQuery позволяет контролировать следующие настройки запросов:

-   указывать порядок возвращаемых результатов;
-   ограничивать возвращаемые поля;
-   ограничивать количество возвращаемых записей;
-   указывает, следует ли включить общее число в ответ;
-   указывать в запросе настраиваемые параметры строки запроса.

Вы можете уточнить запрос, применяя одну или несколько функций. После определения запроса он выполняется путем вызова функции **readWithCompletion**.

#### <a name="sorting"></a>Сортировка возвращаемых данных

Следующие функции используются для указания полей, используемых для сортировки:

    -(void) orderByAscending:(NSString *)field
    -(void) orderByDescending:(NSString *)field

Этот запрос сортирует результаты сначала по длительности, а затем по состоянию завершения задачи:

    [query orderByAscending:@"duration"];
    [query orderByAscending:@"complete"];
    [query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        //code to parse results here
    }]; 

#### <a name="paging"></a>Возврат данных на страницах

Мобильные службы ограничивают количество записей, возвращаемых в одном ответе. Чтобы контролировать число записей, отображаемых для пользователей, необходимо реализовать систему разбиения на страницы. Разбиение по страницам выполняется с помощью следующих трех свойств объекта **MSQuery**:

-   `BOOL includeTotalCount`
-   `NSInteger fetchLimit`
-   `NSInteger fetchOffset`

В следующем примере простая функция запрашивает 20 записей с сервера и затем добавляет их в локальную коллекцию предварительно загруженных записей:

    - (bool) loadResults() {
        MSQuery *query = [table query];

        query.includeTotalCount = YES;
        query.fetchLimit = 20;
        query.fetchOffset = self.loadedItems.count;

        [query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
            if(!error) {
                // Add the items to our local copy
                [self.loadedItems addObjectsFromArray:items];       

                // Set a flag to keep track if there are any additional records we need to load
                self.moreResults = (self.loadedItems.count < totalCount);
            }
        }];
    }

#### <a name="selecting"></a>Ограничение возвращаемых полей

Для ограничения полей, возвращаемых из запроса, просто укажите имена нужных полей, в свойстве **selectFields**. Следующий пример возвращает только текст и заполненные поля:

    query.selectFields = @[@"text", @"completed"];

#### <a name="parameters"></a>Указание дополнительных параметров строки запроса

Клиентская библиотека позволяет включать в запрос к серверу дополнительные параметры строки запроса. Эти параметры могут затребоваться серверными скриптами. Следующий пример добавляет в запрос два параметра строки запроса:

    query.parameters = @{
        @"myKey1" : @"value1",
        @"myKey2" : @"value2",
    };

Эти параметры добавляются в универсальный код ресурса (URI) запроса как `myKey1=value1&myKey2=value2`.
Дополнительные сведения см. в разделе [Доступ к настраиваемым параметрам][Доступ к настраиваемым параметрам].

## <a name="inserting"></a><span class="short-header">Вставка данных</span>Практическое руководство. Вставка данных в мобильную службу

Для вставки новой строки в таблицу создайте объект [NSDictionary][NSDictionary] и передайте его в функцию insert. Следующий код вставляет новый элемент списка дел в таблицу:

    NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified 
        // data compared to what was passed to the server.
    }]; 

Мобильные службы поддерживают уникальные настраиваемые строковые значения идентификатора таблицы. Это позволяет приложениям использовать настраиваемые значения, например, адреса электронной почты или имена пользователей, для столбца идентификатора таблицы мобильных служб. Например, если требуется идентифицировать каждую запись по адресу электронной почты, можно использовать следующий объект JSON.

    NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified 
        // data compared to what was passed to the server.
    }]; 

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

Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе полей объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема][Динамическая схема].

## <a name="modifying"></a><span class="short-header">Изменение данных</span>Практическое руководство. Изменение данных в мобильной службе

Чтобы обновить существующий объект, измените элемент, возвращенный предыдущим запросом, и вызовите функцию **update**.

    NSMutableDictionary *item = [self.results.item objectAtIndex:0];
    [item setObject:@YES forKey:@"complete"];
    [table update:item completion:^(NSDictionary *item, NSError *error) {
        //handle errors or any additional logic as needed
    }];

При обновлении необходимо указать только обновляемое поле, а также идентификатор строки, как в следующем примере:

    [table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @YES} completion:^(NSDictionary *item, NSError *error) {
        //handle errors or any additional logic as needed
    }];

Чтобы удалить элемент из таблицы, просто передайте нужный элемент методу delete, как показано ниже:

    [table delete:item completion:^(id itemId, NSError *error) {
        //handle errors or any additional logic as needed
    }];

Можно также просто удалить запись, непосредственно используя ее идентификатор, как показано в следующем примере.

    [table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
        //handle errors or any additional logic as needed
    }]; 

Обратите внимание, что при обновлении и удалении необходимо по крайней мере задать атрибут `id`.

## <a name="authentication"></a><span class="short-header">Проверка подлинности</span>Практическое руководство. Проверка подлинности пользователей

В мобильных службах можно использовать следующие поставщики удостоверений для проверки подлинности пользователей:

-   Facebook
-   Google
-   Учетная запись Майкрософт
-   Twitter
-   Azure Active Directory

Дополнительные сведения о настройке поставщика удостоверений см. в статье [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности].

Мобильные службы поддерживают следующие два процесса проверки подлинности:

-   В процессе входа, который управляется сервером, мобильные службы контролируют вход в систему от лица вашего приложения. Клиентская библиотека отображает страницу входа определенного поставщика, а мобильные службы выполняют проверку подлинности с выбранным поставщиком.

-   В процессе входа, который управляется клиентом, приложение должно запросить маркер у поставщика удостоверений и затем предоставить его мобильным службам для проверки подлинности.

После успешной проверки подлинности возвращается объект пользователя, содержащий назначенное значение идентификатора пользователя и маркер проверки подлинности. Этот идентификатор пользователя можно применять в серверных скриптах для проверки или изменения запросов. Дополнительные сведения см. в разделе [Использование скриптов для авторизации пользователей][Использование скриптов для авторизации пользователей]. Сам маркер может безопасно кэшироваться для последующих попыток входа.

Можно также задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Дополнительные сведения см. в разделе [Разрешения][Разрешения].

### Управляемый сервером вход

Ниже приведен пример того, как войти в систему с использованием учетной записи Майкрософт. Этот код может быть вызван в ViewDidLoad контроллера или запущен вручную в UIButton. При этом будет показан стандартный пользовательский интерфейс для входа в поставщик удостоверений.

    [client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
        completion:^(MSUser *user, NSError *error) {
            NSString *msg;
            if(error) {
                msg = [@"An error occured: " stringByAppendingString:error.description];
            } else {
                msg = [@"You are logged in as " stringByAppendingString:user.userId];
            }
        
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                  message:msg 
                                  delegate:nil 
                                  cancelButtonTitle:@"OK" 
                                  otherButtonTitles: nil];
            [alert show];
    }]; 

Примечание. Если используется поставщик удостоверений, отличный от учетной записи Майкрософт, измените значение, передаваемое в метод login выше, на одно из следующих: `facebook`, `twitter`, `google` или `windowsazureactivedirectory`.

Также можно получить ссылку на MSLoginController и отобразить ее с помощью следующего кода:

    -(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### Управляемый клиентом вход (единый вход)

Существуют случаи, когда процесс входа выполняется вне клиента мобильных служб. Это может потребоваться для реализации функции единого входа или когда приложение должно обращаться к поставщику удостоверений напрямую, чтобы получить сведения о пользователе. В этих случаях можно выполнить вход в мобильные службы, предоставив маркер, полученный независимо от поддерживаемого поставщика удостоверений.

В следующем примере для реализации единого входа для приложений iOS используется пакет [Live Connect SDK][Live Connect SDK].

    [client loginWithProvider:@"microsoftaccount" 
        token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
        completion:^(MSUser *user, NSError *error) {
            self.loggedInUser = user;
            NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                message:msg 
                delegate:nil 
                cancelButtonTitle:@"OK" 
                otherButtonTitles: nil];
            [alert show];
    }];

В этом коде предполагается, что вы ранее создали экземпляр **LiveConnectClient** с именем `liveClient` в контроллере, а пользователь выполнил вход.

### <a name="caching-tokens"></a>Практическое руководство. Кэширование маркеров проверки подлинности

Чтобы пользователи не проходили проверку подлинности при каждом запуске приложения, можно кэшировать текущее удостоверение пользователя после входа. Затем эту информацию можно использовать, чтобы напрямую создать пользователя пи пропустить процесс входа в систему. Для этого необходимо сохранить идентификатор пользователя и маркер проверки подлинности локально. В следующем примере токен безопасно кэшируется в [KeyChain]:

    - (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
    {
        NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
        [query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
        [query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];
        
        if(isSearch) {
            // Use the proper search constants, return only the attributes of the first match.
            [query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
            [query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
            [query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
        }

        return query;
    }

    - (IBAction)loginUser:(id)sender {
        NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
        CFDictionaryRef cfresult;

        OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
        if (status == noErr) {
            NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;
            
            //create an MSUser object
            MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
            NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
            user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
            [self.todoService.client setCurrentUser:user];
            
        } else if (status == errSecItemNotFound) {
            //we need to log the user in
            [self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
                completion:^(MSUser *user, NSError *error) {
                    NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
                    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                            message:msg delegate:nil 
                                            cancelButtonTitle:@"OK" 
                                            otherButtonTitles: nil];
                    [alert show];
                
                    //save the user id and token to the KeyChain
                    NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client 
                                                            andIsSearch:NO];
                    [newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
                    [newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding] 
                                                    forKey:(__bridge id)kSecValueData];
                 
                    OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
                    if(status != errSecSuccess) {
                        //handle error as needed
                        NSAssert(NO, @"Error caching password.");
                    }
            }];
        }   

<div class="dev-callout"><strong>Примечание.</strong>
<p>Маркеры являются конфиденциальными данными и должны храниться в зашифрованном виде на случай потери или кражи устройства.</p>
</div>

При использовании кэшированного маркера пользователям не придется снова входить в систему до истечения срока действия маркера. Когда пользователь пытается войти в систему с просроченным маркером, возвращается ответ 401, "Нет доступа". На этом этапе пользователь должен войти в систему, чтобы получить новый маркер, который снова может быть кэширован. Чтобы не писать код, обрабатывающий просроченные маркеры, когда приложение вызывает мобильную службу, можно использовать фильтры. Фильтры позволяют перехватывать вызовы и ответы мобильной службы. Код фильтра проверяет наличие ответа 401, запускает процесс входа в систему, если срок действия токена истек, и затем повторяет запрос, вызвавший ответ 401. Дополнительные сведения см. разделе [Обработка токенов с истекшим сроком действия][Обработка токенов с истекшим сроком действия].

## <a name="errors"></a><span class="short-header">Обработка ошибок</span>Практическое руководство. Обработка ошибок

При вызове мобильной службы блок завершения содержит параметр `NSError *error`. При возникновении ошибки в этом параметр возвращается ненулевое значение. В коде необходимо проверить этот параметр и обработать ошибку по мере необходимости.

Если произошла ошибка, можно получить дополнительные сведения, добавив в код файл MSError.h.

    #import <WindowsAzureMobileServices/MSError.h>

Этот файл определяет следующие константы, которые можно использовать для доступа к дополнительным данным из `[error userInfo]`:

-   **MSErrorResponseKey**: данные ответа HTTP, связанного с ошибкой.
-   **MSErrorRequestKey**: данные ответа HTTP, связанного с ошибкой.

Кроме того, для каждого кода ошибки определяется константа. Описание этих кодов можно найти в файле MSError.h.

Пример выполнения проверки и обработки любых см. в статье [Проверка и изменение данных в мобильных службах с помощью серверных скриптов][Проверка и изменение данных в мобильных службах с помощью серверных скриптов]. В этом разделе серверная проверка реализуется с помощью серверных скриптов. При отправке недопустимых данных возвращается ответ с сообщением об ошибке, который обрабатывается клиентом.

<!-- 
<h2><a name="#unit-testing"></a><span class="short-header">Designing tests</span>How to: Design unit tests</h2>  _(Optional) This section shows how to write unit test when using the client library (info from Yavor)._  <h2><a name="#customizing"></a><span class="short-header">Customizing the client</span>How to: Customize the client</h2>  _(Optional) This section shows how to send customize client behaviors._  ###<a name="custom-headers"></a>How to: Customize request headers  _(Optional) This section shows how to send custom request headers._  For more information see, New topic about processing headers in the server-side.  ###<a name="custom-serialization"></a>How to: Customize serialization  _(Optional) This section shows how to use attributes to customize how data types are serialized._  For more information see, New topic about processing headers in the server-side.  ## <a name="next-steps"></a>Next steps -->




  [пакет Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [iOS SDK]: https://developer.apple.com/xcode
  [краткое руководство по мобильным службам]: /ru-ru/develop/mobile/tutorials/get-started-ios
  [Что такое мобильные службы?]: #what-is
  [Основные понятия]: #concepts
  [Настройка и необходимые компоненты]: #Setup
  [Практическое руководство. Создание клиента мобильных служб]: #create-client
  [Практическое руководство. Создание ссылки на таблицу]: #table-reference
  [Практическое руководство. Запрос данных от мобильной службы]: #querying
  [Фильтрация возвращаемых данных]: #filtering
  [Использование объекта MSQuery]: #query-object
  [Выбор определенных столбцов]: #selecting
  [Практическое руководство. Вставка данных в мобильную службу]: #inserting
  [Практическое руководство. Изменение данных в мобильной службе]: #modifying
  [Практическое руководство. Привязка данных к пользовательскому интерфейсу]: #binding
  [Практическое руководство. Проверка подлинности пользователей]: #authentication
  [Практическое руководство. Обработка ошибок]: #errors
  [Создание таблицы]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193162.aspx
  [**Связать двоичные файлы с библиотеками**]: https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html
  [Возврат данных на страницах]: #paging
  [Доступ к настраиваемым параметрам]: /ru-ru/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
  [NSDictionary]: http://go.microsoft.com/fwlink/p/?LinkId=301965
  [Управляющие коды ASCII C0 и C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
  [Интерфейс командной строки для управления таблицами мобильных служб]: http://www.windowsazure.com/ru-ru/manage/linux/other-resources/command-line-tools/#Mobile_Tables
  [Динамическая схема]: http://go.microsoft.com/fwlink/p/?LinkId=296271
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
  [Использование скриптов для авторизации пользователей]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios
  [Разрешения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193161.aspx
  [Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
  [Обработка токенов с истекшим сроком действия]: http://go.microsoft.com/fwlink/p/?LinkId=301955
  [Проверка и изменение данных в мобильных службах с помощью серверных скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios
