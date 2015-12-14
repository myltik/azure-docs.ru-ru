<properties
	pageTitle="Проверка подлинности приложения для единого входа с помощью библиотеки проверки подлинности Active Directory (iOS) | Microsoft Azure"
	description="Узнайте, как аутентифицировать пользователей для единого входа с помощью ADAL в приложении iOS."
	documentationCenter="ios"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/18/2015"
	ms.author="mahender"/>

# Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Обзор

В этом руководстве в проект быстрого запуска будет добавляться проверка подлинности с помощью библиотеки проверки подлинности Active Directory.

Чтобы иметь возможность проверки подлинности пользователей необходимо зарегистрировать приложение в Azure Active Directory (AAD). Это делается в два этапа. Сначала следует зарегистрировать мобильную службу и предоставить в ней разрешения. Во-вторых, нужно зарегистрировать приложение iOS и предоставить ему доступ к этим разрешениям.


>[AZURE.NOTE]Это руководство поможет лучше понять, как мобильные службы позволяют выполнять проверку подлинности Azure Active Directory единого входа для приложений iOS. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].


##Предварительные требования


Для работы с данным учебником требуется следующее:

* XCode 4.5 и iOS 6.0 (или более поздние версии)
* Выполнение заданий учебника [Приступая к работе с мобильными службами].
* Microsoft Azure Mobile Services SDK
* Библиотека проверки подлинности [Active Directory для iOS]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##Настройка мобильной службы для требования проверки подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Добавление кода проверки подлинности в клиентское приложение

1. Загрузите [библиотеку проверки подлинности Active Directory для iOS] и добавьте ее в проект. Кроме того, добавьте раскадровки из источника ADAL.

2. В QSTodoListViewController включите ADAL со следующим:

        #import "ADALiOS/ADAuthenticationContext.h"

2. Затем добавьте следующий метод:

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


6. В коде для метода `loginAndGetData` выше замените **INSERT-AUTHORITY-HERE** именем клиента, в котором подготавливалось приложение, используя формат https://login.windows.net/tenant-name.onmicrosoft.com. Это значение можно скопировать со вкладки "Домен" в Azure Active Directory на [классическом портале Azure].

7. В коде для метода `loginAndGetData` выше замените **INSERT-RESOURCE-URI-HERE** значением **URI кода приложения** для своей мобильной службы. Если вы выполнили инструкции из раздела [Регистрация в Azure Active Directory], то URI кода приложения должен быть аналогичен https://todolist.azure-mobile.net/login/aad.

8. В коде для метода `loginAndGetData` выше замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

9. В коде для метода `loginAndGetData` выше замените **INSERT-REDIRECT-URI-HERE** конечной точкой /login/done для своей мобильной службы. Это должно выглядеть приблизительно так: https://todolist.azure-mobile.net/login/done.


3. В QSTodoListViewController измените `ViewDidLoad`, заменив `[self refresh]` следующим:

        [self loginAndGetData];

##Тестирование клиента с использованием проверки подлинности

1. В меню «Продукт» щелкните «Выполнить» для запуска приложения
2. Появится приглашение выполнить вход в Azure Active Directory.
3. Приложение выполняет проверку подлинности и возвращает элементы списка дел.

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-ios-get-started.md
[Регистрация в Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[классическом портале Azure]: https://manage.windowsazure.com/
[Active Directory для iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
[библиотеку проверки подлинности Active Directory для iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios

<!---HONumber=AcomDC_1203_2015-->