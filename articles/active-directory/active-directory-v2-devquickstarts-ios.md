<properties
	pageTitle="Приложение iOS для Azure AD версии 2.0 | Microsoft Azure"
	description="Как создать приложение для iOS, которое поддерживает вход пользователей в систему с помощью личной, рабочей и учебной учетной записи Майкрософт, используя сторонние библиотеки."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="brandwe"/>

# Добавление функции входа в приложение iOS, использующее стороннюю библиотеку и API Graph, с помощью конечной точки версии 2.0

Платформа Microsoft Identity использует открытые стандарты, такие как OAuth2 и OpenID Connect. Это позволяет разработчикам использовать для интеграции со службами любые библиотеки. Чтобы объяснить разработчикам, как настроить сторонние библиотеки для подключения к платформе Microsoft Identity, мы написали несколько подобных этому пошаговых руководств. Большинство библиотек, в которых реализована [спецификация RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749), смогут подключаться к платформе Microsoft Identity.

Это приложение позволяет пользователям входить в свою организацию и искать других ее сотрудников с помощью API Graph.

Если вы еще не работали с OAuth2 или OpenID Connect, вы не поймете большую часть примера конфигурации. Для начала мы рекомендуем просмотреть [общие сведения о протоколе](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
    Чтобы использовать функции платформы, которые имеют выражения в этих стандартах, такие как управление политикой условного доступа и Intune, требуются библиотеки Microsoft Azure Identity с открытым исходным кодом.

> [AZURE.NOTE] 
    Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

## Загрузить
Код в этом учебнике размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) или клонировать ее:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Или просто скачайте код и немедленно приступите к работе.

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните [эти подробные указания](active-directory-v2-app-registration.md). Не забудьте:

- запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.
- Добавьте для приложения **мобильную** платформу.
- Скопируйте с портала **универсальный код ресурса (URI) перенаправления**. Необходимо использовать стандартное значение `urn:ietf:wg:oauth:2.0:oob`.


## Загрузка сторонней библиотеки NXOAuth2 и запуск рабочей области

В этом пошаговом руководстве будет использоваться библиотека OAuth2Client, расположенная на GitHub, — OAuth2 для Mac OS X и iOS (платформа Cocoa и Cocoa Touch). В ее основе лежит черновая версия (10) спецификации OAuth2. Она реализует профиль собственного приложения и поддерживает конечную точку авторизации конечных пользователей. Это все, что нам потребуется для интеграции с платформой Microsoft Identity.

### Добавление библиотеки в проект с помощью CocoaPods

CocoaPods — это диспетчер зависимостей для проектов Xcode. Он автоматически управляет приведенными выше шагами установки.

```
$ vi Podfile
```
Добавьте в файл Podfile следующий код:

```
 platform :ios, '8.0'
 
 target 'QuickStart' do
 
 pod 'NXOAuth2Client'
 
 end
```

Теперь загрузите файл Podfile с помощью Cocoapods. Будет создана новая рабочая область XCode.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

## Структура проекта

В основе настроена следующая структура проекта:

* Основное представление с поиском имени участника-пользователя.
* Подробное представление с данными выбранного пользователя.
* Представление входа, которое позволяет пользователю входить в приложение для запроса Graph.

Мы будем использовать различные файлы, входящие в основу, для добавления проверки подлинности. Другие части кода, например код визуального элемента, не относятся к идентификации и предоставляются автоматически.

## Настройка файла settings.plst в библиотеке

-	В проекте QuickStart откройте файл `settings.plist`. Замените значения элементов в соответствующем разделе на значения, введенные на портале Azure. Код будет использовать эти значения при каждом обращении к библиотеке ADAL.
    -	`clientId` — это идентификатор clientId приложения, скопированный с портала.
    -	`redirectUri` — это URL-адрес перенаправления, предоставленный на портале.

## Настройка библиотеки NXOAuth2Client в LoginViewController

Для библиотеки NXOAuthClient необходимо задать некоторые значения. После этого можно использовать токен, полученный для вызова API Graph. Так как `LoginView` вызывается каждый раз, когда нужно выполнить проверку подлинности, необходимо внести значения конфигурации в следующий файл.
* Открытие файла `LoginViewController.m`

* Нам нужно добавить некоторые значения в код, который задает контекст для проверки подлинности и авторизации. Это подробнее описано ниже.

