<properties linkid="develop-mobile-tutorials-sso-with-adal-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender" />

# Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Магазин Windows C#" >Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" class="current">iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

В этом руководстве в проект быстрого запуска будет добавляться проверка подлинности с помощью библиотеки проверки подлинности Active Directory.

Чтобы иметь возможность проверки подлинности пользователей необходимо зарегистрировать приложение в Azure Active Directory (AAD). Это делается в два этапа. Сначала следует зарегистрировать мобильную службу и предоставить в ней разрешения. Во-вторых, нужно зарегистрировать приложение iOS и предоставить ему доступ к этим разрешениям.

> [WACOM.NOTE] Это руководство поможет лучше понять, как мобильные службы позволяют выполнять проверку подлинности Azure Active Directory единого входа для приложений iOS. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

В этом учебнике рассматриваются следующие основные действия:

1.  [Регистрация мобильной службы в Azure Active Directory][Регистрация мобильной службы в Azure Active Directory]
2.  [Регистрация приложения в Azure Active Directory][Регистрация приложения в Azure Active Directory]
3.  [Настройка мобильной службы для требования проверки подлинности][Настройка мобильной службы для требования проверки подлинности]
4.  [Добавление кода проверки подлинности в клиентское приложение][Добавление кода проверки подлинности в клиентское приложение]
5.  [Тестирование клиента с использованием проверки подлинности][Тестирование клиента с использованием проверки подлинности]

Для работы с данным учебником требуется следующее:

-   XCode 4.5 и iOS 6.0 (или более поздние версий)
-   Изучение учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными].
-   Пакет SDK для мобильных служб Microsoft Azure
-   Библиотека проверки подлинности [Active Directory для iOS][Active Directory для iOS]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Настройка мобильной службы для требования проверки подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Добавление кода проверки подлинности в клиентское приложение

1.  Загрузите [библиотеку проверки подлинности Active Directory для iOS][Active Directory для iOS] и добавьте ее в проект. Кроме того, добавьте раскадровки из источника ADAL.

2.  В QSTodoListViewController включите ADAL со следующим:

        #import "ADALiOS/ADAuthenticationContext.h"

3.  Затем добавьте следующий метод:

        - (void) loginAndGetData
        {    
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];   
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }

4.  В коде для метода `loginAndGetData` выше замените **INSERT-AUTHORITY-HERE** именем клиента, в котором подготавливалось приложение, используя формат <https://login.windows.net/tenant-name.onmicrosoft.com>. Это значение можно скопировать со вкладки "Домен" в Azure Active Directory на [портале управления Azure][портале управления Azure].

5.  В коде для метода `loginAndGetData` выше замените **INSERT-RESOURCE-URI-HERE** на **Универсальный код ресурса (URI) идентификатора приложения** для вашей мобильной службы. Если выполнялись инструкции из раздела [Регистрация в Azure Active Directory][Регистрация в Azure Active Directory], то URI ИД приложения должен быть аналогичен <https://todolist.azure-mobile.net/login/aad>.

6.  В коде для метода `loginAndGetData` выше замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

7.  В коде для метода `loginAndGetData` выше замените **INSERT-RESOURCE-URI-HERE** на конечную точку /login/done для вашей мобильной службы. Это должно выглядеть так: <https://todolist.azure-mobile.net/login/done>.

8.  В QSTodoListViewController измените `ViewDidLoad`, заменив `[self refresh]` на следующее:

        [self loginAndGetData];

## <a name="test-client"></a>Тестирование клиента с использованием проверки подлинности

1.  В меню «Продукт» щелкните «Выполнить» для запуска приложения
2.  Появится приглашение выполнить вход в Azure Active Directory.
3.  Приложение выполняет проверку подлинности и возвращает элементы списка дел.

![][0]

<!-- Anchors. -->
<!-- URLs. -->

  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
  [Регистрация мобильной службы в Azure Active Directory]: #register-mobile-service-aad
  [Регистрация приложения в Azure Active Directory]: #register-app-aad
  [Настройка мобильной службы для требования проверки подлинности]: #require-authentication
  [Добавление кода проверки подлинности в клиентское приложение]: #add-authentication-code
  [Тестирование клиента с использованием проверки подлинности]: #test-client
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/
  [Active Directory для iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [портале управления Azure]: https://manage.windowsazure.com/
  [Регистрация в Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [0]: ./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png
