---
title: Использование пакета iOS SDK для мобильных приложений Azure
description: Использование пакета iOS SDK для мобильных приложений Azure
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
ms.openlocfilehash: 63dd283605553297a7dc8feab90c8bcbd716d5de
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
ms.locfileid: "26739110"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Использование клиентской библиотеки iOS для мобильных приложений Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

В данном руководстве показано, как реализовать типичные сценарии с использованием последней версии [пакета SDK iOS для мобильных приложений Azure][1]. Если вы не знакомы с мобильными приложениями Azure, изучите статью [Быстрый запуск мобильного приложения Azure], чтобы создать серверную часть и таблицу, а также скачать предварительно собранный проект Xcode для iOS. В данном руководстве мы сосредоточимся на клиентской части пакета iOS SDK. Дополнительные сведения о серверном пакете SDK для внутреннего сервера см. практических руководствах по пакету SDK для сервера.

## <a name="reference-documentation"></a>Справочная документация
Справочная документация по пакету SDK для клиента iOS находится здесь: [Справочник по клиенту iOS мобильных приложений Azure][2].

## <a name="supported-platforms"></a>Поддерживаемые платформы
Пакет SDK для iOS поддерживает проекты Objective-C, проекты Swift 2.2 и проекты Swift 2.3 для iOS 8.0 или более поздней версии.

"Серверная" аутентификация использует WebView для представляемого пользовательского интерфейса.  Если устройство не может представить пользовательский интерфейс WebView, потребуется применить другие способы аутентификации, которые выходят за рамки данного продукта.  
Поэтому данный пакет SDK не подходит для различного рода часов и других устройств с аналогичными ограничениями.

## <a name="Setup"></a>Настройка и необходимые компоненты
В данном руководстве предполагается, что вы уже создали серверную часть с таблицей. В этом руководстве предполагается, что в таблице используется та же схему, что и в таблицах, приведенных в этих учебниках. Кроме того, в данном руководстве предполагается, что в коде можно ссылаться на `MicrosoftAzureMobile.framework` и импортировать `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Создание клиента
Чтобы получить доступ к серверной части мобильных приложений Azure в вашем проекте, создайте `MSClient`. Замените `AppUrl` на URL-адрес приложения. Параметры `gatewayURLString` и `applicationKey` можно оставить пустыми. Если вы настроите шлюз для проверки подлинности, укажите в параметре `gatewayURLString` его URL-адрес.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


## <a name="table-reference"></a>Практическое руководство. Создание ссылки на таблицу
Для доступа к данным или их обновления создайте ссылку на таблицу серверной части. Замените `TodoItem` на имя таблицы.

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


## <a name="querying"></a>Практическое руководство. Запрос данных
Чтобы создать запрос к базе данных, отправьте запрос объекта `MSTable` . Следующий запрос возвращает все элементы в `TodoItem` и регистрирует в журнале текст каждого из них.

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

## <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных
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

## <a name="query-object"></a>Практическое руководство. Использование MSQuery
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

`MSQuery` позволяет контролировать некоторые настройки запросов.

* Задание порядка результатов
* Ограничение возвращаемых полей
* Ограничение количества возвращаемых записей
* Указание общего количества в ответе
* Указание в запросе настраиваемых параметров строки запроса
* Применение дополнительных функций

Выполнение `MSQuery` запроса путем вызова `readWithCompletion` для объекта.

## <a name="sorting"></a>Практическое руководство. Сортировка данных с помощью MSQuery
На примере ниже показано, как сортировать результаты. Для сортировки по возрастанию значений поля text, а затем по убыванию по значению complete, вызовите `MSQuery` следующим образом.

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
Чтобы ограничить поля, возвращаемые в запросе, укажите имена полей в свойстве **selectFields** . Данный пример возвращает только текст и заполненные поля.

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

## <a name="paging"></a>Практическое руководство. Настройка размера страницы
При использовании мобильных приложений Azure размер страницы определяет количество записей, одновременно извлекаемых из таблиц серверной части. Вызов данных `pull` приведет к пакетному извлечению данных на основе этого размера страницы до тех пор, пока не будут извлечены все соответствующие записи.

Можно настроить размер страницы с помощью **MSPullSettings**, как показано ниже. Размер страницы по умолчанию равен 50, а в приведенном ниже примере он изменен на 3.

Можно изменить размер страницы для повышения производительности. Если имеется большое количество записей данных небольшого размера, то большой размер страницы уменьшит число круговых путей сервера.

Этот параметр определяет размер страницы только на стороне клиента. Если клиент запрашивает больший размер страницы, чем поддерживает серверная часть мобильных приложений, то используется максимальный размер страницы, допускаемый серверной частью.

Этот параметр также определяет *число* записей данных, а не *размер в байтах*.

Если увеличить размер страницы клиента, также следует увеличить размер страницы на сервере. Инструкции см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

**Objective-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```


