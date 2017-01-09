---
title: "Приступая к работе с Azure AD для iOS | Документация Майкрософт"
description: "Практическое руководство по созданию приложения для iOS, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, по протоколу OAuth."
services: active-directory
documentationcenter: ios
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/16/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d3fb8e98d36c4a031458b5400147614510b1b064


---
# <a name="integrate-azure-ad-into-an-ios-app"></a>Интеграция Azure AD в приложение для iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Клиентские приложения для iOS, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL).  Единственная задача ADAL — упрощение процесса получения приложением маркеров доступа.  Чтобы показать, насколько это просто, создадим приложение To Do List (список дел) на Objective C, которое:

* Получает маркеры доступа для вызова интерфейса API Graph Azure AD с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Осуществляет поиск пользователей в каталоге по псевдониму.

Для создания полного рабочего приложения необходимо:

1. Зарегистрировать приложение в Azure AD.
2. установить и настроить ADAL;
3. использовать ADAL для получения маркеров из Azure AD.

Чтобы начать работу, [скачайте схему приложения](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) или [скачайте готовый пример](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  Вам также потребуется клиент Azure AD, в котором можно создавать пользователей и регистрировать приложение.  Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

> [!TIP]
> Воспользуйтесь предварительной версией нашего нового [портала разработчиков](https://identity.microsoft.com/Docs/iOS) , который поможет вам приступить к работе с Azure Active Directory через несколько минут.  Портал разработчиков поможет зарегистрировать приложение и интегрировать Azure AD в коде.  Завершив работу, вы получите простое приложение, с помощью которого выполняется аутентификация пользователей в клиенте и на сервере, принимающем маркеры и проводящем проверку.
>
>

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. Выбор URI перенаправления для iOS*
Для безопасного запуска приложений в некоторых сценариях использования единого входа требуется создать **URI перенаправления** в определенном формате. URI перенаправления используются, чтобы гарантировать, что маркеры получает именно то приложение, которое их запрашивало.

Формат URI перенаправления в iOS:

```
<app-scheme>://<bundle-id>
```

* Схема **aap-scheme** регистрируется в проекте XCode и используется для вызова из других приложений. Данные сведения можно найти в файле Info.plist (URL Types -> URL Identifier). Если вы еще не создали или не настроили хотя бы одну схему, следует сделать это.
* **bundle-id** — это идентификатор пакета, который можно найти в разделе "identity" параметров проекта XCode.

Пример для рассматриваемого проекта QuickStart: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2. Регистрация приложения DirectorySearcher*
Чтобы приложение могло получать маркеры, сначала необходимо его зарегистрировать в клиенте Azure AD и предоставить ему разрешение на доступ к интерфейсу Graph API Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com).
2. В правом верхнем углу щелкните учетную запись и в списке **Каталог** выберите клиент Active Directory, где есть разрешения администратора.
3. В фильтре поиска введите **регистрация приложений**.
4. Щелкните **Регистрация приложений** и нажмите кнопку **Добавить**.
5. Следуйте инструкциям на экране, а затем создайте новое **Собственное клиентское приложение**. **Имя** приложения является его описанием для конечных пользователей. **URI перенаправления** — это универсальный код ресурса (URI), который используется для возврата маркеров приложению. Укажите значение, специфичное для вашего приложения. Щелкните **Создать**, чтобы создать приложение.
6. На портале Azure выберите приложение, щелкните **Параметры** и выберите **Свойства**.
7. Найдите значение идентификатора приложения и скопируйте его в буфер обмена.
8. Настройте разрешения для приложения. В меню "Параметры" выберите раздел "Необходимые разрешения", щелкните **Добавить**, а затем — **Выбор API** и выберите Windows Azure Active Directory (это API AADGraph). Затем щелкните **Выбор разрешений** и выберите разрешение "Чтение данных каталога".

## <a name="3-install--configure-adal"></a>*3. Установка и настройка ADAL*
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением.  Чтобы ADAL могла обмениваться информацией с Azure AD, необходимо предоставить некоторую информацию о регистрации вашего приложения.

* Для начала добавьте ADAL в проект DirectorySearcher, используя Cocoapods.

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

* В проекте QuickStart откройте файл `settings.plist`.  Замените значения элементов в соответствующем разделе на значения, введенные на портале Azure.  Код будет использовать эти значения при каждом обращении к библиотеке ADAL.
  * `tenant` — это домен вашего клиента Azure AD, например contoso.onmicrosoft.com
  * `clientId` — это идентификатор clientId приложения, скопированный с портала.
  *  `redirectUri` — это URL-адрес перенаправления, зарегистрированный на портале.

## <a name="4----use-adal-to-get-tokens-from-aad"></a>*4.    Использование библиотеки ADAL для получения маркеров из Azure AD*
Основной принцип ADAL состоит в том, что каждый раз, когда вашему приложению необходим маркер доступа, оно будет просто вызывать сompletionBlock `+(void) getToken : `, а библиотека ADAL сделает все остальное.  

* В проекте `QuickStart` откройте `GraphAPICaller.m` и найдите комментарий "`// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.`" вверху.  Здесь вы указываете координаты, которые требуются библиотеке ADAL для взаимодействия с Azure AD, и сообщаете способ кэширования маркеров.

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

* Мы будем использовать этот маркер для поиска пользователей в графе. Найдите комментарий `// TODO: implement SearchUsersList`. Этот метод выполняет запрос GET в интерфейс API Graph службы Azure AD для получения списка пользователей, чьи UPN начинаются с определенного слова.  Но для отправки запросов в Graph API необходимо включить access_token в заголовок `Authorization` запроса — именно отсюда ADAL начинает свою работу.

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
* Когда приложение запрашивает маркер путем вызова `getToken(...)`, библиотека ADAL пытается вернуть маркер без запроса учетных данных пользователя.  Если ADAL решит, что пользователь должен войти в систему для получения маркера, то служба отобразит диалоговое окно входа, соберет учетные данные пользователя и вернет маркер после успешной проверки подлинности.  Если библиотеке ADAL не удастся по какой-либо причине вернуть маркер, она вызовет исключение `AdalException`.
* Обратите внимание, что объект `AuthenticationResult` содержит объект `tokenCacheStoreItem`, который может использоваться для сбора сведений, необходимых приложению.  В проекте QuickStart объект `tokenCacheStoreItem` используется, чтобы определить, была ли выполнена проверка подлинности.

## <a name="step-5-build-and-run-the-application"></a>Этап 5. Компиляция и запуск приложения
Поздравляем! Теперь у нас есть рабочее приложение для iOS, которое позволяет проверять подлинность пользователей, безопасно вызывать методы веб-API по протоколу OAuth 2.0 и получать основные сведения о пользователе.  Если же вы этого еще не сделали, пришло время добавить в клиент нескольких пользователей.  Запустите приложение QuickStart и выполните вход как один из пользователей.  Осуществите поиск других пользователей по их имени участника-пользователя.  Закройте приложение и снова запустите его.  Обратите внимание на то, что пользовательский сеанс остался без изменений.

Библиотека ADAL упрощает включение в приложение всех этих типичных функций работы с удостоверением.  Он отвечает за всю грязную работу: управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа, обновление истекших маркеров и многое другое.  Все, что вам действительно нужно знать, — это вызов интерфейса API `getToken`.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Дополнительные сценарии
Теперь можно приступить к изучению других сценариев.  Можно попробовать:

* [Безопасность веб-API с Azure AD для Node.JS](active-directory-devquickstarts-webapi-nodejs.md)
* См. раздел [Включение единого входа в нескольких приложениях iOS с помощью ADAL](active-directory-sso-ios.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]



<!--HONumber=Jan17_HO1-->


