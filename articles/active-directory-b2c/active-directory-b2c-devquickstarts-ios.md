<properties
	pageTitle="Azure Active Directory B2C: вызов веб-API из приложения iOS с использованием сторонней библиотеки | Microsoft Azure"
	description="В этой статье показано, как создать приложение iOS ";Список дел";, вызывающее веб-API Node.js с помощью токенов носителей OAuth 2.0 при использовании сторонней библиотеки."
	services="active-directory-b2c"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

	ms.date="07/26/2016"
	ms.author="brandwe"/>

# Azure AD B2C: вызов веб-API из приложения iOS с использованием сторонней библиотеки

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Платформа Microsoft Identity использует открытые стандарты, такие как OAuth2 и OpenID Connect. Это позволяет разработчикам использовать для интеграции со службами любые библиотеки. Чтобы объяснить разработчикам, как настроить сторонние библиотеки для подключения к платформе Microsoft Identity, мы написали несколько подобных этому пошаговых руководств. Большинство библиотек, в которых реализована [спецификация RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749), смогут подключаться к платформе Microsoft Identity.


Если вы еще не работали с OAuth2 или OpenID Connect, вы не поймете большую часть примера конфигурации. Для начала мы рекомендуем просмотреть [общие сведения о протоколе](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Чтобы использовать функции платформы, которые имеют выражения в этих стандартах, такие как управление политикой условного доступа и Intune, требуются библиотеки Microsoft Azure Identity с открытым исходным кодом.
   
Не все сценарии и компоненты Azure Active Directory поддерживаются платформой B2C. Чтобы определить, следует ли вам использовать платформу B2C, ознакомьтесь с [ограничениями этой платформы](active-directory-b2c-limitations.md).


## Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## Создание приложения

Затем необходимо создать приложение в каталоге B2C. Таким образом в Azure AD поступят сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае приложение и веб-API представлены одним **идентификатором приложения**, так как они включают в себя одно приложение логики. Создайте приложение, выполнив [эти указания](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

- Включите в приложение **мобильное устройство**.
- Скопируйте **идентификатор приложения**, назначенный приложению. Этот идентификатор также потребуется позднее.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Наше приложение предусматривает одну процедуру идентификации, сочетающую в себе вход и регистрацию. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании политики обязательно сделайте следующее:

- Укажите **отображаемое имя** и атрибуты входа для политики.
- Укажите утверждения приложения **Отображаемое имя** и **Идентификатор объекта** для каждой политики. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`. Эти имена политик понадобятся вам позже.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

После создания политик можно приступать к сборке приложения.


## Загрузка кода

Код в этом учебнике размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c). Для понимания процесса можно [скачать приложение как ZIP-файл](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c) (zip/archive/master.zip) или клонировать его:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Можно также просто скачать готовый код и немедленно приступить к работе.

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

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
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Теперь загрузите файл Podfile с помощью Cocoapods. Будет создана новая рабочая область XCode.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## Структура проекта

В основе настроена следующая структура проекта:

* **основное представление** с областью задач;
* представление **Добавление задачи** для данных выбранной задачи;
* **представление входа**, которое позволяет пользователю входить в приложение.

Мы будем использовать различные файлы, входящие в проект, для добавления проверки подлинности. Другие части кода, например код визуального элемента, не относятся к идентификации и предоставляются автоматически.

## Создание файла `settings.plist` для приложения

Чтобы упростить настройку приложения, необходимо использовать централизованное расположение для размещения значений конфигурации. Это также поможет понять функцию каждого параметра в приложении. Для передачи этих значений в приложение мы будем использовать *список свойств*.

* Создайте или откройте файл `settings.plist` в разделе `Supporting Files` рабочей области приложения.

* Введите следующие значения (их подробное описание приведено ниже):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>accountIdentifier</key>
	<string>B2C_Acccount</string>
	<key>clientID</key>
	<string><client ID></string>
	<key>clientSecret</key>
	<string></string>
	<key>authURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
	<key>loginURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/login</string>
	<key>bhh</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>tokenURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
	<key>keychain</key>
	<string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
	<key>contentType</key>
	<string>application/x-www-form-urlencoded</string>
	<key>taskAPI</key>
	<string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Давайте рассмотрим это подробно.


Для параметров `authURL`, `loginURL`, `bhh` и `tokenURL` понадобится указать имя клиента. Это имя назначенного вам клиента B2C. Например, `kidventusb2c.onmicrosoft.com`. При использовании библиотек Microsoft Azure Identity с открытым исходным кодом эти данные можно получить с помощью конечной точки метаданных. Мы уже сделали все за вас и извлекли эти значения.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Значение `keychain` — это контейнер, который будет использовать библиотека NXOAuth2Client, чтобы создать цепочку ключей для хранения маркеров. Если вы хотите настроить единый вход для нескольких приложений, можно указать одну и ту же цепочку ключей в каждом приложении, а также запросить ее использование в рамках прав XCode. Это подробно описано в документации Apple.

Вместо заполнителя `<policy name>` в конце каждого URL-адреса необходимо указать политику, созданную ранее. Приложение вызывает эти политики в зависимости от последовательности.

Значение `taskAPI` — это конечная точка REST, которая будет вызываться вместе с маркером B2C для добавления задач или запроса имеющихся задач. Этот параметр настроен специально для данного примера. Не изменяйте его, чтобы этот образец кода работал.

Остальные значения требуются для использования библиотеки. Они необходимы для передачи значений в контекст.

Теперь, когда мы создали файл `settings.plist`, необходимо написать код для его считывания.

## Настройка класса AppData для считывания параметров

В этом разделе мы создадим простой файл, который будет анализировать созданный ранее файл `settngs.plist`, и сделаем эти параметры доступными для любого класса, которому они понадобятся в будущем. Чтобы не создавать копию данных каждый раз, когда их запрашивает класс, мы используем шаблон с одним элементом. Таким образом, при запросе будет возвращаться один и тот же экземпляр, созданный при отправке запроса на параметры.

* Создайте файл `AppData.h`.

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Создайте файл `AppData.m`.

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Теперь, чтобы получить наши данные, нам просто нужно вызвать `  AppData *data = [AppData getInstance];` в соответствующих классах, описанных ниже.



## Настройка библиотеки NXOAuth2Client в AppDelegate

Для библиотеки NXOAuthClient необходимо задать некоторые значения. После этого можно использовать токен, полученный для вызова REST API. Так как `AppDelegate` вызывается каждый раз, когда загружается приложение, необходимо внести значения конфигурации в следующий файл.
* Откройте файл `AppDelegate.m`.

* Импортируйте файлы заголовков, которые будут использоваться позже.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Добавьте метод `setupOAuth2AccountStore` в AppDelegate.

Нам нужно создать хранилище учетных данных и передать в него данные, считанные из файла `settings.plist`.

На этом этапе необходимо учесть некоторые моменты, касающиеся службы B2C. Это поможет сделать код более понятным.


1. Для обработки запроса Azure AD B2C использует *политику* в соответствии с параметрами запроса. Таким образом Azure Active Directory может выступать в качестве независимой службы только для вашего приложения. Чтобы предоставить эти дополнительные параметры, необходимо указать метод `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` с пользовательскими параметрами политики.

2. Azure AD B2C использует области практически так же, как другие серверы OAuth2. Однако, так как B2C предназначен не только для проверки подлинности пользователя, но и для доступа к ресурсам, некоторые области крайне важны для правильной работы потока. Одна из таких областей — `openid`. Пакеты SDK для Microsoft Identity автоматически предоставляют область `openid`, поэтому она не указывается в конфигурации пакета SDK. Однако, так как мы используем стороннюю библиотеку, нам необходимо указать эту область.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Далее нам понадобится вызвать ее в AppDelegate методом `didFinishLaunchingWithOptions:`.

```
[self setupOAuth2AccountStore];
```


## Создание класса `LoginViewController`, который будет использоваться для обработки запросов на проверку подлинности

Мы используем его для входа в учетную запись. Таким образом мы можем запрашивать у пользователя дополнительные факторы, например SMS-сообщения (если настроено), или возвращать ему сообщения об ошибках. Мы настроим веб-представление, а затем напишем код для обработки обратных вызовов, которые будут выполняться в этом представлении, из службы Microsoft Identity.

* Создайте класс `LoginViewController.h`.

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Мы создадим каждый из приведенных методов далее.

> [AZURE.NOTE] 
    Привяжите представление `loginView` к веб-представлению в раскадровке. В противном случае веб-представление, которое должно всплывать при проверке подлинности, не будет настроено.

* Создайте класс `LoginViewController.m`.

* Добавьте переменные, которые будут содержать значение состояния при проверке подлинности.

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Переопределение методов веб-представления для обработки проверки подлинности

Необходимо настроить для веб-представления поведение при попытке пользователя выполнить вход, как описано выше. Вы можете просто вырезать и вставить приведенный ниже код.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Написание кода для обработки результатов запроса OAuth2

Нам потребуется код, обрабатывающий URL-адрес перенаправления, который возвращается из веб-представления. Мы будем совершать попытки, пока не достигнем желаемого результата. В то же время в библиотеке возникнет ошибка, которую можно просмотреть в консоли или обработать асинхронно.

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Настройте фабрики уведомлений.

Мы создадим тот же метод, что и для `AppDelegate` выше, но на этот раз мы настроим некоторые уведомления `NSNotification`, чтобы знать о том, что происходит в нашей службе. Кроме того, мы настроим наблюдатель, который будет сообщать об изменениях токена. После получения маркера пользователь вернется в `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Добавьте код для обработки пользователя каждый раз, когда инициируется запрос для собственного входа.

Давайте создадим метод, который будет вызываться при получении запроса на проверку подлинности. Этот метод будет создавать веб-представление.

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Наконец, давайте настроим так, чтобы при каждой загрузке `LoginViewController` вызывались все методы, описанные выше. Для этого необходимо добавить эти методы в метод `viewDidLoad`, предоставляемый Apple.

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Мы настроили все, что нужно для базового взаимодействия с приложением при входе в систему. После выполнения входа нам потребуется использовать полученные токены. Для этого мы создадим вспомогательный код, который будет вызывать интерфейсы REST API с использованием сторонней библиотеки.


## Создание класса `GraphAPICaller` для обработки запросов к REST API

При каждой загрузке приложения загружается соответствующая конфигурация. Теперь, когда у нас есть токен, нам нужно внести некоторые изменения.

* Создайте файл `GraphAPICaller.h`.

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Как видно в коде, мы создадим два метода: один — для получения задач из API, а другой — для добавления задач в API.

Теперь, когда наш интерфейс настроен, нам нужно добавить фактическую реализацию:

* Создайте файл `GraphAPICaller.m file`.

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## Запуск примера приложения

Наконец, можно собрать и запустить приложение в Xcode. Зарегистрируйтесь в приложении или войдите в него, а затем создайте задачи для пользователя, выполнившего вход. Выйдите и снова войдите от имени другого пользователя. Создайте задачи для этого пользователя.

Обратите внимание, что в API хранятся задачи для каждого пользователя, так как API извлекает удостоверение пользователя из получаемого маркера доступа.


## Дальнейшие действия

Теперь можно перейти к более сложным темам B2C. Попробуйте ознакомиться с такими материалами:

[Вызов веб-API Node.js из веб-приложения Node.js]()

[Настройка UX для приложения B2C]()

<!---HONumber=AcomDC_1005_2016-->