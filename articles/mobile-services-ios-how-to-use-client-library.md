<properties urlDisplayName="iOS Client Library" pageTitle="Использование клиентской библиотеки iOS - мобильные службы Azure" metaKeywords="Azure Mobile Services, Mobile Service iOS client library, iOS client library" description="Learn how to use the iOS client library for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the iOS client library for Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />




# Использование клиентской библиотеки iOS для мобильных служб
<div class="dev-center-tutorial-selector sublanding">
  <a href="/ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">Платформа .NET</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

В этом руководстве показано, как реализовать стандартные сценарии с использованием клиента iOS для мобильных служб Azure. Примеры написаны на Objective C, и им необходим пакет [Mobile Services SDK].  Для этого учебника также требуется [iOS SDK]. В сценариях описываются запросы данных, вставка, обновление и удаление данных, проверка подлинности пользователей и обработка ошибок. Впервые осваивая мобильные службы, следует вначале ознакомиться с [кратким руководством по мобильным службам][Начало работы с мобильными службами]. С помощью данного краткого руководства можно настроить учетную запись и создать первую мобильную службу.

## Оглавление

- [Что такое мобильные службы?][]
- [Основные понятия][]
- [Настройка и необходимые компоненты][]
- [Практическое руководство. Создание клиента мобильных служб][]
- [Практическое руководство. Создание ссылки на таблицу][]
- [Практическое руководство. Запрос данных от мобильной службы][]
	- [Фильтрация возвращаемых данных]
    - [Использование объекта MSQuery][	Практическое руководство. Использование MSQuery]
	- [Выбор определенных столбцов]
- [Практическое руководство. Вставка данных в мобильную службу]
- [Практическое руководство. Изменение данных в мобильной службе]
- [Практическое руководство. Привязка данных к пользовательскому интерфейсу]
- [Практическое руководство. Проверка подлинности пользователей]
- [Практическое руководство. Обработка ошибок]

<!--- [How to: Design unit tests]
- [How to: Customize the client]
	- [Customize request headers]
	- [Customize data type serialization]
- [Next steps][]-->

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Настройка и необходимые компоненты

В этом руководстве предполагается, что вы создали мобильную службу с таблицей.   Дополнительные сведения см. в разделе [Создание таблицы] или повторно используйте таблицу `ToDoItem`, созданную в учебнике [Начало работы с мобильными службами]. В примерах в этом разделе используется таблица с именем `ToDoItem`, которая содержит следующие столбцы:

+ `идентификатор`
+ `текст`
+ `завершение`
+ `длительность`


Если вы в первый раз создаете приложение для iOS, добавьте `WindowsAzureMobileServices.framework` в настройку приложения [**Связывание двоичных файлов с библиотеками**](https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html). На этом этапе нажмите "Добавить другие...", перейдите в расположение загруженного пакета SDK мобильных служб Microsoft Azure и выберите это расположение.

Кроме того, необходимо добавить следующую ссылку в соответствующие файлы или PCH-файл приложения.

	#import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

<h2><a name="create-client"></a>Практическое руководство. Создание клиента мобильных служб</h2>

Следующий код используется для создания объекта клиента мобильной службы, используемый для доступа к мобильной службе.

	MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

В приведенном выше коде замените `MobileServiceUrl` и `AppKey` на URL-адрес мобильной службы и ключ приложения мобильной службы. Чтобы определить эти параметры для мобильной службы, на портале управления Azure выберите мобильную службу, а затем щелкните **Панель мониторинга**.

Можно также создать клиент на основе объекта **NSURL**, который представляет URL-адрес службы, как показано ниже.

	MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];

<h2><a name="table-reference"></a>Практическое руководство. Создание ссылки на таблицу</h2>

Для получения данных из вашей мобильной связи необходимо получить ссылку на таблицу, элементы которой требуется запрашивать, обновлять или удалять. В следующем примере `ToDoItem` является именем таблицы:

	MSTable *table = [client tableWithName:@"ToDoItem"];


