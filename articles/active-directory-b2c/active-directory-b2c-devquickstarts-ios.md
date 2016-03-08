<properties pageTitle="Предварительная версия Azure Active Directory B2C: вызов веб-API из веб-приложения iOS | Microsoft Azure" description="В этой статье показано, как создать приложение iOS "Список дел", вызывающее веб-API Node.js с помощью токенов носителя OAuth 2.0. Как приложение iOS, так и веб-API используют Azure Active Directory B2C для проверки подлинности пользователей и управления их удостоверениями." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Предварительная версия Azure AD B2C: вызов веб-API из приложения iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C позволяет добавлять в веб-API и приложения iOS мощные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье показано, как создать приложение iOS "Список дел", вызывающее веб-API Node.js с помощью токенов носителя OAuth 2.0. Приложение iOS и веб-API используют Azure AD B2C для проверки подлинности пользователей и управления их удостоверениями.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Для полноценной работы с этим кратким руководством требуется веб-API, защищенный с помощью Azure AD B2C. Мы создали для вас веб-API, который можно использовать как с .NET, так и с Node.js. В этом пошаговом руководстве предполагается, что пример веб-API для Node.js уже настроен. Чтобы узнать больше, см. [пример веб-API Azure Active Directory для Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]
	В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Основную информацию об Azure AD B2C см. в статье [Предварительная версия Azure AD B2C: создание веб-приложения API .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Прежде чем продолжать, [создайте каталог B2C](active-directory-b2c-get-started.md), если он еще не создан.

## Создание приложения

Затем необходимо создать приложение в каталоге B2C. Таким образом в Azure AD поступят сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае приложение и веб-API представлены одним **идентификатором приложения**, так как они включают в себя одно приложение логики. Создайте приложение, выполнив [эти инструкции](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

- Включите в приложение **веб-приложение или веб-API**.
- Введите `http://localhost:3000/auth/openid/return` как **URL-адрес ответа**. Это URL-адрес по умолчанию для данного примера кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Оно понадобится вам позднее.
- Скопируйте **идентификатор приложения**, назначенный приложению. Этот идентификатор также потребуется позднее.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Это приложение содержит три вида идентификации: регистрация, вход и вход с помощью учетной записи Facebook. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех необходимых политик обязательно сделайте следующее:

- В политике регистрации выберите атрибут **Отображаемое имя** и атрибуты входа.
- В каждой политике выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`. Эти имена политик понадобятся вам через некоторое время.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к сборке приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Загрузка кода

Код, используемый в этом руководстве, размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Для выполнения этого руководства необходимо скачать схему.** В iOS довольно сложно реализовать полнофункциональное приложение, поэтому **схема** содержит UX-код, который будет выполнен после завершения работы с руководством. Это позволит разработчику сэкономить время. UX-код не связан с добавлением B2C в приложение iOS.

Кроме того, можно скачать готовое приложение [в виде ZIP-файла](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

Теперь загрузите `podfile` с помощью CocoaPods. Будет создана новая рабочая область Xcode. Если у вас нет CocoaPods, [установите это средство с веб-сайта](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Настройка приложения задач iOS

Для того чтобы приложение задач iOS взаимодействовало с Azure AD B2C, необходимо задать несколько общих параметров. В папке `Microsoft Tasks` откройте файл `settings.plist` в корне проекта и замените значения в разделе `<dict>`. Эти значения будут использоваться во всем приложении.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Получение маркеров доступа и вызов API задачи

В этом разделе объясняется, как выполнить обмен маркерами OAuth 2.0 в веб-приложении с помощью библиотек и платформ Майкрософт. Сведения о кодах авторизации и маркерах доступа см. в [справочнике по протоколу OAuth 2.0](active-directory-b2c-reference-protocols.md).

### Создание файлов заголовков с помощью методов

Чтобы получить маркер с выбранной политикой, а затем вызвать сервер задач, вам потребуются методы. Их можно настроить прямо сейчас.

Создайте файл с именем `samplesWebAPIConnector.h` в папке `/Microsoft Tasks` проекта Xcode.

Добавьте следующий код, чтобы определить необходимые действия.

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Это простые операции создания, чтения, обновления и удаления (CRUD) в API, а также метод `doPolicy`. Этот метод позволяет получить маркер с нужной политикой.

Обратите внимание, что необходимо определить еще два файла заголовка. Они будут содержать элемент задачи и данные политики. Создайте эти заголовки сейчас, как описано ниже.

Создайте файл `samplesTaskItem.h` с помощью указанного ниже кода.

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Создайте также файл `samplesPolicyData.h` для хранения данных политики.

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Добавление реализации для элементов задачи и политики

Теперь, когда созданы файлы заголовков, можно написать код для хранения данных, которые будут использоваться в вашем примере.

Создайте файл `samplesPolicyData.m` с помощью указанного ниже кода.

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Написание кода установки для вызова ADAL для iOS

Быстрый код для хранения объектов пользовательского интерфейса готов. Теперь нужно написать код для доступа к библиотеке проверки подлинности Active Directory (ADAL) для iOS с помощью параметров, указанных в файле `settings.plist`. Этот код будет получать маркер доступа, который нужно будет предоставлять серверу задач.

Вся работа будет выполняться в файле `samplesWebAPIConnector.m`.

Сначала создайте реализацию `doPolicy()`, записанную в файле заголовка `samplesWebAPIConnector.h`:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

Это простой метод. В качестве входных данных принимаются созданный вами объект `samplesPolicyData`, родительский объект `ViewController` и обратный вызов. Обратный вызов особенно интересен, и мы рассмотрим его подробнее.

- Обратите внимание, что `completionBlock` содержит `ADProfileInfo` как тип, который возвращается с помощью объекта `userInfo`. `ADProfileInfo` — тип, который содержит все ответы от сервера, в том числе утверждения.
- Также обратите внимание на команду `readApplicationSettings`. Она считывает данные, указанные в `settings.plist`.
- Наконец, рассмотрим большой метод `getClaimsWithPolicyClearingCache`. Это и есть тот вызов ADAL для iOS, который нам нужно написать. Мы вернемся к нему позже.

Теперь напишите большой метод `getClaimsWithPolicyClearingCache`. Он настолько большой, что заслуживает отдельного раздела.

### Создание вызова ADAL для iOS

Если схема была загружена с сайта GitHub, она уже содержит ряд таких вызовов, которые помогут вам создать пример приложения. Все они соответствуют шаблону `get(Claims|Token)With<verb>ClearningCache`. Благодаря условным обозначениям Objective C они читаются почти так же легко, как обычный английский текст. Например, команда Get a token with extra parameters that I provide to you and clear the cache (Получить маркер с дополнительными параметрами, которые я укажу, и очистить кэш) выглядит вот так: `getTokenWithExtraParamsClearingCache()`.

Вы напишете Get claims and a token with the policy that I provide to you and don't clear the cache (Получить утверждения и маркер согласно указанной мною политике и не очищать кэш) или `getClaimsWithPolicyClearingCache`. Мы всегда возвращаем маркер из ADAL, поэтому в методе не нужно указывать claims and a token (утверждения и маркер). Тем не менее в случаях, когда требуется только маркер без анализа утверждений, в схеме вызывается метод без утверждений, который называется `getTokenWithPolicyClearingCache`.

Теперь напишите этот код:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

Первая часть должна быть вам знакома.

- Загрузите параметры, указанные в `settings.plist`, и назначьте их параметру `data`.
- Настройте параметр `ADAuthenticationError`, который принимает все ошибки из ADAL для iOS.
- Создайте параметр `authContext`, который настраивает вызов ADAL. Этот параметр запускает процесс от вашего имени.
- Передайте параметру `authContext` ссылку на родительский контроллер, чтобы можно было вернуться к нему.
- Преобразуйте параметр `redirectURI`, который раньше был строкой в `settings.plist`, в тип NSURL, который необходим для ADAL.
- Настройте параметр `correlationId`. Это просто идентификатор UUID, который может проследить вызов от клиента к серверу и обратно. Он пригодится для отладки.

Теперь у вас есть фактический вызов ADAL. Он будет отличаться от вызовов ADAL для iOS, с которыми вы сталкивались раньше.

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

Как видите, вызов выполняется очень просто.

`scopes` — это области, которые можно передавать на сервер и которые необходимо запрашивать с сервера для пользователя, выполняющего вход. В предварительной версии B2C нужно передать `client_id`. Но в будущем мы планируем заменить этот параметр областями чтения. После этого изменения данный документ будет обновлен. `additionalScopes` — это дополнительные области, которые можно использовать для вашего приложения. Они будут использоваться в будущем. `clientId` — идентификатор приложения, полученный с портала. `redirectURI` — перенаправление, в которое маркер будет передан обратно. `identifier` — способ идентификации пользователя, при котором можно увидеть, есть ли в кэше доступный для использования маркер. Благодаря этому не нужно постоянно запрашивать на сервере другой маркер. Эту задачу выполняет тип `ADUserIdentifier`, и вы можете указать, что будет использоваться в качестве идентификатора. Следует использовать `username`. `promptBehavior` — устаревший параметр. Вместо него используется `AD_PROMPT_ALWAYS`. `extraQueryParameters` — любые дополнительные сведения, которые необходимо передать на сервер, закодированные в формате URL-адреса. `policy` — политика, которую вы вызываете. Это самая важная часть этого руководства.

В `completionBlock` вы видите, что передается `ADAuthenticationResult`. Этот параметр содержит маркер и информацию профиля (при успешном вызове).

С помощью приведенного выше кода можно получить маркер для указанной политики. Вы можете использовать этот маркер для вызова API.

### Создание вызовов задач к серверу

Теперь, когда у нас есть маркер, необходимо указать его в API для авторизации.

Необходимо реализовать три метода.

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

Метод `getTasksList` содержит массив, представляющий задачи на сервере. Методы `addTask` и `deleteTask` выполняют последующие операции и в случае успеха возвращают значения `true` или `false`.

Сначала напишите `getTaskList`:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

Обсуждение кода задачи выходит за рамки этого пошагового руководства. Но вы могли заметить кое-что интересное — метод `craftRequest`, который принимает URL-адрес задачи. Используйте этот метод для создания запроса к серверу с помощью полученного маркера доступа. Напишите его сейчас.

Добавьте в файл `samplesWebAPIConnector.m` следующий код:

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Этот код принимает веб-URI (универсальный код ресурса), добавляет к нему маркер с помощью заголовка `Bearer` в HTTP и возвращает его вам. Вы можете вызвать API `getTokenClearingCache`. Возможно, это покажется странным, но с помощью этого вызова вы можете получить из кэша маркер и проверить, действителен ли он. (Вызовы `getToken` делают это, отправляя запросы к ADAL.) Этот код применяется при каждом вызове. Теперь создайте дополнительные методы задач.

Напишите `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Этот код работает по тому же шаблону, но он также содержит последний метод, который необходимо реализовать: `convertTaskToDictionary`. Он принимает массив и делает его объектом словаря. Этот объект легко превращается в параметры запроса, которые необходимо передать на сервер. Код простой:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Теперь напишите `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Добавление выхода в приложение

Осталось только реализовать в приложении функцию выхода. Это просто. Вставьте в файл `sampleWebApiConnector.m` этот код:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## Запуск примера приложения

Наконец, можно собрать и запустить приложение в Xcode. Зарегистрируйтесь в приложении или войдите в него, а затем создайте задачи для пользователя, выполнившего вход. Выйдите и снова войдите от имени другого пользователя. Создайте задачи для этого пользователя.

Обратите внимание, что в API хранятся задачи для каждого пользователя, так как API извлекает удостоверение пользователя из получаемого маркера доступа.

Готовый пример в виде ZIP-файла находится [здесь](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Дальнейшие действия

Теперь можно перейти к более сложным темам B2C. Попробуйте ознакомиться с такими материалами:

[Вызов веб-API Node.js из веб-приложения Node.js]()

[Настройка UX для приложения B2C]()

<!---HONumber=AcomDC_0302_2016-->