```objc
NSString *scopes = @"offline_access User.ReadBasic.All";
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

Давайте рассмотрим это подробно.

Первая строка задается для `scopes`. Для данного приложения мы будем запрашивать область `User.ReadBasic.All`, которая позволяет просматривать базовые профили всех пользователей в каталоге. Дополнительные сведения об областях, доступных для использования с Microsft Graph, см. [здесь](https://graph.microsoft.io/docs/authorization/permission_scopes).

Для `authURL`, `loginURL`, `bhh` и `tokenURL` следует использовать значения, приведенные выше. При использовании библиотек Microsoft Azure Identity с открытым исходным кодом эти данные можно получить с помощью конечной точки метаданных. Мы уже сделали все за вас и извлекли эти значения.

Значение `keychain` — это контейнер, который будет использовать библиотека NXOAuth2Client, чтобы создать цепочку ключей для хранения токенов. Если вы хотите настроить единый вход для нескольких приложений, можно указать одну и ту же цепочку ключей в каждом приложении, а также запросить ее использование в рамках прав XCode. Это подробно описано в документации Apple.

Остальные значения требуются для использования библиотеки. Они необходимы для передачи значений в контекст.

* Создание кэша URL-адресов

Внутри метода `(void)viewDidLoad()`, который вызывается после загрузки представления, нужно заполнить кэш.

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

* Создайте веб-представление, которое будет использоваться для входа.

Мы используем его для входа в учетную запись. Таким образом мы можем запрашивать у пользователя дополнительные факторы, например SMS-сообщения (если настроено), или возвращать ему сообщения об ошибках. Мы настроим веб-представление, а затем напишем код для обработки обратных вызовов, которые будут выполняться в этом представлении, из службы Microsoft Identity.

```objc
-(void)requestOAuth2Access {
    //in order to login to Mircosoft APIs using OAuth2 we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client
                                       
                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

* Переопределение методов веб-представления для обработки проверки подлинности

Необходимо настроить для веб-представления поведение при попытке пользователя выполнить вход, как описано выше. Вы можете просто вырезать и вставить приведенный ниже код.

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

* Написание кода для обработки результатов запроса OAuth2

Нам потребуется код, обрабатывающий URL-адрес перенаправления, который возвращается из веб-представления. Мы будем совершать попытки, пока не достигнем желаемого результата. В то же время в библиотеке возникнет ошибка, которую можно просмотреть в консоли или обработать асинхронно.

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

* Настройка контекста OAuth (вызываемого хранилища учетных записей)

Вы можете вызвать `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` для общего хранилища учетных записей для каждой службы, к которой необходимо иметь доступ из вашего приложения. Учетная запись имеет тип "строка", который используется в качестве идентификатора для определенной службы. Так как мы получаем доступ к API Graph, мы используем имя `"myGraphService"`. Затем мы настроим наблюдатель, который будет сообщать об изменениях токена. После получения токена пользователь вернется в `masterView`.



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

## Настройка основного представления для поиска и отображения пользователей из API Graph

Приложение MVC, которое отображает возвращаемые данные в сетке, выходит за рамки этого пошагового руководства. В Интернете есть много руководств, в которых объясняется, как создать такое приложение. Весь код представлен в файле основы. Однако нам нужно внести некоторые изменения в этом приложении MVC:

* Настроить перехват текста, вводимого пользователем в поле поиска.
* Вернуть объект данных в основное представление, чтобы результаты могли отображаться в сетке.

Мы выполним это далее.

* Добавить проверку выполнения входа.

Доступные возможности приложения ограничены, если пользователь не выполнил вход, поэтому целесообразно проверить, существует ли уже токен в кэше. В противном случае осуществляется перенаправление в представление входа для входа пользователя. Если вы помните, для выполнения действий при загрузке представления рекомендуется использовать метод `viewDidLoad()`, предоставляемый Apple.

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

* Обновить представление таблицы при получении данных.

Данные, возвращенные из API Graph, необходимо отобразить. Для простоты здесь приведен весь код, необходимый для обновления таблицы. Вы можете просто вставить правильные значения в стандартном коде MVC.

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

* Указать метод вызова API Graph, когда пользователь вводит текст в поле поиска.

Когда пользователь вводит текст в поле поиска, необходимо передавать его в API Graph. Чтобы лучше отделить функцию поиска от предоставления, немного позже мы создадим другой класс с именем `GraphAPICaller`. Сейчас мы напишем код для передачи любых символов поиска в API Graph. Для этого мы укажем метод `lookupInGraph`, принимающий строку, которую нужно найти.

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

## Написать вспомогательный класс для доступа к API Graph.

Это часть нашего приложения. Раньше мы вставляли код в шаблон MVC по умолчанию, предоставленный Apple, а теперь мы напишем код для запроса Graph при вводе и возврате этих данных. Мы покажем этот код и подробно его разберем.

* Создайте файл заголовка Objective C `GraphAPICaller.h`, используя следующий код:

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Как видно, мы указываем метод, который принимает строку и возвращает параметр completionBlock. Этот параметр, как можно догадаться, будет обновлять таблицу, предоставляя объект с заполненными данными в режиме реального времени во время выполнения поиска пользователем.


* Создайте файл Objective C `GraphAPICaller.m` и добавьте следующий метод:

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

Разберем этот метод детально.

Основная часть этого кода находится в методе `NXOAuth2Request`, который принимает параметры, ранее определенные в файле settings.plist. Сначала нужно создать правильный вызов API Graph. Так как мы вызываем `/users`, необходимо добавить это в ресурс API Graph вместе с версией. Целесообразно задать для этих свойств срок действия внешних параметров, так как они могут меняться по мере развития API.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Сейчас мы укажем параметры, также необходимые для вызова API Graph. *Внимание!* Эти параметры не следует добавлять к конечной точке ресурсов, так как все символы, не соответствующие URI, удаляются во время выполнения. В параметрах необходимо указать весь код запроса.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Вы могли заметить, что этот код вызывает метод `convertParamsToDictionary`, который мы еще не написали. Давайте добавим его в конце файла:

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

Наконец, давайте рассмотрим метод возврата данных в MasterViewController. Данные возвращаются в сериализированном виде. Их необходимо десериализировать и загрузить в объект, который может использовать MainViewController. Для этого в основе предусмотрен файл `User.m/h`, который создает объект-пользователь. Мы заполним этот объект-пользователь данными из Graph.

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


## Запуск примера

Если вы использовали основу и выполнили все указания пошагового руководства, приложение должно работать. Запустите симулятор и нажмите кнопку "Вход", чтобы использовать приложение.

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0629_2016-->