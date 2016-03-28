<properties
	pageTitle="Использование пакета iOS SDK для мобильных приложений Azure"
	description="Использование пакета iOS SDK для мобильных приложений Azure"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

# Использование клиентской библиотеки iOS для мобильных приложений Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

В данном руководстве показано, как реализовать типичные сценарии с использованием последней версии [пакета iOS SDK для мобильных приложений Azure](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Если вы не знакомы с мобильными приложениями Azure, изучите статью [Быстрый запуск мобильного приложения Azure], чтобы создать серверную часть и таблицу, а также скачать предварительно собранный проект Xcode для iOS. В данном руководстве мы сосредоточимся на клиентской части пакета iOS SDK. Дополнительные сведения о серверном пакете SDK для .NET см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## Справочная документация

Справочная документация по пакету SDK для клиента iOS находится здесь: [Справочник по клиенту iOS мобильных приложений Azure](http://azure.github.io/azure-mobile-services/iOS/v3/).

##<a name="Setup"></a>Настройка и необходимые компоненты

В данном руководстве предполагается, что вы уже создали серверную часть с таблицей. В этом руководстве предполагается, что в таблице используется та же схему, что и в таблицах, приведенных в этих учебниках. Кроме того, в данном руководстве предполагается, что в коде можно ссылаться на `MicrosoftAzureMobile.framework` и импортировать `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Создание клиента

Чтобы получить доступ к серверной части мобильных приложений Azure в вашем проекте, создайте `MSClient`. Замените `AppUrl` на URL-адрес приложения. Параметры `gatewayURLString` и `applicationKey` можно оставить пустыми. Если вы настроите шлюз для проверки подлинности, укажите в параметре `gatewayURLString` его URL-адрес.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Практическое руководство. Создание ссылки на таблицу

Для доступа к данным или их обновления создайте ссылку на таблицу серверной части. Замените `TodoItem` на имя таблицы.

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Практическое руководство. Запрос данных

Чтобы создать запрос к базе данных, отправьте запрос объекта `MSTable`. Следующий запрос возвращает все элементы в `TodoItem` и регистрирует в журнале текст каждого из них.

**Objective-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Есть множество способов фильтрации результатов.

Для фильтрации данных с использованием предиката используйте `NSPredicate` и `readWithPredicate`. Следующие фильтры позволяют возвращать только незавершенные элементы Todo.

**Objective-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Практическое руководство. Использование MSQuery

Чтобы выполнить сложный запрос (в том числе на сортировку и подкачку), создайте объект `MSQuery` непосредственно или с помощью предиката:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` позволяет контролировать некоторые настройки запросов, включая следующие. Выполнение запроса `MSQuery` путем вызова в нем метода `readWithCompletion`, как показано в следующем примере.
* Задание порядка результатов
* Ограничение возвращаемых полей
* Ограничение количества возвращаемых записей
* Указание общего количества в ответе
* Указание в запросе настраиваемых параметров строки запроса
* Применение дополнительных функций


## <a name="sorting"></a>Практическое руководство. Сортировка данных с помощью MSQuery

На примере ниже показано, как сортировать результаты. Чтобы сначала отсортировать данные в порядке возрастания по полю `text`, а затем в порядке убывания по полю `completion`, вызовите `MSQuery`:

**Objective-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Практическое руководство. Ограничение возвращаемых полей и развертывание строковых параметров запроса с помощью MSQuery

Чтобы ограничить поля, возвращаемые в запросе, укажите имена полей в свойстве **selectFields**. В следующем примере возвращается только текст и заполненные поля:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Чтобы включить дополнительные строковые параметры в запрос сервера (например, так как они используются в настраиваемом серверном скрипте), заполните `query.parameters` следующим образом:

**Objective-C**:

```
query.parameters = @{
	@"myKey1" : @"value1",
	@"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

##<a name="inserting"></a>Практическое руководство. Вставка данных

Чтобы вставить новую строку таблицы, создайте `NSDictionary` и вызовите `table insert`. Мобильные службы автоматически создают столбцы на основе `NSDictionary`, если не отключена [динамическая схема].

Если не указано значение параметра `id`, на внутреннем сервере автоматически создается уникальный идентификатор. Укажите собственное значение параметра `id`, чтобы в качестве идентификатора использовать адреса электронной почты, имена пользователей или настраиваемые значения. Наличие собственного идентификатора может упростить операции соединения и бизнес-логику базы данных.

`result` содержит новый элемент, который был вставлен. В зависимости от логики вашего сервера он может иметь дополнительные или измененные данные, по сравнению с теми, которые были переданы на сервер.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Практическое руководство. Изменение данных

Чтобы обновить существующую строку, измените элемент и вызовите `update`:

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Можно также указать идентификатор строки и обновляемое поле:

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

При обновлении необходимо по крайней мере задать атрибут `id`.

##<a name="deleting"></a>Практическое руководство. Удаление данных

Чтобы удалить элемент, вызовите `delete` с элементом:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Кроме того, для удаления элемента можно указать идентификатор строки:

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

При удалении необходимо по крайней мере задать атрибут `id`.

##<a name="customapi"></a>Практическое руководство. Вызов настраиваемого интерфейса API

С помощью настраиваемого API можно включить любые функциональные возможности серверной части. Они не должны совпадать с операциями с таблицами. Вы не только расширяете контроль над процессом обмена сообщениями, но также получаете возможность читать и задавать заголовки и изменять формат текста ответа. Сведения о создании настраиваемого API в серверной части см. в разделе [Настраиваемые интерфейсы API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Чтобы вызвать настраиваемый интерфейс API, вызовите `MSClient.invokeAPI`, как показано ниже. Содержимое запроса и ответа содержимого рассматривается как JSON. Чтобы использовать другие типы носителей, [воспользуйтесь другой перегрузкой `invokeAPI`](http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:).

Чтобы вместо запроса `POST` выполнить запрос `GET`, задайте параметру `HTTPMethod` значение `"GET"`, а параметру `body` — значение `nil` (поскольку запросы GET не имеют текста сообщений). Если настраиваемый API поддерживает другие команды HTTP, измените `HTTPMethod` соответствующим образом.

**Objective-C**:
```
    [self.client invokeAPI:@"sendEmail"
                      body:@{ @"contents": @"Hello world!" }
                HTTPMethod:@"POST"
                parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
                   headers:nil
                completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                    if(error) {
                        NSLog(@"ERROR %@", error);
                    } else {
                        // Do something with result
                    }
                }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Использование шаблонов для отправки кроссплатформенных push- уведомлений

Для регистрации шаблонов просто передайте их в клиентское приложение с помощью метода **client.push registerDeviceToken**.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	}
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Шаблоны будут относиться к типу NSDictionary и могут содержать несколько шаблонов в следующем формате:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Обратите внимание, что все теги будут удалены для безопасности. В статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) показано, как добавлять теги для установки устройства или шаблоны в пределах установки.