<h2><a name="querying"></a>Практическое руководство. Запрос данных от мобильной службы</h2>

После создания объекта MSTable можно создать запрос.   Следующий простой запрос возвращает все элементы в таблице ToDoItem.

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

+ _items_: массив **NSArray** записей, соответствующих запросу.
+ _totalCount_: общее число элементов на всех страницах запроса, а не только те, которые возвращаются на текущей странице. Это значение равно -1, если вы явно не запросили общее число в запросе. Дополнительные сведения см. в разделе [Возврат данных на страницах].
+ _error_: любая произошедшая ошибка; в противном случае - значение `nil`.

### <a name="filtering"></a>Практическое руководство. Данные, возвращенные фильтром

Для фильтрации результатов можно использовать различные способы.

Самый распространенный из них - использование NSPredicate для фильтрации результатов.

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

Обратите внимание, что в этом случае параметры обратного вызова немного отличаются.   Вместо получения массива результатов и необязательного счетчика просто возвращается одна запись.

### <a name="query-object"></a>Использование объекта MSQuery

Используйте объект **MSQuery** для более сложных запросов, чем простая фильтрация строк, например изменение порядка сортировки результатов или ограничение количества возвращаемых записей данных. В двух следующих примерах показано, как создать экземпляр объекта MSQuery.

    MSQuery *query = [table query];

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

Объект MSQuery позволяет контролировать следующие настройки запросов:

* указывать порядок возвращаемых результатов;
* ограничивать возвращаемые поля;
* ограничивать количество возвращаемых записей;
* указывает, следует ли включить общее число в ответ;
* указывать в запросе настраиваемые параметры строки запроса.

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

Мобильные службы ограничивают количество записей, возвращаемых в одном ответе. Для управления числом записей, отображаемых для пользователей, необходимо реализовать систему разбиения на страницы.   Разбиение по страницам выполняется с помощью следующих трех свойств объекта **MSQuery**:

