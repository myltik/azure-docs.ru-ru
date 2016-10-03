<properties
	pageTitle="Приступая к работе с Azure AD для iOS | Microsoft Azure"
	description="Практическое руководство по созданию приложения для iOS, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, по протоколу OAuth."
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# Интеграция Azure AD в приложение для iOS

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Клиентские приложения для iOS, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственная задача ADAL — упрощение процесса получения приложением маркеров доступа. Чтобы показать, насколько это просто, создадим приложение To Do List (список дел) на Objective C, которое:

-	Получает маркеры доступа для вызова интерфейса API Graph Azure AD с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Осуществляет поиск пользователей в каталоге по псевдониму.

Для создания полного рабочего приложения необходимо:

2. Зарегистрировать приложение в Azure AD.
3. установить и настроить ADAL;
5. использовать ADAL для получения маркеров из Azure AD.

Чтобы начать работу, [загрузите каркас приложения](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) или [завершенный пример](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Вам также потребуется клиент Azure AD, в котором можно будет создавать пользователей и зарегистрировать приложение. Если у вас еще нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## *1. Выбор URI перенаправления для iOS*

Для безопасного запуска приложений в некоторых сценариях использования единого входа требуется создать **URI перенаправления** в определенном формате. URI перенаправления используются, чтобы гарантировать, что маркеры получает именно то приложение, которое их запрашивало.

Формат URI перенаправления в iOS:

```
<app-scheme>://<bundle-id>
```

- 	Схема **aap-scheme** регистрируется в проекте XCode и используется для вызова из других приложений. Данные сведения можно найти в файле Info.plist (URL Types -> URL Identifier). Если вы еще не создали или не настроили хотя бы одну схему, следует сделать это.
- 	**bundle-id** — это идентификатор пакета, который можно найти в разделе "identity" параметров проекта XCode.

Пример для рассматриваемого проекта QuickStart: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## *2. Регистрация приложения DirectorySearcher*
Чтобы приложение могло получать маркеры, сначала необходимо его зарегистрировать в клиенте Azure AD и предоставить ему разрешение на доступ к интерфейсу Graph API Azure AD:

-	Войдите на портал управления Azure.
-	В левой панели навигации щелкните **Active Directory**.
-	Выберите клиента, в котором будет зарегистрировано приложение.
-	Перейдите на вкладку **Приложения** и нажмите кнопку **Добавить** в нижней панели.
-	Следуйте инструкциям на экране, а затем создайте новое **Собственное клиентское приложение**.
    -	**Имя** приложения отображает его описание конечным пользователям.
    -	**URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения любых маркеров, запрошенных приложением. Введите значение для конкретного приложения, используя приведенные сведения.
-	После завершения регистрации Azure AD присваивает приложению уникальный идентификатор клиента. Это значение понадобится в следующих разделах, поэтому скопируйте его с вкладки **Настройка**.
- Также на вкладке **Настройка** найдите раздел "Разрешения для других приложений". Для приложения Azure Active Directory добавьте разрешение на **Допуск к каталогу вашей организации** в списке **Делегированные разрешения**. Это позволит приложению запрашивать интерфейс Graph API для пользователей.

## *3. Установка и настройка ADAL*
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением. Чтобы ADAL могла обмениваться информацией с Azure AD, необходимо предоставить некоторую информацию о регистрации вашего приложения.
-	Для начала добавьте ADAL в проект DirectorySearcher, используя Cocoapods.

```
$ vi Podfile
```
Добавьте в файл Podfile следующий код:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Теперь загрузите файл Podfile с помощью Cocoapods. Будет создана новая рабочая область XCode.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-	В проекте QuickStart откройте файл `settings.plist`. Замените значения элементов в соответствующем разделе на значения, введенные на портале Azure. Код будет использовать эти значения при каждом обращении к библиотеке ADAL.
    -	`tenant` — это домен вашего клиента Azure AD, например contoso.onmicrosoft.com
    -	`clientId` — это идентификатор clientId приложения, скопированный с портала.
    -	`redirectUri` — это URL-адрес перенаправления, зарегистрированный на портале.

## *4. Использование библиотеки ADAL для получения маркеров из Azure AD*
Основной принцип ADAL состоит в том, что каждый раз, когда вашему приложению необходим маркер доступа, оно будет просто вызывать сompletionBlock `+(void) getToken : `, а библиотека ADAL сделает все остальное.

-	В проекте `QuickStart` откройте `GraphAPICaller.m` и найдите комментарий "`// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.`" вверху. Здесь вы указываете координаты, которые требуются библиотеке ADAL для взаимодействия с Azure AD, и сообщаете способ кэширования маркеров.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- Мы будем использовать этот маркер для поиска пользователей в графе. Найдите комментарий `// TODO: implement SearchUsersList`. Этот метод выполняет запрос GET в интерфейс API Graph службы Azure AD для получения списка пользователей, чьи UPN начинаются с определенного слова. Но для отправки запросов в Graph API необходимо включить access\_token в заголовок `Authorization` запроса — именно отсюда ADAL начинает свою работу.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
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

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

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
                         s.name =[keyValuePairs valueForKey:@"givenName"];

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
     }];

}

```
- Когда приложение запрашивает маркер путем вызова `getToken(...)`, библиотека ADAL пытается вернуть маркер без запроса учетных данных пользователя. Если ADAL решит, что пользователь должен выполнить вход для получения маркера, будет отображено диалоговое окно входа. Введенные учетные данные пользователя будут использованы для проверки подлинности, и в случае успешной проверки библиотека вернет маркер. Если библиотеке ADAL не удастся по какой-либо причине вернуть маркер, она вызовет исключение `AdalException`.
- Обратите внимание, что объект `AuthenticationResult` содержит объект `tokenCacheStoreItem`, который может использоваться для сбора сведений, необходимых приложению. В проекте QuickStart объект `tokenCacheStoreItem` используется, чтобы определить, была ли выполнена проверка подлинности.


## Этап 5. Компиляция и запуск приложения



Поздравляем! Теперь у нас есть рабочее приложение для iOS, которое позволяет проверять подлинность пользователей, безопасно вызывать методы веб-API по протоколу OAuth 2.0 и получать основные сведения о пользователе. Если же вы этого еще не сделали, пришло время добавить в клиент нескольких пользователей. Запустите приложение QuickStart и выполните вход как один из пользователей. Осуществите поиск других пользователей по их имени участника-пользователя. Закройте приложение и снова запустите его. Обратите внимание на то, что пользовательский сеанс остался без изменений.

Библиотека ADAL упрощает включение в приложение всех этих типичных функций работы с удостоверением. Он отвечает за всю грязную работу: управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа, обновление истекших маркеров и многое другое. Все, что вам действительно нужно знать, — это вызов интерфейса API `getToken`.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).

## Дополнительные сценарии
Теперь можно приступить к изучению других сценариев. Можно попробовать:

- [Безопасность веб-API с Azure AD для Node.JS](active-directory-devquickstarts-webapi-nodejs.md)
- См. раздел [How to enable cross-app SSO on iOS using ADAL](active-directory-sso-ios.md).

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->