Для отправки уведомлений с использованием зарегистрированных шаблонов используйте [API концентраторов уведомлений](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="errors"></a>Практическое руководство. Обработка ошибок

При вызове мобильной службы блок завершения содержит параметр `NSError`. Если возникает ошибка, этот параметр не является пустым. В коде необходимо проверить этот параметр и обработать ошибку по мере необходимости, как показано в примерах кода выше.

Файл [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) определяет константы `MSErrorResponseKey`, `MSErrorRequestKey` и `MSErrorServerItemKey`, что позволяет получить дополнительные данные об этой ошибке следующим образом:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Кроме того, этот файл определяет константы для каждого кода ошибки, что можно использовать, например, следующим образом:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Практическое руководство. Проверка подлинности пользователей с помощью библиотеки проверки подлинности Active Directory

Библиотеку проверки подлинности Active Directory (ADAL) можно использовать для входа пользователей в приложение с помощью Azure Active Directory. Этот подход является более предпочтительным, чем использование методов `loginAsync()`, так как он обеспечивает более удобный интерфейс входа для пользователя и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью AAD, следуя указаниям в руководстве [Настройка приложения службы приложений для использования службы входа Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Обязательно выполните дополнительный этап регистрации собственного клиентского приложения. Для iOS рекомендуется (хотя это не обязательно) следующий формат URI перенаправления: `<app-scheme>://<bundle-id>`. Дополнительные сведения см. в статье [Быстрый запуск ADAL iOS](active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem).

2. Установите ADAL с помощью Cocoapods. Измените файл Podfile: добавьте в него следующий код, заменив **YOUR-PROJECT** именем проекта Xcode:

		source 'https://github.com/CocoaPods/Specs.git'
		link_with ['YOUR-PROJECT']
		xcodeproj 'YOUR-PROJECT'
и Pod:

		pod 'ADALiOS'

3. С помощью приложения Terminal запустите `pod install` из каталога, содержащего проект, а затем откройте созданную рабочую область Xcode (не проект).

4. Добавьте в приложение приведенный ниже код, соответствующий используемому языку. В каждом коде выполните следующие замены.

* Замените текст **INSERT-AUTHORITY-HERE** именем клиента, в котором подготовлено приложение. Используйте следующий формат: https://login.windows.net/contoso.onmicrosoft.com. Это значение можно скопировать на вкладке "Домен" в Azure Active Directory на [классическом портале Azure].

* Замените текст **INSERT-RESOURCE-ID-HERE** идентификатором клиента для серверной части мобильного приложения. Это значение можно скопировать на портале в разделе **Настройки Azure Active Directory** на вкладке **Дополнительно**.

* Замените текст **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

* Замените текст **INSERT-REDIRECT-URI-HERE** конечной точкой сайта _/.auth/login/done_, используя схему HTTPS. Это значение должно быть аналогично _https://contoso.azurewebsites.net/.auth/login/done_.

**Objective-C**:

	#import <ADALiOS/ADAuthenticationContext.h>
	#import <ADALiOS/ADAuthenticationSettings.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*))completionBlock;
	{
	    NSString *authority = @"INSERT-AUTHORITY-HERE";
	    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
	    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
	    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
	    ADAuthenticationError *error;
	    ADAuthenticationContext authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
	    authContext.parentController = parent;
	    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
	    [authContext acquireTokenWithResource:resourceId
	                                 clientId:clientId
	                              redirectUri:redirectUri
	                          completionBlock:^(ADAuthenticationResult *result) {
	                              if (result.status != AD_SUCCEEDED)
	                              {
	                                  completionBlock(nil, result.error);;
	                              }
	                              else
	                              {
	                                  NSDictionary *payload = @{
	                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
	                                                            };
	                                  [client loginWithProvider:@"aad" token:payload completion:completionBlock];
	                              }
	                          }];
	}


**Swift**:

	// add the following imports to your bridging header:
	//     #import <ADALiOS/ADAuthenticationContext.h>
	//     #import <ADALiOS/ADAuthenticationSettings.h>

	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let authority = "INSERT-AUTHORITY-HERE"
		let resourceId = "INSERT-RESOURCE-ID-HERE"
		let clientId = "INSERT-CLIENT-ID-HERE"
		let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
		var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
		let authContext = ADAuthenticationContext(authority: authority, error: error)
		authContext.parentController = parent
		ADAuthenticationSettings.sharedInstance().enableFullScreen = true
		authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
		        if result.status != AD_SUCCEEDED {
		            completion(nil, result.error)
		        }
		        else {
		            let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
		            client.loginWithProvider("aad", token: payload, completion: completion)
		        }
    		}
	}


<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Быстрый запуск мобильного приложения Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[динамическая схема]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0316_2016-->