**Swift**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Практическое руководство. Вставка данных
Чтобы вставить новую строку таблицы, создайте `NSDictionary` и вызовите `table insert`. Если включена [динамическая схема], то серверная часть мобильной службы службы приложений Azure автоматически создает столбцы на основе `NSDictionary`.

Если не указано значение параметра `id` , на внутреннем сервере автоматически создается уникальный идентификатор. Укажите собственное значение параметра `id` , чтобы в качестве идентификатора использовать адреса электронной почты, имена пользователей или настраиваемые значения. Наличие собственного идентификатора может упростить операции соединения и бизнес-логику базы данных.

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

## <a name="modifying"></a>Практическое руководство. Изменение данных
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

При обновлении необходимо по крайней мере задать атрибут `id` .

## <a name="deleting"></a>Практическое руководство. Удаление данных
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

При удалении необходимо по крайней мере задать атрибут `id` .

## <a name="customapi"></a>Практическое руководство. Вызов настраиваемого интерфейса API
С помощью настраиваемого API можно включить любые функциональные возможности серверной части. Они не должны совпадать с операциями с таблицами. Вы не только расширяете контроль над процессом обмена сообщениями, но также получаете возможность читать и задавать заголовки и изменять формат текста ответа. Сведения о создании настраиваемого API в серверной части см. в разделе [Настраиваемые интерфейсы API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis).

Для вызова настраиваемого API вызовите `MSClient.invokeAPI`. Содержимое запроса и ответа содержимого рассматривается как JSON. Чтобы использовать другие типы носителей, [воспользуйтесь другой перегрузкой `invokeAPI`][5].  Чтобы вместо запроса `POST` выполнить запрос `GET`, задайте параметру `HTTPMethod` значение `"GET"`, а параметру `body` — значение `nil` (поскольку запросы GET не имеют текста сообщений). Если настраиваемый API поддерживает другие команды HTTP, измените `HTTPMethod` соответствующим образом.

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

## <a name="templates"></a>Использование шаблонов для отправки кроссплатформенных push- уведомлений
Чтобы зарегистрировать шаблоны, передайте их с помощью метода **client.push registerDeviceToken** в свое клиентское приложение.

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

Шаблоны относятся к типу NSDictionary и могут содержать несколько шаблонов в следующем формате.

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Все теги будут удалены из запроса в целях безопасности.  В статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure][4] показано, как добавлять теги для установки или шаблоны в пределах установки.  Для отправки уведомлений с использованием зарегистрированных шаблонов используйте [API центров уведомлений][3].

## <a name="errors"></a>Практическое руководство. Обработка ошибок
При вызове серверной части мобильной службы службы приложений Azure блок завершения содержит параметр `NSError`. Если возникает ошибка, этот параметр не является пустым. В коде необходимо проверить этот параметр и обработать ошибку по мере необходимости, как показано во фрагментах кода выше.

