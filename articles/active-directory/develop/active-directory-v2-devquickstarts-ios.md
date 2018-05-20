---
title: Добавление функции входа в приложение iOS с помощью конечной точки Azure AD версии 2.0 | Документация Майкрософт
description: Узнайте, как создать приложение iOS, которое поддерживает вход пользователей в систему с помощью личной, рабочей и учебной учетной записи Майкрософт, используя сторонние библиотеки.
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7476417e6585976ea2404a83602a6d9aa77d9c7a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Добавление функции входа в приложение iOS, использующее стороннюю библиотеку и API Graph, с помощью конечной точки версии 2.0
Платформа Microsoft Identity использует открытые стандарты, такие как OAuth2 и OpenID Connect. Разработчики могут использовать любую библиотеку на свой выбор для интеграции с нашими службами. Чтобы помочь разработчикам с использованием нашей платформы с другими библиотеками, мы написали несколько подобных этому пошаговых руководств по настройке сторонних библиотеки для подключения к платформе Microsoft Identity. Большинство библиотек, в которых реализована [спецификация RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) , могут подключаться к платформе Microsoft Identity.

Используя приложение, создаваемое в этом пошаговом руководстве, пользователи смогут войти в инфраструктуру своей организации и найти там других пользователей с помощью API Graph.

