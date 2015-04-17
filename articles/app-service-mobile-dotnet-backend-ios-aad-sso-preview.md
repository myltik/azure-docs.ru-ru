<properties 
        pageTitle="Проверка подлинности приложения iOS с помощью единого входа Azure Active Directory" 
        description="Выполнение входа пользователей в приложение iOS с помощью библиотеки проверки подлинности Active Directory." 
        documentationCenter="Mobile" 
        authors="mattchenderson" 
        services="app-service\mobile" 
        manager="dwrede" 
        editor=""/>

<tags ms.service="app-service"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios" 
ms.devlang="objective-c" 
ms.topic="article" 
ms.date="02/20/2015" 
ms.author="mahender" />

# Добавление единого входа Azure Active Directory в приложение iOS

[WACOM.INCLUDE [app-service-mobile-selector-aad-sso](../includes/app-service-mobile-selector-aad-sso.md)]

В этом руководстве в проект быстрого запуска будет добавляться проверка подлинности с помощью библиотеки проверки подлинности Active Directory.

Чтобы получить возможность проверки подлинности пользователей, необходимо зарегистрировать приложение в клиенте Azure Active Directory (AAD). Это делается в два этапа. Сначала необходимо зарегистрировать службу приложений и предоставить в ней разрешения. Далее нужно зарегистрировать приложение iOS и предоставить ему доступ к этим разрешениям.

Для работы с данным учебником требуется следующее:

* XCode 4.5 и iOS 6.0 (или более поздние версии)
* Прохождение учебника [Начало работы с мобильными приложениями].
* Microsoft Azure Mobile Services SDK
* [Библиотека проверки подлинности Active Directory для iOS]

## <a name="register-application"></a>Регистрация приложения в Azure Active Directory

[WACOM.INCLUDE [app-service-mobile-adal-register-app](../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>Настройка обязательной проверки подлинности в приложении

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

## <a name="add-adal"></a>Добавление ссылки на библиотеку проверки подлинности Active Directory

1. Скачайте [Библиотека проверки подлинности Active Directory для iOS].

2. В Xcode Navigator выберите свой проект, щелкните **Файл**, **Добавить файлы в...**. Перейдите в папку, в которую скачали библиотеку, и выберите **ADALiOS.xcodeproj**.

3. Снова выберите свой проект и откройте вкладку **Параметры сборки**. Перейдите в раздел **Компоновка** и добавьте `-ObjC` в параметр **Другие флаги компоновки**.

4. Откройте вкладку **Этапы сборки**. В разделе **Целевые зависимости** добавьте `ADALiOS`.

5. В разделе **Компоновка двоичного файла с библиотеками** добавьте `libADALiOS.a`.

Теперь вы сможете ссылаться на библиотеку проверки подлинности Active Directory в проекте.

## <a name="add-authentication-code"></a>Добавление кода проверки подлинности в клиентское приложение

2. В QSTodoListViewController включите ADAL со следующим:

        #import "ADALiOS/ADAuthenticationContext.h"

3. Затем добавьте следующий метод:

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

4. В коде для метода `loginAndGetData` выше замените **INSERT-AUTHORITY-HERE** именем клиента, в котором подготовлено приложение. Формат должен быть следующим: https://login.windows.net/tenant-name.onmicrosoft.com. Это значение можно скопировать со вкладки "Домен" в Azure Active Directory на [Портал управления Azure].

5. В коде для метода `loginAndGetData` выше замените **INSERT-RESOURCE-URI-HERE** на **URI идентификатора приложения** для мобильного приложения. Если вы следовали инструкциям в разделе [Настройка мобильного приложения с помощью Azure Active Directory], URI идентификатора приложения должен быть аналогичен следующему: https://contosogateway.azurewebsites.net/login/aad.

6. В коде для метода `loginAndGetData` выше замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из нативного клиентского приложения.

7. В коде для метода `loginAndGetData` выше замените **INSERT-REDIRECT-URI-HERE** конечной точкой "/login/done" шлюза службы приложений. Она должна быть аналогична https://contosogateway.azurewebsites.net/login/done.

8. В QSTodoListViewController измените `ViewDidLoad`, заменив `[self refresh]` следующим:

        [self loginAndGetData];

## <a name="test-client"></a>Тестирование клиента с использованием проверки подлинности

1. В меню "Продукт" щелкните "Выполнить" для запуска приложения
2. Появится приглашение выполнить вход в Azure Active Directory.  
3. Приложение выполняет проверку подлинности и возвращает элементы списка дел.

<!-- URLs. -->
[Настройка мобильного приложения с помощью Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[Портал управления Azure]: https://manage.windowsazure.com/
[Библиотека проверки подлинности Active Directory для iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [Начало работы с мобильными приложениями]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

<!--HONumber=49-->