Файл [`<WindowsAzureMobileServices/MSError.h>`][6] определяет константы `MSErrorResponseKey`, `MSErrorRequestKey` и `MSErrorServerItemKey`. Вот как можно получить дополнительные данные об ошибке.

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Кроме того, этот файл определяет константы для каждого кода ошибки.

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Практическое руководство. Проверка подлинности пользователей с помощью библиотеки проверки подлинности Active Directory
Библиотеку проверки подлинности Active Directory (ADAL) можно использовать для входа пользователей в приложение с помощью Azure Active Directory. Клиентский поток аутентификации с использованием пакета SDK поставщика удостоверений предпочтительнее использования метода `loginWithProvider:completion:` .  Клиентский поток аутентификации обеспечивает более удобный пользовательский интерфейс входа и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью AAD, следуя указаниям в руководстве [Настройка приложения службы приложений для использования службы входа Azure Active Directory][7]. Обязательно выполните дополнительный этап регистрации собственного клиентского приложения. Для iOS мы рекомендуем использовать универсальный код ресурса (URI) перенаправления следующего вида: `<app-scheme>://<bundle-id>`. Чтобы узнать больше, ознакомьтесь с [кратким руководством по ADAL для iOS][8].
2. Установите ADAL с помощью Cocoapods. Измените файл Podfile: добавьте в него следующее определение, заменив **YOUR-PROJECT** именем проекта Xcode.

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   и Pod:

        pod 'ADALiOS'
3. С помощью приложения Terminal запустите `pod install` из каталога, содержащего проект, а затем откройте созданную рабочую область Xcode (не проект).
4. Добавьте в приложение приведенный ниже код, соответствующий используемому языку. Выполните следующие замены:

   * Замените строку **INSERT-AUTHORITY-HERE** именем клиента, в котором подготовлено приложение. Формат должен быть следующим: https://login.microsoftonline.com/contoso.onmicrosoft.com. Это значение можно скопировать на вкладке "Домен" в разделе Azure Active Directory на [портале Azure].
   * Замените текст **INSERT-RESOURCE-ID-HERE** идентификатором клиента для серверной части мобильного приложения. Идентификатор клиента можно скопировать на портале в разделе **Настройки Azure Active Directory** на вкладке **Дополнительно**.
   * Замените текст **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.
   * Замените текст **INSERT-REDIRECT-URI-HERE** конечной точкой сайта */.auth/login/done* , используя схему HTTPS. Это значение должно быть похоже на *https://contoso.azurewebsites.net/.auth/login/done*.

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
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
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
    //        #import <ADALiOS/ADAuthenticationContext.h>
    //        #import <ADALiOS/ADAuthenticationSettings.h>

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

## <a name="facebook-sdk"></a>Практическое руководство: проверка подлинности пользователей с помощью пакета SDK Facebook для iOS
Пакет SDK Facebook для iOS можно использовать для входа пользователей в приложение с помощью Facebook.  Использование клиентского потока аутентификации предпочтительнее использования метода `loginWithProvider:completion:` .  Клиентский поток аутентификации обеспечивает более удобный пользовательский интерфейс входа и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью Facebook, следуя указаниям в руководстве [Как настроить приложение службы приложений для использования имени для входа Facebook][9].
2. Установите пакет SDK Facebook для iOS, как описано в документе [Facebook SDK for iOS - Getting Started][10] (Пакет SDK Facebook для iOS. Приступая к работе). Вместо создания приложения можно добавить платформу iOS в имеющуюся регистрацию.
3. Документация Facebook содержит код Objective-C в делегате приложения. При использовании **Swift** можно использовать следующие переводы для AppDelegate.swift.

        // Add the following import to your bridging header:
        //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }
4. Кроме добавления в проект `FBSDKCoreKit.framework` необходимо таким же образом добавить ссылку на `FBSDKLoginKit.framework`.
5. Добавьте в приложение приведенный ниже код, соответствующий используемому языку.

