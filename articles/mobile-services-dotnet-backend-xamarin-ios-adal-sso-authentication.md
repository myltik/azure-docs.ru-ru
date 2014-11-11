<properties linkid="develop-mobile-tutorials-sso-with-adal-xamarin-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Xamarin.iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Xamarin.iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender" />

# Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Магазин Windows C#" >Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
</div>

В этом руководстве в проект быстрого запуска будет добавляться проверка подлинности с помощью библиотеки проверки подлинности Active Directory.

Чтобы иметь возможность проверки подлинности пользователей необходимо зарегистрировать приложение в Azure Active Directory (AAD). Это делается в два этапа. Сначала следует зарегистрировать мобильную службу и предоставить в ней разрешения. Далее необходимо зарегистрировать приложение Xamarin.iOS и предоставить ему доступ к этим разрешениям.

> [WACOM.NOTE] Этот учебник поможет лучше понять, как мобильные службы позволяют выполнять проверку подлинности Azure Active Directory единого входа для приложений Xamarin.iOS. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

В этом учебнике рассматриваются следующие основные действия:

1.  [Регистрация мобильной службы в Azure Active Directory][Регистрация мобильной службы в Azure Active Directory]
2.  [Регистрация приложения в Azure Active Directory][Регистрация приложения в Azure Active Directory]
3.  [Настройка мобильной службы для требования проверки подлинности][Настройка мобильной службы для требования проверки подлинности]
4.  [Добавление кода проверки подлинности в клиентское приложение][Добавление кода проверки подлинности в клиентское приложение]
5.  [Тестирование клиента с использованием проверки подлинности][Тестирование клиента с использованием проверки подлинности]

Для работы с данным учебником требуется следующее:

-   XCode 4.5 и iOS 6.0 (или более поздние версии)
-   Visual Studio с [расширением Xamarin][расширением Xamarin] или [Xamarin Studio][Xamarin Studio] в OS X
-   Изучение учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными].
-   Microsoft Azure Mobile Services SDK
-   [Привязка Xamarin для библиотеки проверки подлинности Active Directory для iOS][Привязка Xamarin для библиотеки проверки подлинности Active Directory для iOS].

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Настройка мобильной службы для требования проверки подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Добавление кода проверки подлинности в клиентское приложение

1.  Добавьте привязку Xamarin для библиотеки проверки подлинности Active Directory в проект Xamarin.iOS. В Visual Studio 2013 щелкните правой кнопкой мыши **Ссылки** и нажмите кнопку **Добавить ссылку**. Затем перейдите в библиотеку привязок и щелкните кнопку **Добавить**. Убедитесь, что также добавили раскадровки из источника ADAL.

2.  Добавьте следующие методы в класс QSTodoService:

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

3.  В коде для метода `AuthenticateAsync` выше замените **INSERT-AUTHORITY-HERE** именем клиента, в котором подготавливалось приложение, используя формат <https://login.windows.net/tenant-name.onmicrosoft.com>. Это значение можно скопировать со вкладки "Домен" в Azure Active Directory на [портале управления Azure][портале управления Azure].

4.  В коде для метода `AuthenticateAsync` выше замените **INSERT-RESOURCE-URI-HERE** на **Универсальный код ресурса (URI) идентификатора приложения** для вашей мобильной службы. Если выполнялись инструкции из раздела [Регистрация в Azure Active Directory][Регистрация в Azure Active Directory], то URI ИД приложения должен быть аналогичен <https://todolist.azure-mobile.net/login/aad>.

5.  В коде для метода `AuthenticateAsync` выше замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

6.  В коде для метода `AuthenticateAsync` выше замените **INSERT-REDIRECT-URI-HERE** на конечную точку /login/done для вашей мобильной службы. Это значение должно быть аналогичным <https://todolist.azure-mobile.net/login/done>.

7.  В QSTodoListViewController измените **ViewDidLoad**, добавив следующий код перед вызовом RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>Тестирование клиента с использованием проверки подлинности

1.  В меню "Запуск" щелкните кнопку "Запустить" для запуска приложения.
2.  Появится приглашение выполнить вход в Azure Active Directory.
3.  Приложение выполняет проверку подлинности и возвращает элементы списка дел.

![][0]

<!-- Anchors. -->
<!-- URLs. -->

  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [Регистрация мобильной службы в Azure Active Directory]: #register-mobile-service-aad
  [Регистрация приложения в Azure Active Directory]: #register-app-aad
  [Настройка мобильной службы для требования проверки подлинности]: #require-authentication
  [Добавление кода проверки подлинности в клиентское приложение]: #add-authentication-code
  [Тестирование клиента с использованием проверки подлинности]: #test-client
  [расширением Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Приступая к работе с данными]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [Привязка Xamarin для библиотеки проверки подлинности Active Directory для iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [портале управления Azure]: https://manage.windowsazure.com/
  [Регистрация в Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [0]: ./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png
