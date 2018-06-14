---
title: Начало работы с проверкой подлинности для мобильных приложений в приложении Xamarin Forms | Документация Майкрософт
description: Узнайте, как использовать мобильные приложения для проверки подлинности пользователей приложения Xamarin Forms с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт.
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: panarasi
ms.openlocfilehash: e3e8c843437558c6d5d3a3c39bed1e647f852b18
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593404"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Добавление проверки подлинности в приложение Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Обзор
В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом руководстве вы добавите проверку подлинности в проект быстрого запуска Xamarin Forms, используя поставщик удостоверений, поддерживаемый службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя, и вы сможете получить доступ к ограниченным табличным данным.

## <a name="prerequisites"></a>предварительным требованиям
Для продуктивной работы с этим руководством мы рекомендуем сначала изучить руководство [Создание приложения Xamarin.Forms][1]. Завершив работу, вы получите проект Xamarin Forms — кроссплатформенное приложение TodoList.

Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для аутентификации. Дополнительные сведения о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Регистрация приложения для проверки подлинности и настройка служб приложений
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Добавление приложения в список разрешенных URL-адресов внешнего перенаправления

Для безопасной аутентификации требуется определить новую схему URL-адресов для своего приложения. Это позволяет системе аутентификации выполнять перенаправление обратно в приложение после завершения процесса аутентификации. В этом руководстве мы повсеместно используем схему URL-адресов _appname_. Тем не менее можно использовать любую схему URL-адресов на свой выбор. Она должна быть уникальной для мобильного приложения. Вот как можно включить перенаправление на стороне сервера.

1. На [портале Azure][8] выберите свою службу приложений.

2. Выберите пункт меню **Аутентификация или авторизация**.

3. В поле **Разрешенные URL-адреса внешнего перенаправления** введите `url_scheme_of_your_app://easyauth.callback`.  **url_scheme_of_your_app** в этой строке — это схема URL-адресов для вашего мобильного приложения.  Она должна соответствовать обычной спецификации URL-адресов для протокола (можно использовать буквы и цифры, и адрес должен начинаться с буквы).  Необходимо записать выбранную строку, так как потребуется в нескольких местах настроить код мобильного приложения с использованием схемы URL-адресов.

4. Последовательно выберите **ОК**.

5. Выберите команду **Сохранить**.

## <a name="restrict-permissions-to-authenticated-users"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Добавление проверки подлинности в переносимую библиотеку классов
В мобильных приложениях используется метод расширения [LoginAsync][3] в [MobileServiceClient][4] для входа пользователя в систему с аутентификацией в службе приложений. В этом примере используется управляемый сервером поток проверки подлинности, который отображает интерфейс входа поставщика в приложение. Дополнительные сведения см. в статье [Управляемая сервером проверка подлинности][5]. Чтобы пользователям было удобнее работать с вашим рабочим приложением, попробуйте использовать [управляемую клиентом аутентификацию][6].

Чтобы пройти проверку подлинности в проекте Xamarin Forms, следует определить интерфейс **IAuthenticate** в переносимой библиотеке классов для приложения. Затем добавьте в пользовательский интерфейс, указанный в переносимой библиотеке классов, кнопку **Вход**, которую пользователь нажимает для выполнения проверки подлинности. После успешной проверки подлинности данные загружаются из серверной части мобильного приложения.

Реализуйте интерфейс **IAuthenticate** для каждой платформы, поддерживаемой приложением.

1. В Visual Studio или Xamarin Studio откройте файл App.cs в проекте, в имени которого содержится слово **Переносимый**, а именно в проекте переносимой библиотеки классов, а потом добавьте следующий оператор `using`.

        using System.Threading.Tasks;
2. В файл Appp.cs добавьте следующее определение интерфейса `IAuthenticate` непосредственно перед определением класса `App`.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Чтобы инициализировать интерфейс с реализацией, относящейся к конкретной платформе, добавьте приведенные ниже статические элементы в класс **App**.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Откройте файл TodoList.xaml в проекте переносимой библиотеки классов и добавьте следующий элемент **Button** к элементу макета *buttonsPanel* после имеющейся кнопки:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Эта кнопка запускает управляемую сервером проверку подлинности с помощью серверной части мобильного приложения.
5. Откройте файл TodoList.xaml.cs в проекте переносимой библиотеки классов и добавьте в класс `TodoList` следующее поле:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Замените метод **OnAppearing** следующим кодом:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Это гарантирует, что данные будут обновляться из службы только после проверки подлинности пользователя.
7. Добавьте следующий обработчик событий **Clicked** в класс **TodoList**:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Сохраните изменения и создайте проект переносимой библиотеки классов, убедившись в отсутствии ошибок.

## <a name="add-authentication-to-the-android-app"></a>Добавление проверки подлинности в приложение Android
В этом разделе показано, как реализовать интерфейс **IAuthenticate** в проекте приложения Android. Пропустите этот раздел, если вы не пользуетесь устройствами Android.

1. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **droid** и выберите пункт **Назначить запускаемым проектом**.
2. Нажмите клавишу F5, чтобы запустить проект в отладчике. После запуска приложения должно появиться необработанное исключение с кодом состояния 401 ("Не санкционировано"). Код 401 создается потому, что доступ на серверной стороне предоставлен только авторизованным пользователям.
3. Откройте файл MainActivity.cs в проекте Android и добавьте следующие операторы `using` :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Обновите класс **MainActivity**, как показано ниже, чтобы реализовать интерфейс **IAuthenticate**.

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Обновите класс **MainActivity**, добавив поле **MobileServiceUser** и метод **Authenticate**, который необходим для интерфейса **IAuthenticate**, как показано ниже.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Если используется поставщик удостоверений, отличающийся от Facebook, выберите другое значение [MobileServiceAuthenticationProvider][7].