Если вы еще не работали с OAuth2 или OpenID Connect, то вы не поймете большую часть примера конфигурации. Рекомендуем сначала ознакомиться со статьей [Протоколы версии 2.0 — поток кода авторизации OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

> [!NOTE]
> Чтобы использовать функции платформы, которые описаны в этих стандартах OAuth2 или OpenID Connect, такие как управление политиками условного доступа и управление политиками Intune, требуются библиотеки Microsoft Azure Identity с открытым кодом.
> 
> 

Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0.

> [!NOTE]
> Чтобы определить, следует ли вам использовать конечную точку 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Скачивание кода с сайта GitHub
Код в этом учебнике размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) или клонировать ее:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Можно также просто скачать пример и немедленно приступить к работе.

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>регистрация приложения;
Создайте новое приложение на [портале регистрации приложений](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или выполните подробные инструкции по [регистрации приложения с использованием конечной точки версии 2.0](active-directory-v2-app-registration.md). Не забудьте:

* Скопируйте назначенный вашему приложению **идентификатор приложения**. Он вскоре вам понадобится.
* Добавьте для приложения **мобильную** платформу.
* Скопируйте значение **URI перенаправления** с портала. Необходимо использовать стандартное значение `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Скачивание сторонней библиотеки NXOAuth2 и создание рабочей области
В этом пошаговом руководстве будет использоваться библиотека OAuth2Client с сайта GitHub. Это библиотека OAuth2 для Mac OS X и iOS (Cocoa и Cocoa Touch). В ее основе лежит черновая версия (10) спецификации OAuth2. Она реализует профиль собственного приложения и поддерживает конечную точку авторизации пользователей. Это все, что вам потребуется для интеграции с платформой Microsoft Identity.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Добавление библиотеки в проект с помощью CocoaPods
CocoaPods — это диспетчер зависимостей для проектов Xcode. Он автоматически управляет приведенными выше шагами установки.

```
$ vi Podfile
```
1. Добавьте в файл Podfile следующий код:
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. Скачайте файл Podfile с помощью CocoaPods. Будет создана новая рабочая область Xcode.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Изучение структуры проекта
В основе настроена следующая структура проекта.

* Основное представление с поиском имени участника-пользователя.
* Подробное представление с данными выбранного пользователя.
* Представление входа, которое позволяет пользователю входить в приложение для запроса Graph.

Мы будем использовать различные файлы, входящие в основу, для добавления аутентификации. Другие части кода, например код визуального элемента, не относятся к идентификации, но также предоставляются.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Настройка файла settings.plst в библиотеке
* В проекте QuickStart откройте файл `settings.plist` . Замените значения элементов в соответствующем разделе на значения, указанные на портале Azure. Код будет использовать эти значения при каждом использовании библиотеки аутентификации Active Directory.
  * Для `clientId` укажите скопированный на портале идентификатор клиента приложения.
  * Для `redirectUri` укажите URL-адрес перенаправления, предоставленный на портале.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Настройка библиотеки NXOAuth2Client в LoginViewController
Для библиотеки NXOAuth2Client необходимо задать некоторые значения. После выполнения этой задачи полученный токен можно будет использовать для вызова API Graph. Так как `LoginView` вызывается в любое время, когда требуется аутентификация, то имеет смысл поместить в этот файл значения конфигурации.

* Добавим несколько значений в файл `LoginViewController.m` , чтобы задать контекст для аутентификации и авторизации. Описание значений указано после кода.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Давайте взглянем на сведения о коде.

Первая строка — для `scopes`. Значение `User.Read` позволяет считывать базовый профиль вошедшего пользователя.

Дополнительные сведения о всех доступных областях действия см. в разделе [Microsoft Graph permission scopes](https://graph.microsoft.io/docs/authorization/permission_scopes) (Области действия разрешений Microsoft Graph).

Для `authURL`, `loginURL`, `bhh` и `tokenURL` следует использовать значения, заданные ранее. При использовании библиотек Microsoft Azure Identity с открытым кодом эти данные можно получить с помощью конечной точки метаданных. Мы уже сделали все за вас и извлекли эти значения.

Значение `keychain` — это контейнер, который будет использовать библиотека NXOAuth2Client, чтобы создать цепочку ключей для хранения токенов. Если вы хотите настроить единый вход для множества приложений, то можно указать одну и ту же цепочку ключей в каждом приложении и запросить ее использование в рамках объема обслуживания XCode. Это подробно описано в документации Apple.

Остальные значения требуются для использования библиотеки. Они необходимы для передачи значений в контекст.

### <a name="create-a-url-cache"></a>Создание кэша URL-адресов
В функции `(void)viewDidLoad()`, которая всегда вызывается после загрузки представления, следующий код обеспечивает кэш для использования.

Добавьте следующий код:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Создание веб-представления для входа в систему
Веб-представление может запрашивать у пользователя дополнительные факторы, например SMS-сообщение (если настроено), или возвращать ему сообщения об ошибках. Вы настроите веб-представление, а затем запрограммируете обработку обратных вызовов, которые будут выполняться в этом представлении, из службы идентификации.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Переопределение методов веб-представления для обработки проверки подлинности
Чтобы сообщить веб-представлению о том, что происходит, когда пользователю нужно выполнить вход, как обсуждалось ранее, можно вставить следующий код.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Написание кода для обработки результатов запроса OAuth2
Следующий код будет обрабатывать redirectURL, возвращаемый веб-представлением. Если аутентификация не будет пройдена, код повторит попытку. В то же время библиотека сообщит об этой ошибке, которую можно будет просмотреть в консоли или обработать асинхронно.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Настройка контекста OAuth (вызываемого хранилища учетных записей)
Вы можете вызвать `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` для общего хранилища учетных записей для каждой службы, к которой у вашего приложения должен быть доступ. Учетная запись имеет тип "строка", который используется в качестве идентификатора для определенной службы. Так как вы обращаетесь к API Graph, код ссылается на него как на `"myGraphService"`. Затем вы настроите наблюдатель, который будет сообщать об изменениях токена. После получения токена пользователь вернется в `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Настройка основного представления для поиска и отображения пользователей из API Graph
Приложение MVC, которое отображает возвращаемые данные в сетке, выходит за рамки этого пошагового руководства. В Интернете есть много руководств, в которых объясняется, как создать такое приложение. Весь этот код находится в файле основы. Однако нужно внести некоторые изменения в это приложение MVC:

* Настроить перехват текста, вводимого пользователем в поле поиска.
* Вернуть объект данных в основное представление, чтобы результаты могли отображаться в сетке.

Мы выполним это далее.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Добавление проверки выполнения входа
Возможности приложения ограничены, если пользователь не выполнил вход, поэтому целесообразно проверить, существует ли уже токен в кэше. В противном случае осуществляется перенаправление в представление входа в систему, чтобы пользователь мог войти. Если вы помните, для выполнения действий при загрузке представления рекомендуется использовать метод `viewDidLoad()` , предоставляемый Apple.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Обновить представление таблицы при получении данных.
При возвращении данных из API Graph их необходимо отобразить. Для простоты здесь приведен весь код, необходимый для обновления таблицы. Вы можете просто вставить правильные значения в стандартном коде MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Указать метод вызова API Graph, когда пользователь вводит текст в поле поиска.
Когда пользователь вводит текст в поле поиска, необходимо передать его в API Graph. Класс `GraphAPICaller` , сборку которого вы выполните в следующем коде, разделяет функции поиска и представления. А пока давайте напишем код, передающий знаки, вводимые при поиске, в API Graph. Для этого мы укажем метод `lookupInGraph`, принимающий строку, которую нужно найти.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Написать вспомогательный класс для доступа к API Graph.
Это ядро нашего приложения. Раньше мы вставляли код в шаблон MVC по умолчанию, предоставленный Apple, а теперь вы напишете код для запроса к Graph при вводе и возврата данных. Ниже приведен код и пояснения к нему.

### <a name="create-a-new-objective-c-header-file"></a>Создание нового файла заголовка Objective C
Назовите файл `GraphAPICaller.h`и добавьте в него следующий код.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Как видно, указанный метод принимает строку и возвращает параметр completionBlock. Этот completionBlock, как можно догадаться, будет обновлять таблицу, предоставляя объект с заполненными данными в режиме реального времени во время выполнения поиска пользователем.

### <a name="create-a-new-objective-c-file"></a>Создание нового файла Objective C
Назовите файл `GraphAPICaller.m`и добавьте в него следующий метод.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Разберем этот метод по-подробнее.

Ядро этого кода находится в методе `NXOAuth2Request`, который принимает параметры, уже определенные вами в файле settings.plist.

Сначала нужно создать правильный вызов API Graph. Так как вызывается `/users`, необходимо указать это, добавив его в ресурс API Graph вместе с версией. Целесообразно поместить эти данные во внешний файл параметров, так как они могут меняться по мере развития API.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Далее следует задать параметры, которые также указываются в вызове API Graph. *Внимание!* Эти параметры не следует добавлять к конечной точке ресурсов, так как все знаки, не соответствующие формату универсального кода ресурса (URI), удаляются из нее во время выполнения. В параметрах необходимо указать весь код запроса.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Вы могли заметить, что этот код вызывает метод `convertParamsToDictionary` , который вы еще не написали. Давайте добавим его в конце файла:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Теперь мы используем метод `NXOAuth2Request` для получения данных из API в формате JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Наконец, давайте рассмотрим метод возврата данных в MVC. Данные возвращаются в сериализированном виде. Их необходимо десериализировать и загрузить в объект, который может использовать MVC. Для этого в основе предусмотрен файл `User.m/h`, который создает объект-пользователь. Вы заполните этот объект-пользователь данными из Graph.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Запуск примера
Если вы использовали основу и выполнили все указания пошагового руководства, приложение должно работать. Запустите симулятор и нажмите кнопку **Sign in** (Войти), чтобы использовать приложение.

## <a name="get-security-updates-for-our-product"></a>Получение обновлений системы безопасности для наших продуктов
Рекомендуем получать уведомления о произошедших инцидентах безопасности. Для этого посетите [технический центр безопасности](https://technet.microsoft.com/security/dd252948) и подпишитесь на уведомления о советах безопасности.

