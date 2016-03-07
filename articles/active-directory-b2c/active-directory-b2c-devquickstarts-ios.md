<properties pageTitle="Предварительная версия Azure AD B2C: вызов веб-API из веб-приложения iOS | Microsoft Azure" description="В этой статье показано, как создать приложение iOS "Список дел", вызывающее веб-API node.js с помощью маркеров носителей OAuth 2.0. Как приложение iOS, так и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей. " services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Предварительна версия Azure AD B2C: вызов веб-API из приложения iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C позволяет добавлять в приложения iOS и веб-приложения мощные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье показано, как создать приложение iOS "Список дел", вызывающее веб-API node.js с помощью маркеров носителей OAuth 2.0. Приложение iOS и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Для работы с этим кратким руководством требуется веб-API, защищенный Azure AD с B2C. Мы создали для вас файлы .NET и node.js. В данном пошаговом руководстве предполагается, что образец веб-API в файле node.js уже настроен. См. [образец файла node.js в веб-API Azure Active Directory](active-directory-b2c-devquickstarts-api-node.md`).

> [AZURE.NOTE]
	В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Прочитайте [руководство по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md), чтобы изучить основы Azure AD B2C (если вы еще этого не сделали).

## 1\. Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md).

## 2\. Создание приложения

Теперь необходимо создать приложение в каталоге B2C. Оно будет передавать в Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае приложение и веб-API будут представлены одним **идентификатором приложения**, поскольку они включают в себя одно приложение логики. Создайте приложение, выполнив [эти инструкции](active-directory-b2c-app-registration.md). Не забудьте

- Включите в приложение **веб-приложение или веб-API**.
- Введите `http://localhost:3000/auth/openid/return` как **URL-адрес ответа**. Это — URL-адрес по умолчанию для этого примера кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Скоро он вам понадобится. Обратите внимание, что это значение должно быть [экранировано для XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) перед использованием.
- Скопируйте **идентификатор приложения**, назначенный приложению. Он также вам скоро понадобится.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Создание политик

В Azure AD B2C всякое взаимодействие с пользователем определяется [**политикой**](active-directory-b2c-reference-policies.md). Это приложение содержит три вида идентификации: регистрация, вход и вход с помощью учетной записи Facebook. Вам нужно создать по одной политике каждого типа, как описано в [справочнике по политикам](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно сделайте следующее.

- В политике регистрации выберите **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`. Эти имена политик понадобятся вам через некоторое время.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Загрузка кода

Код для этого учебника размещен на веб-сайте [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Чтобы построить образец, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip) или клонируйте ее.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Для изучения этого учебника необходимо загрузить схему.** Из-за сложности реализации полнофункционального приложения для iOS **схема** содержит UX-код, который будет выполнен после завершения работы с указанным ниже учебником. Это позволит разработчику сэкономить время. UX-код не связан с темой добавления B2C в приложение iOS.

Кроме того, можно скачать готовое приложение [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) или получить его из ветви `complete` того же репозитория.


Теперь загрузите файл Podfile с помощью Cocoapods. Будет создана новая рабочая область XCode. Если у вас нет Cocoapods, установите его с [веб-сайта](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\. Настройка приложения задач iOS

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

## 6\. Получение маркеров доступа и вызов API задач

В этом разделе показано, как выполнить обмен маркерами OAuth 2.0 в веб-приложении с помощью библиотек и платформ от Майкрософт. Сведения о **кодах авторизации** и **маркерах доступа** см. в [справочнике по протоколу OAuth 2.0](active-directory-b2c-reference-protocols.md).

#### Создайте файлы заголовков с применением методов, которые мы будем использовать.

Эти методы потребуются нам для того, чтобы получить маркер в соответствии с выбранной политикой, а затем вызвать сервер задач. Попробуем их настроить.

В папке /Microsoft Task проекта XCode создайте файл с именем `samplesWebAPIConnector.h`.

Добавьте следующий код, чтобы определить необходимые действия:

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

Вы увидите, что получить маркер в соответствии с желаемой политикой можно с помощью простых операций CRUD в API, а также с помощью метода `doPolicy`.

Вы также увидите, что нам необходимо определить еще два файла заголовков, которые будут содержать элемент задачи и данные политики. Давайте их создадим.

Создайте файл с именем `samplesTaskItem.h` с помощью указанного ниже кода.

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Кроме того, создадим файл `samplesPolicyData.h` для хранения данных политики.

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### Добавление реализации для элементов задачи и политики

Теперь, когда у нас есть файлы заголовков, нужно написать код для хранения данных, которые будут использоваться в нашем образце кода.

Создайте файл с именем `samplesPolicyData.m` с помощью указанного ниже кода.

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

#### Написание кода установки для вызова ADAL из iOS

Быстрый код для хранения объектов нашего пользовательского интерфейса готов. Теперь, используя параметры, определенные в файле `settings.plist`, необходимо написать код для доступа к ADAL из iOS, предназначенный для получения маркера доступа и передачи его на сервер задач. Код может быть длинным, поэтому будьте внимательны.

Вся работа будет выполняться в `samplesWebAPIConnector.m`.

Сначала создадим реализацию метода `doPolicy()`, которую мы написали в файле заголовка `samplesWebAPIConnector.h`.

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

Вы увидите, что метод довольно прост. В качестве входных данных он принимает объект `samplesPolicyData`, который мы создали несколько минут назад, родительский класс ViewController, а затем обратный вызов. Обратный вызов — это интересно, и мы рассмотрим его подробно.

1. Вы увидите, что в `completionBlock` в качестве типа выбран ADProfileInfo, возвращаемый объектом `userInfo`. ADProfileInfo — это тип, содержащий весь ответ сервера в определенных утверждениях.
2. Мы используем `readApplicationSettings`. Эта функция считывает данные, предоставленные в `settings.plist`.
3. И, наконец, мы используем довольно большой метод `getClaimsWithPolicyClearingCache`. Это и есть тот вызов ADAL для iOS, который нам нужно написать. Мы сделаем это позже.

Теперь напишем большой метод `getClaimsWithPolicyClearingCache`. Он такой большой, что заслуживает отдельного раздела.

#### Создание вызова ADAL для iOS

Если схема была загружена из GitHub, она уже содержит ряд таких вызовов, которые помогут нам создать образец приложения. Все они соответствуют шаблону `get(Claims|Token)With<verb>ClearningCache`. Благодаря условным обозначениям Objetive C они почти так же понятны, как обычный текст, например "получить маркер с дополнительными параметрами, которые я укажу, и очистить кэш". Это `getTokenWithExtraParamsClearingCache()`. Довольно просто.

Мы напишем «получать утверждения и маркер согласно указанной мною политике и не очищать кэш» или `getClaimsWithPolicyClearingCache`. Мы всегда возвращаем маркер из ADAL, поэтому указывать "Утверждения и маркер" в методе необязательно. Тем не менее в случаях, когда требуется только маркер без синтаксического анализа утверждений, в схеме вызывается метод без утверждений, который называется `getTokenWithPolicyClearingCache`.

Давайте напишем этот код.

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

Первая часть должна быть вам знакома. Мы загружаем параметры, указанные в `Settings.plist`, и назначаем их параметру `data`. Затем мы настраиваем метод `ADAuthenticationError`, который принимает любую ошибку, поступающую в iOS из ADAL. Кроме того, мы создаем `authContext`, который будет настраивать наш вызов в ADAL. Мы передаем ему свое *полномочие*, чтобы запустить процесс. Затем мы передаем `authContext` ссылку на родительский контроллер, чтобы можно было вернуться к нему. Кроме того, мы преобразуем `redirectURI`, который раньше был строкой в `settings.plist`, в тип NSURL, который необходим для ADAL. И, наконец, настроим `correlationId` — это просто UUID, который может проследить вызов от клиента до сервера и обратно. Он пригодится для отладки.

Теперь перейдем собственно к вызову ADAL — он будет отличаться от вызовов ADAL из iOS, с которыми вы имели дело раньше.

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

**scopes** — это области, передаваемые на сервер, который будет запрашиваться с сервера для входа пользователя. Для предварительной версии B2C мы передаем идентификатор клиента. Однако в будущем вместо это будет использоваться чтение областей. Когда это произойдет, документ будет обновлен. **addtionalScopes** — это дополнительные области, которые можно использовать в приложении. Этот параметр будет использован в будущем. **clientId** — идентификатор приложения, полученный с портала. **redirectURI** — это URI перенаправления для обратной публикации маркера. **identifier** — это способ идентификации пользователя, позволяющий определять наличие пригодного маркера в кэше, а не постоянно запрашивать у сервера новый маркер. Эту задачу выполняет тип `ADUserIdentifier`, и мы можем указать, что будет использоваться в качестве идентификатора. Это должно быть имя пользователя. **promptBehavior** — этот параметр устарел и должен выглядеть как AD\_PROMPT\_ALWAYS. **extraQueryParameters** — любой дополнительный параметр, который нужно передать на сервер в составе URL-адреса. **policy** — это вызываемая политика. Это самая важная часть данного руководства.

В параметре completionBlock мы передаем `ADAuthenticationResult`, который содержит наш маркер и сведения профиля (если вызов был выполнен успешно).

С помощью приведенного выше кода можно получить маркер для указанной политики. Этот маркер будет использоваться для вызова API.

#### Создание вызовов задач на сервер

Теперь, когда у нас есть маркер, необходимо передать его в API для авторизации.

Если помните, нам нужно реализовать три метода.

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

Метод `getTasksList` предоставляет массив, представляющий задачи на сервере. Методы `addTask` и `deleteTask` выполняют последующее действие и в случае успеха возвращают значение TRUE или FALSE.

Сначала напишем метод `getTaskList`.

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

Обсуждение кода задачи выходит за рамки данного пошагового руководства, однако есть нечто, на что вам нужно обратить внимание: метод `craftRequest`, принимающий URL-адрес нашей задачи. Мы используем этот метод и полученный маркер доступа для создания запроса к серверу. Давайте это запишем.

Добавим в файл samplesWebAPIConnector.m следующий код:

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

Как видите, он принимает веб-URI, добавляет к нему маркер с заголовком `Bearer` в HTTP и возвращает его нам. Мы вызываем API `getTokenClearingCache`. Это может показаться странным, но мы используем этот вызов для получения маркера из кэша и проверки его допустимости (эту операцию делают вызовы метода getToken*, опрашивая ADAL). Этот код применяется при каждом вызове. Теперь вернемся к составлению дополнительных методов задач.

Напишем метод `addTask`.

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

Представленный ниже код соответствует тому же шаблону, но в нем появляется еще один (последний) метод, который нам необходимо реализовать: `convertTaskToDictionary`. Он принимает наш массив и превращает его в объект словаря — его проще адаптировать к параметрам запроса, который нам нужно передавать на сервер. Этот код очень простой.

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

И, наконец, запишем метод `deleteTask`.

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

Нам осталось только добавить в приложение функцию выхода. Это довольно просто. Добавим в файл `sampleWebApiConnector.m` указанный ниже код.

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

## 9\. Запуск примера приложения

Наконец, можно собрать и запустить приложение в xCode. Зарегистрируйтесь или войдите в приложение и создайте задачи для пользователя, вошедшего в систему. Выйдите и снова войдите от имени другого пользователя. Создайте задачи для этого пользователя.

Обратите внимание, что в API хранятся задачи для каждого пользователя, поскольку API извлекает удостоверение пользователя из получаемого маркера доступа.

Готовый пример в виде ZIP-файла находится [здесь](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). Кроме того, можно клонировать его с GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Дальнейшие действия

Теперь можно перейти к более сложным темам B2C. Можно попробовать:

[Вызов веб-API node.js из веб-приложения node.js >>]()

[Настройка UX приложения B2C >>]()

<!---HONumber=AcomDC_0224_2016-->