6. Обновите файл **AndroidManifest.xml**, добавив следующий код XML в элемент `<application>`:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Замените имя репозитория `{url_scheme_of_your_app}` своей схемой URL-адреса.
7. Добавьте следующий код в метод **OnCreate** в классе **MainActivity** перед вызовом метода `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Этот код гарантирует, что структура проверки подлинности инициализируется перед загрузкой приложения.
8. Повторно выполните сборку и запустите приложение. Потом выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к данным в качестве прошедшего проверку подлинности пользователя.

## <a name="add-authentication-to-the-ios-app"></a>Добавление проверки подлинности в приложение iOS
В этом разделе показано, как реализовать интерфейс **IAuthenticate** в проекте приложения iOS. Пропустите этот раздел, если вы не пользуетесь устройствами iOS.

1. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **iOS** и выберите **Назначить запускаемым проектом**.
2. Нажмите клавишу F5, чтобы запустить проект в отладчике. После запуска приложения должно появиться необработанное исключение с кодом состояния 401 ("Не санкционировано"). Ответ 401 создается потому, что доступ на серверной стороне предоставлен только авторизованным пользователям.
3. Затем в проекте iOS откройте файл AppDelegate.cs и добавьте следующие операторы `using` :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Обновите класс **AppDelegate**, как показано ниже, чтобы реализовать интерфейс **IAuthenticate**.

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Обновите класс **AppDelegate**, добавив поле **MobileServiceUser** и метод **Authenticate**, который необходим для интерфейса **IAuthenticate**, как показано ниже.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Если используется поставщик удостоверений, отличающийся от Facebook, выберите другое значение [MobileServiceAuthenticationProvider].
    
6. Обновите класс **AppDelegate**, добавив перегрузку метода **OpenUrl**, как показано ниже:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Добавьте следующую строку кода в метод **FinishedLaunching** перед вызовом `LoadApplication()`:

        App.Init(this);

    Этот код гарантирует, что структура проверки подлинности инициализируется перед тем, как приложение загрузится.

8. Откройте Info.plist и добавьте **тип URL-адреса**. Для параметра **Идентификатор** задайте имя по своему выбору, для параметра **Схемы URL-адресов** укажите схему URL-адреса приложения, а для параметра **Роль** установите значение None.

9. Повторно выполните сборку и запустите приложение. Потом выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к данным в качестве прошедшего проверку подлинности пользователя.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Добавление проверки подлинности в проекты приложений Windows 10 (включая Phone)
В этом разделе показано, как реализовать интерфейс **IAuthenticate** в проекте приложения Windows 10. Те же действия применяются для проектов универсальной платформы Windows (UWP), но с помощью проекта **UWP** (с указанными изменениями). Пропустите этот раздел, если вы не пользуетесь устройствами Windows.

1. В Visual Studio щелкните правой кнопкой мыши проект **UWP**и выберите пункт **Назначить запускаемым проектом**.
2. Нажмите клавишу F5, чтобы запустить проект в отладчике. После запуска приложения должно появиться необработанное исключение с кодом состояния 401 ("Не санкционировано"). Ответ 401 возвращается, так как доступ на серверной стороне предоставлен только авторизованным пользователям.
3. Затем откройте файл MainPage.xaml.cs проекта приложения Windows и добавьте следующие операторы `using` :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Вместо `<your_Portable_Class_Library_namespace>` укажите пространство имен для переносимой библиотеки классов.
4. Обновите класс **MainPage**, как показано ниже, чтобы реализовать интерфейс **IAuthenticate**.

        public sealed partial class MainPage : IAuthenticate
5. Обновите класс **MainPage**, добавив поле **MobileServiceUser** и метод **Authenticate**, который необходим для интерфейса **IAuthenticate**, как показано ниже.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Если используется поставщик удостоверений, отличающийся от Facebook, выберите другое значение [MobileServiceAuthenticationProvider][7].

1. Добавьте следующую строку кода в конструктор для класса **MainPage`LoadApplication()` перед вызовом метода** :

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Вместо `<your_Portable_Class_Library_namespace>` укажите пространство имен для переносимой библиотеки классов.

3. Если вы используете **UWP**, добавьте переопределение метода **OnActivated** в класс **App**:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Откройте файл Package.appxmanifest и добавьте объявление **протокола**. Для параметра **Отображаемое имя** задайте имя по своему выбору, а для параметра **Имя** укажите схему URL-адреса приложения.

4. Повторно выполните сборку и запустите приложение. Потом выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к данным в качестве прошедшего проверку подлинности пользователя.

## <a name="next-steps"></a>Дополнительная информация
Вы прошли этот учебник по обычной проверке подлинности и теперь можете перейти к одному из следующих учебников:

* [Добавление push-уведомлений в приложение](app-service-mobile-xamarin-forms-get-started-push.md)

  Узнайте, как добавить поддержку push-уведомлений в мобильное приложение и настроить в его серверной части использование концентраторов уведомлений Azure для отправки push-уведомлений.
* [Включение автономной синхронизации для приложения](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Узнайте, как добавить в приложение поддержку автономной работы с помощью серверной части мобильного приложения. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением — просматривать, добавлять или изменять данные даже при отсутствии подключения к сети.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