+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`


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

Данные параметры добавляются в URI запроса как `myKey1=value1&myKey2=value2`.
Дополнительные сведения см. в разделе [Практическое руководство. Доступ к настраиваемым параметрам].

<h2><a name="inserting"></a>Практическое руководство. Вставка данных в мобильную службу</h2>

Для вставки новой строки в таблицу создайте объект [NSDictionary] и передайте его в функцию insert. Следующий код вставляет новый элемент списка дел в таблицу:

	NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Мобильные службы поддерживают уникальные пользовательские строковые значения для идентификатора таблицы. Это позволяет использовать в приложениях пользовательские значения, такие как адреса электронной почты или имена входа для столбца идентификатора в таблице мобильных служб. Например, чтобы обозначить каждую запись адресом электронной почты, можно применить следующий объект JSON.

	NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Если значение идентификатора строки не указано при вставке новых записей в таблицу, мобильные службы создают уникальное значение идентификатора.

Поддержка строковых идентификаторов предоставляет разработчикам следующие преимущества:

+ Идентификаторы можно создавать без обмена данными с базой данных.
+ Можно легко объединять записи из разных таблиц или баз данных.
+ Значения идентификаторов можно удобно интегрировать с логикой приложения.

Можно также использовать серверный скрипт для задания значений идентификаторов. В приведенном ниже примере скрипта создается пользовательский GUID и присваивается идентификатору новой записи. Это аналогично тому значению идентификатора, которое было бы создано мобильными службами, если бы не было передано значение для идентификатора записи.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Если приложение предоставляет значение для идентификатора, мобильные службы сохраняют его "как есть". (В том числе с начальными или конечными пробелами.) Пробелы не удаляются в значении.

Значение для `идентификатора` должно быть уникальным и не может включать символы из следующих наборов:

+ Управляющие символы: [0x0000-0x001F] и [0x007F-0x009F]. Дополнительные сведения см. в разделе [Управляющие коды ASCII C0 и C1].
+  Печатаемые символы: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Идентификаторы "." и ".."

Иным образом, можно использовать для таблиц целочисленные идентификаторы. Для этого необходимо создать таблицу с помощью команды `mobile table create` с параметром `--integerId`. Эта команда используется в интерфейсе командной строки (CLI) Azure. Дополнительные сведения об использовании CLI см. в разделе [CLI для управления таблицами мобильных служб].

Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе полей объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема].

<h2><a name="modifying"></a>Практическое руководство. Изменение данных в мобильной службе</h2>

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

<h2><a name="authentication"></a>Практическое руководство. Проверка подлинности пользователей</h2>

В мобильных службах можно использовать следующие поставщики удостоверений для проверки подлинности пользователей:

- Facebook
- Google
- Учетная запись Майкрософт
- Twitter
- Azure Active Directory

Дополнительные сведения о настройке поставщика удостоверений см. в статье [Приступая к работе с проверкой подлинности].

Мобильные службы поддерживают следующие два процесса проверки подлинности:

- В процессе входа, который управляется сервером, мобильные службы контролируют вход в систему от лица вашего приложения. Клиентская библиотека отображает страницу входа определенного поставщика, а мобильные службы выполняют проверку подлинности с выбранным поставщиком.

- В процессе входа, который управляется клиентом, приложение должно запросить маркер у поставщика удостоверений и затем предоставить его мобильным службам для проверки подлинности.

После успешной проверки подлинности возвращается объект пользователя, содержащий назначенное значение идентификатора пользователя и маркер проверки подлинности. Этот идентификатор пользователя можно применять в серверных скриптах для проверки или изменения запросов. Дополнительные сведения см. в разделе [Использование скриптов для авторизации пользователей]. Сам маркер может безопасно кэшироваться для последующих попыток входа.

Можно также задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Дополнительные сведения см. в разделе [Разрешения].

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

В следующем примере для реализации единого входа для приложений iOS используется пакет [Live Connect SDK].

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

This code assumes that you have previously created a Экземпляр **LiveConnectClient** с именем `liveClient` в контроллер, а пользователь выполнил вход.

###<a name="caching-tokens"></a>Практическое руководство. Токены проверки подлинности кэша

Чтобы пользователи проходили проверку подлинности в каждом запуске приложения, можно кэшировать текущее удостоверение пользователя после входа. Затем эти сведения можно использовать напрямую для создания пользователя и пропустить процесс входа в систему.   Для этого необходимо сохранить идентификатор пользователя и маркер проверки подлинности локально. В следующем примере маркер безопасно кэшируется в [KeyChain].

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

При использовании кэшированного маркера пользователям не придется снова входить в систему до истечения срока действия маркера. Когда пользователь пытается войти в систему с просроченным маркером, возвращается ответ 401, "Нет доступа". На этом этапе пользователь должен войти в систему, чтобы получить новый маркер, который снова может быть кэширован. Можно использовать фильтры, чтобы избежать необходимости писать код, обрабатывающий просроченные маркеры, когда приложение вызывает из мобильной службы.  Фильтры позволяют перехватывать вызовы и ответы из вашей мобильной службы. Код фильтра проверяет наличие ответа 401, запускает процесс входа в систему, если срок действия маркера истек, и затем повторяет запрос, вызвавший ответ 401. Дополнительные сведения см. в разделе [Обработка маркеров с истекшим сроком действия].

<h2><a name="errors"></a>Практическое руководство. Обработка ошибок</h2>

При вызове мобильной службы блок завершения содержит параметр `NSError *error`. При возникновении ошибки в этом параметр возвращается ненулевое значение. В коде необходимо проверить этот параметр и обработать ошибку по мере необходимости.

Если произошла ошибка, можно получить дополнительные сведения, добавив в код файл MSError.h.

    #import <WindowsAzureMobileServices/MSError.h>

Этот файл определяет следующие константы, которые можно использовать для доступа к дополнительным данным из `[error userInfo]`:

+ **MSErrorResponseKey**: данные ответа HTTP, связанного с ошибкой
* **MSErrorRequestKey**: данные ответа HTTP, связанного с ошибкой

Кроме того, для каждого кода ошибки определяется константа. Описание этих кодов можно найти в файле MSError.h.

Пример выполнения проверки и обработки любых данных см. в статье [Проверка и изменение данных в мобильных службах с помощью серверных скриптов]. В этом разделе серверная проверка реализуется с помощью серверных скриптов. При отправке недопустимых данных возвращается ответ с сообщением об ошибке, который обрабатывается клиентом.

<!--
<h2><a name="#unit-testing"></a>Практическое руководство. Разработка модульных тестов</h2>

_(Необязательно) В этом разделе показано, как написать модульный тест при использовании клиентской библиотеки (сведения из Yavor)._

<h2><a name="#customizing"></a>Практическое руководство. Настройка клиента</h2>

_(Необязательно) В этом разделе показано, как отправить данные о настраиваемом поведении клиента._

###<a name="custom-headers"></a>Практическое руководство. Настройка заголовков запроса

_(Необязательно) В этом разделе показано, как отправить настраиваемые заголовки запроса._

Дополнительные сведения см. в новом разделе об обработке заголовков на стороне сервера.

###<a name="custom-serialization"></a>Практическое руководство. Настраиваемая сериализация

_(Необязательно) В этом разделе показано, как использовать атрибуты для настройки способа сериализации типов данных._

Дополнительные сведения см. в новом разделе об обработке заголовков на стороне сервера.

## <a name="next-steps"></a>Дальнейшие действия
-->

<!-- Anchors. -->

[Что такое мобильные службы?]: #what-is
[Основные понятия]: #concepts
[Настройка и необходимые компоненты]: #Setup
[Практическое руководство. Создание клиента мобильных служб]: #create-client
[Практическое руководство. Создание ссылки на таблицу]: #table-reference
[Практическое руководство. Запрос данных от мобильной службы]: #querying
[Фильтрация возвращаемых данных]: #filtering
[Сортировка возвращаемых данных]: #sorting
[Возврат данных на страницах]: #paging
[Выбор определенных столбцов]: #selecting
[Практическое руководство. Привязка данных к пользовательскому интерфейсу]: #binding
[Практическое руководство. Вставка данных в мобильную службу]: #inserting
[Практическое руководство. Изменение данных в мобильной службе]: #modifying
[Практическое руководство. Проверка подлинности пользователей]: #authentication
[Кэширование маркеров проверки подлинности]: #caching-tokens
[Практическое руководство. Передача изображений и больших файлов]: #blobs
[Практическое руководство. Обработка ошибок]: #errors
[Практическое руководство. Разработка модульных тестов]: #unit-testing
[Практическое руководство. Настройка клиента]: #customizing
[Настройка заголовков запроса]: #custom-headers
[Настройка сериализации типа данных]: #custom-serialization
[Дальнейшие действия]: #next-steps
[Практическое руководство. Использование MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Проверка и изменение данных в мобильных службах с помощью серверных скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Пакет SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Обработка маркеров с истекшим сроком действия]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Разрешения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193161.aspx
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Динамическая схема]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[Практическое руководство. Доступ к настраиваемым параметрам]: /ru-ru/develop/mobile/how-to-guides/work-with-server-scripts#access-headers.
[Создание таблицы]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193162.aspx
[Объект NSDictionary]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[Управляющие коды ASCII C0 и C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Интерфейс командной строки для управления таблицами мобильных служб]: http://www.windowsazure.com/ru-ru/manage/linux/other-resources/command-line-tools/#Mobile_Tables