**Objective-C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {        
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**:

    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Практическое руководство: проверка подлинности пользователей с помощью структуры Twitter для iOS
Структуру для iOS можно использовать для входа пользователей в приложение с помощью Twitter. Использование клиентского потока аутентификации является более предпочтительным, чем использование метода `loginWithProvider:completion:` , так как он обеспечивает более удобный пользовательский интерфейс входа и позволяет выполнять дополнительную настройку.

1. Настройте серверную часть мобильного приложения для входа с помощью Twitter, следуя указаниям в учебнике [Как настроить приложение службы приложений для использования имени для входа Twitter](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) .
2. Добавьте структуру в проект, как описано в документе [Fabric for iOS - Getting Started] (Структура для iOS. Приступая к работе), а также установите TwitterKit.

   > [!NOTE]
   > По умолчанию структура создаст приложение Twitter. Чтобы избежать создания этого приложения, можно зарегистрировать ключ клиента и секрет клиента, которые вы создали ранее, с помощью фрагментов кода ниже.    Кроме того, можно заменить значения ключа клиента и секрета клиента, которые предоставляются службе приложений, на значения, отображаемые на [панели мониторинга структуры]. Если выбран этот параметр, не забудьте задать URL-адрес обратного вызова для значения заполнителя, например `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.
   >
   >

    Если вы решили использовать ранее созданные секреты, добавьте в делегат приложения следующий фрагмент кода.

    **Objective-C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }

    **Swift**:

        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
3. Добавьте в приложение приведенный ниже код, соответствующий используемому языку.

**Objective-C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Практическое руководство: проверка подлинности пользователей с помощью пакета SDK Google Sign-In для iOS
Пакет SDK Google Sign-In для iOS можно использовать для входа пользователей в приложение с помощью учетной записи Google.  Недавно компания Google объявила о внесении изменений в свои политики безопасности OAuth.  Эти изменения политик в дальнейшем потребуют использовать пакет SDK для Google.

1. Настройте серверную часть мобильного приложения для входа с помощью Google Sign-In, следуя указаниям в руководстве [Как настроить приложение службы приложений для использования имени для входа Google](../app-service/app-service-mobile-how-to-configure-google-authentication.md) .
2. Установите пакет SDK Google для iOS, следуя инструкциям в статье [Google Sign-In for iOS - Start integrating](https://developers.google.com/identity/sign-in/ios/start-integrating) (Google Sign-In для iOS — начало интеграции). Раздел "Authenticate with a Backend Server" (Аутентификация на внутреннем сервере) можно пропустить.
3. Добавьте в метод `signIn:didSignInForUser:withError:` делегата код, приведенный ниже (в соответствии с используемым языком).

**Objective-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };

        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

1. Добавьте также показанный ниже код в `application:didFinishLaunchingWithOptions:` в делегате приложения, заменив SERVER_CLIENT_ID тем же идентификатором, с помощью которого вы настроили службу приложений на этапе 1.

**Objective-C**:

         [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";

 **Swift**:

        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"


1. Добавьте приведенный ниже код в приложение в UIViewController, который реализует протокол `GIDSignInUIDelegate` (в соответствии с используемым языком).  Вы вышли перед повторным входом, и хотя вам не нужно повторно вводить учетные данные, вы увидите диалоговое окно согласия.  Вызывайте этот метод, только когда истек срок маркера сеанса.

   **Objective-C**:

       #import <Google/SignIn.h>
       // ...
       - (void)authenticate
       {
               [GIDSignIn sharedInstance].uiDelegate = self;
               [[GIDSignIn sharedInstance] signOut];
               [[GIDSignIn sharedInstance] signIn];
        }

   **Swift**:

       // ...
       func authenticate() {
           GIDSignIn.sharedInstance().uiDelegate = self
           GIDSignIn.sharedInstance().signOut()
           GIDSignIn.sharedInstance().signIn()
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
[портале Azure]: https://portal.azure.com/
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
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[панели мониторинга структуры]: https://www.fabric.io/home
[Fabric for iOS - Getting Started]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
