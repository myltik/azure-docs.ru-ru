<properties linkid="develop-mobile-tutorials-sso-with-adal" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Магазин Windows C#" class="current">Магазин Windows C#</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

В этом руководстве в проект быстрого запуска будет добавляться проверка подлинности с помощью библиотеки проверки подлинности Active Directory.

Чтобы иметь возможность проверки подлинности пользователей необходимо зарегистрировать приложение в Azure Active Directory (AAD). Это делается в два этапа. Сначала следует зарегистрировать мобильную службу и предоставить в ней разрешения. Затем нужно зарегистрировать приложение Магазина Windows и предоставить ему доступ к этим разрешениям.

> [WACOM.NOTE] Это руководство поможет лучше понять, как мобильные службы позволяют выполнять проверку подлинности Azure Active Directory единого входа для приложений Магазина Windows. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами][].

В этом учебнике рассматриваются следующие основные действия:

1.  [Регистрация мобильной службы в Azure Active Directory][]
2.  [Регистрация приложения в Azure Active Directory][]
3.  [Настройка мобильной службы для требования проверки подлинности][]
4.  [Добавление кода проверки подлинности в клиентское приложение][]
5.  [Тестирование клиента с использованием проверки подлинности][]

Для работы с данным учебником требуется следующее:

-   Visual Studio 2013 в Windows 8.1.
-   Изучение учебника [Приступая к работе с мобильными службами][] или [Приступая к работе с данными][].
-   Пакет NuGet пакета SDK мобильных служб Microsoft Azure
-   Пакет NuGet библиотеки проверки подлинности Active Directory

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][]]

## <a name="register-app-aad"></a>Регистрация приложения в Azure Active Directory

Чтобы зарегистрировать приложение в Azure Active Directory, необходимо сопоставить его с Магазином Windows и получить идентификатор безопасности пакета (SID) для приложения. SID пакета регистрируется с параметрами собственного приложения в Azure Active Directory.

### Сопоставление приложения с новым именем приложения Магазина

1.  В Visual Studio щелкните правой кнопкой мыши проект клиентского приложения, а затем выберите пункты **Магазин** и **Связать приложение с Магазином**.

    ![][]

2.  Войдите в свою учетную запись в Центре разработки.

3.  Введите имя, которое планируется зарезервировать для приложения, и нажмите кнопку **Зарезервировать**.

    ![][1]

4.  Выберите новое имя приложения и нажмите кнопку **Далее**.

5.  Нажмите **Сопоставить**, чтобы сопоставить приложение с именем в Магазине.

### Получение SID пакета для приложения

Теперь необходимо получить SID пакета, который будет настроен с параметрами собственного приложения.

1.  Войдите в свою [панель мониторинга Центра разработки для Windows][] и нажмите кнопку **Правка** в приложении.

    ![][2]

2.  Затем нажмите **Службы**.

    ![][3]

3.  Затем нажмите **Сайт служб Live**.

    ![][4]

4.  Скопируйте SID пакета, который находится вверху страницы.

    ![][5]

### Создание регистрации собственного приложения

1.  На [портале управления Azure][] перейдите в раздел **Active Directory** и щелкните свой каталог.

    ![][6]

2.  Щелкните вкладку **Приложения** наверху, затем нажмите кнопку **ДОБАВИТЬ**, чтобы добавить приложение.

    ![][7]

3.  Выберите команду **Добавить приложение, разрабатываемое моей организацией**.

4.  В мастере добавления приложения введите **имя** для приложения и выберите тип **Собственное клиентское приложение**. Затем щелкните "Далее".

    ![][8]

5.  В поле **URI перенаправления** вставьте SID пакета приложения, скопированный ранее, а затем нажмите кнопку завершения регистрации собственного приложения.

    ![][9]

6.  Перейдите на вкладку **Конфигурация** собственного приложения и скопируйте **ИД клиента**. Этот идентификатор потребуется позднее.

    ![][10]

7.  Прокрутите страницу вниз до раздела **разрешений для других приложений** и предоставьте полный доступ к приложению мобильной службы, зарегистрированному ранее. Затем нажмите кнопку **Сохранить**.

    ![][11]

Теперь мобильная служба настроена в AAD для принятия попыток единого входа из приложения.

## <a name="require-authentication"></a>Настройка мобильной службы для требования проверки подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][]]

## <a name="add-authentication-code"></a>Добавление кода проверки подлинности в клиентское приложение

1.  Откройте проект клиентского приложения Магазина Windows в Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget][]]

1.  В окне обозревателя решений Visual Studio откройте файл MainPage.xaml.cs и добавьте следующие директивы using.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;

2.  Добавьте следующий фрагмент кода в класс MainPage, объявляющий метод `AuthenticateAsync`.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

3.  В коде для метода `AuthenticateAsync` выше замените **INSERT-AUTHORITY-HERE** именем клиента, в котором подготавливалось приложение, используя формат <https://login.windows.net/tenant-name.onmicrosoft.com>. Это значение можно скопировать со вкладки "Домен" в Azure Active Directory на [портале управления Azure][].

4.  В коде для метода `AuthenticateAsync` выше замените **INSERT-RESOURCE-URI-HERE** на **Универсальный код ресурса (URI) идентификатора приложения** для вашей мобильной службы. Если выполнялись инструкции из раздела [Регистрация в Azure Active Directory][], то URI ИД приложения должен быть аналогичен <https://todolist.azure-mobile.net/login/aad>.

5.  В коде для метода `AuthenticateAsync` выше замените **INSERT-CLIENT-ID-HERE** идентификатором клиента, скопированным из собственного клиентского приложения.

6.  В окне обозревателя решений в Visual Studio откройте файл Package.appxmanifest в клиентском проекте. Перейдите на вкладку **Возможности** и установите флажки **Корпоративное приложение** и **Частные сети (клиент и сервер)**. Сохраните файл.

    ![][12]

7.  В файле MainPage.cs обновите обработчик событий `OnNavigatedTo` для вызова метода `AuthenticateAsync` следующим образом.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (user == null)
                await AuthenticateAsync();

            RefreshTodoItems();
        }

## <a name="test-client"></a>Тестирование клиента с использованием проверки подлинности

1.  Запустите клиентское приложение в Visual Studio.
2.  Появится приглашение выполнить вход в Azure Active Directory.
3.  Приложение выполняет проверку подлинности и возвращает элементы списка дел.

    ![][13]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [Магазин Windows C\#]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "Магазин Windows C#"
  [iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [Приступая к работе с мобильными службами]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Регистрация мобильной службы в Azure Active Directory]: #register-mobile-service-aad
  [Регистрация приложения в Azure Active Directory]: #register-app-aad
  [Настройка мобильной службы для требования проверки подлинности]: #require-authentication
  [Добавление кода проверки подлинности в клиентское приложение]: #add-authentication-code
  [Тестирование клиента с использованием проверки подлинности]: #test-client
  [Приступая к работе с данными]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  []: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
  [1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
  [панель мониторинга Центра разработки для Windows]: http://go.microsoft.com/fwlink/p/?LinkID=266734
  [2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
  [3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
  [4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
  [5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
  [портале управления Azure]: https://manage.windowsazure.com/
  [6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
  [7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
  [8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
  [9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
  [10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
  [11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-dotnet-adal-install-nuget]: ../includes/mobile-services-dotnet-adal-install-nuget.md
  [Регистрация в Azure Active Directory]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
  [13]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png
