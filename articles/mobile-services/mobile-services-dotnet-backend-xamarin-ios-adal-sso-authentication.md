<properties 
	pageTitle="Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory (Xamarin.iOS) | Microsoft Azure" 
	description="Узнайте, как аутентифицировать пользователей для единого входа с помощью ADAL в приложении Xamarin.iOS." 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="dwrede" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="mahender"/>

# Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Обзор

В этом руководстве в проект быстрого запуска будет добавляться проверка подлинности с помощью библиотеки проверки подлинности Active Directory.

Чтобы иметь возможность проверки подлинности пользователей необходимо зарегистрировать приложение в Azure Active Directory (AAD). Это делается в два этапа. Сначала следует зарегистрировать мобильную службу и предоставить в ней разрешения. Далее необходимо зарегистрировать приложение Xamarin.iOS и предоставить ему доступ к этим разрешениям.


>[AZURE.NOTE]Это руководство поможет лучше понять, как мобильные службы обеспечивают единый вход с проверкой подлинности Azure Active Directory для приложений Xamarin.iOS. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].

##Предварительные требования

Для работы с данным учебником требуется следующее:

* XCode 4.5 и iOS 6.0 (или более поздние версии) 
* Visual Studio с [расширением Xamarin] или [Xamarin Studio] в OS X
* Изучение учебника [Приступая к работе с мобильными службами] или [Приступая к работе с данными].
* Microsoft Azure Mobile Services SDK
* [Привязка Xamarin для библиотеки проверки подлинности Active Directory для iOS].

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##Настройка мобильной службы для требования проверки подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Добавление кода проверки подлинности в клиентское приложение

1. Добавьте привязку Xamarin для библиотеки проверки подлинности Active Directory в проект Xamarin.iOS. В Visual Studio 2013 щелкните правой кнопкой мыши **Ссылки** и нажмите кнопку **Добавить ссылку**. Затем перейдите в библиотеку привязок и щелкните кнопку **Добавить**. Убедитесь, что также добавили раскадровки из источника ADAL.

2. Добавьте следующие методы в класс QSTodoService:

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

6. В коде для метода `AuthenticateAsync` выше замените **INSERT-AUTHORITY-HERE** именем клиента, в котором подготавливалось приложение, используя формат https://login.windows.net/tenant-name.onmicrosoft.com. Это значение можно скопировать со вкладки "Домен" в Azure Active Directory на [портале управления Azure].

7. В коде для метода `AuthenticateAsync` выше замените **INSERT-RESOURCE-URI-HERE** значением **URI кода приложения** для своей мобильной службы. Если вы выполнили инструкции из раздела [Регистрация в Azure Active Directory], то URI кода приложения должен быть аналогичен https://todolist.azure-mobile.net/login/aad.

8. В коде для метода `AuthenticateAsync` выше замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

9. В коде для метода `AuthenticateAsync` выше замените **INSERT-REDIRECT-URI-HERE** конечной точкой /login/done для своей мобильной службы. Это должно выглядеть приблизительно так: https://todolist.azure-mobile.net/login/done.


3. В QSTodoListViewController измените **ViewDidLoad**, добавив следующий код перед вызовом RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

##Тестирование клиента с использованием проверки подлинности

1. В меню "Запуск" щелкните кнопку "Запустить" для запуска приложения. 
2. Появится приглашение выполнить вход в Azure Active Directory.  
3. Приложение выполняет проверку подлинности и возвращает элементы списка дел.

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Приступая к работе с данными]: mobile-services-ios-get-started-data.md
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Регистрация в Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[портале управления Azure]: https://manage.windowsazure.com/
[Привязка Xamarin для библиотеки проверки подлинности Active Directory для iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[расширением Xamarin]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download

<!---HONumber=August15_HO7-->