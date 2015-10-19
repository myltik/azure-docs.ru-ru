<properties
        pageTitle="Проверяйте подлинность пользователей приложения iOS с помощью проверки подлинности Azure Active Directory."
        description="Выполнение входа пользователей в приложение iOS с помощью библиотеки проверки подлинности Active Directory."
        documentationCenter="Mobile"
        authors="mattchenderson"
        services="app-service\mobile"
        manager="dwrede" />

<tags ms.service="app-service-mobile"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios"
ms.devlang="objective-c"
ms.topic="article"
ms.date="09/14/2015"
ms.author="mahender" />

# Добавление проверки подлинности Azure Active Directory в приложение iOS

[AZURE.INCLUDE [app-service-mobile-selector-aad-sso](../../includes/app-service-mobile-selector-aad-sso.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

В этом руководстве в проект быстрого запуска будет добавляться проверка подлинности с помощью библиотеки проверки подлинности Active Directory (ADAL). Также вы можете включить проверку подлинности более простым способом — при помощи пакета SDK для мобильных приложений, как описано в учебнике [Добавление проверки подлинности в приложение]. Использование ADAL обеспечивает более интегрированную проверку подлинности конечных пользователей и более широкие возможности доступа к другим ресурсам, защищенным Azure AD.

Чтобы получить возможность проверки подлинности пользователей при помощи ADAL, необходимо зарегистрировать приложение в клиенте Azure Active Directory (AAD). Это делается в два этапа. Сначала необходимо зарегистрировать службу приложений и предоставить в ней разрешения. Далее нужно зарегистрировать приложение iOS и предоставить ему доступ к этим разрешениям.

Для работы с данным учебником требуется следующее:

* XCode 4.5 и iOS 6.0 (или более поздние версии)
* Прохождение учебника [Начало работы с мобильными приложениями].
* Microsoft Azure Mobile Services SDK
* Библиотека проверки подлинности [Active Directory для iOS]

##<a name="review"></a>Проверка конфигурации сервера проекта (необязательное действие)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

## <a name="register-application"></a>Регистрация приложения в Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-adal-register-app](../../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>Настройка обязательной проверки подлинности в приложении

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-adal"></a>Добавление ссылки на библиотеку проверки подлинности Active Directory

1. Загрузите [библиотеку проверки подлинности Active Directory для iOS].

2. В Xcode Navigator выберите свой проект, щелкните **Файл** и выберите пункт **Добавить файлы в...**. Перейдите в папку, в которую загрузили библиотеку, и выберите файл **ADALiOS.xcodeproj**.

3. Снова выберите свой проект и откройте вкладку **Параметры сборки**. Перейдите в раздел **Компоновка** и добавьте `-ObjC` в параметр **Другие флаги компоновщика**.

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

4. В коде для метода `loginAndGetData` выше замените **INSERT-AUTHORITY-HERE** именем клиента, в котором подготавливалось приложение, используя формат https://login.windows.net/tenant-name.onmicrosoft.com. Это значение можно скопировать со вкладки "Домен" в Azure Active Directory на [портале управления Azure].

5. В коде для метода `loginAndGetData` выше замените **INSERT-RESOURCE-URI-HERE** на **URI идентификатора приложения** для мобильного приложения. Если вы выполнили инструкции из раздела [Настройка мобильного приложения в Azure Active Directory], то ваш URI идентификатора приложения должен быть аналогичен https://contosogateway.azurewebsites.net/login/aad.

6. В коде для метода `loginAndGetData` выше замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

7. В коде для метода `loginAndGetData` выше замените **INSERT-REDIRECT-URI-HERE** конечной точкой «/login/done» для шлюза службы приложений. Это должно быть аналогично https://contosogateway.azurewebsites.net/login/done.

8. В QSTodoListViewController измените `viewDidLoad`, заменив `[self refresh]` на следующее:

        [self loginAndGetData];

## <a name="test-client"></a>Тестирование клиента с использованием проверки подлинности

1. В меню «Продукт» щелкните «Выполнить» для запуска приложения
2. Появится приглашение выполнить вход в Azure Active Directory.  
3. Приложение выполняет проверку подлинности и возвращает элементы списка дел.

<!-- URLs. -->
[Настройка мобильного приложения в Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[портале управления Azure]: https://manage.windowsazure.com/
[Active Directory для iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
[библиотеку проверки подлинности Active Directory для iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [Начало работы с мобильными приложениями]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
 [Добавление проверки подлинности в приложение]: app-service-mobile-dotnet-backend-ios-get-started-users-preview.md

<!---HONumber=Oct15_HO2-->