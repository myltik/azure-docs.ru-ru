---
title: "Начало работы с проверкой подлинности для мобильных приложений в приложении Xamarin Forms | Документация Майкрософт"
description: "Узнайте, как использовать мобильные приложения для проверки подлинности пользователей приложения Xamarin Forms с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 30fac48cbacb26b03ce430987997c38c68368385
ms.lasthandoff: 03/01/2017


---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Добавление проверки подлинности в приложение Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Обзор
В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом руководстве вы добавите проверку подлинности в проект быстрого запуска Xamarin Forms, используя поставщик удостоверений, поддерживаемый службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя, и вы сможете получить доступ к ограниченным табличным данным.

## <a name="prerequisites"></a>Предварительные требования
Для продуктивной работы с этим руководством мы рекомендуем сначала изучить руководство [Создание приложения Xamarin.Forms][1]. Завершив работу, вы получите проект Xamarin Forms — кроссплатформенное приложение TodoList.

Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для аутентификации. Дополнительные сведения о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Регистрация приложения для проверки подлинности и настройка служб приложений
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

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
                    MobileServiceAuthenticationProvider.Facebook);
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

1. Добавьте следующий код в метод **OnCreate** в классе **MainActivity** перед вызовом метода `LoadApplication()`:
   
        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);
   
    Этот код гарантирует, что структура проверки подлинности инициализируется перед загрузкой приложения.
2. Повторно выполните сборку и запустите приложение. Потом выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к данным в качестве прошедшего проверку подлинности пользователя.

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
                        MobileServiceAuthenticationProvider.Facebook);
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
6. Добавьте следующую строку кода в метод **FinishedLaunching** перед вызовом `LoadApplication()`:
   
        App.Init(this);
   
    Этот код гарантирует, что структура проверки подлинности инициализируется перед тем, как приложение загрузится.
7. Повторно выполните сборку и запустите приложение. Потом выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к данным в качестве прошедшего проверку подлинности пользователя.

## <a name="add-authentication-to-windows-81-including-phone-app-projects"></a>Добавление проверки подлинности в проекты приложений Windows 8.1 (включая Phone)
В этом разделе показано, как реализовать интерфейс **IAuthenticate** в проектах приложений Windows 8.1 и Windows Phone 8.1. Те же действия применяются для проектов универсальной платформы Windows (UWP), но с помощью проекта **UWP** (с указанными изменениями). Пропустите этот раздел, если вы не пользуетесь устройствами Windows.

1. В Visual Studio щелкните правой кнопкой мыши проект **WinApp** или **WinPhone81** и выберите пункт **Назначить запускаемым проектом**.
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
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
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

    Если используется поставщик удостоверений, отличающийся от Facebook, выберите другое значение [MobileServiceAuthenticationProvider].

1. Добавьте следующую строку кода в конструктор для класса **MainPage`LoadApplication()` перед вызовом метода **:
   
        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
   
    Вместо `<your_Portable_Class_Library_namespace>` укажите пространство имен для переносимой библиотеки классов.
   
    Если вы вносите изменения в проект WinApp, перейдите к шагу 8. Следующий шаг применяется только к проекту WinPhone81, где необходимо выполнить обратный вызов входа в систему.
2. (Необязательно.) В проекте приложения **WinPhone81** откройте файл App.xaml.cs и добавьте следующие операторы `using`:
   
        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;
   
    Вместо `<your_Portable_Class_Library_namespace>` укажите пространство имен для переносимой библиотеки классов.
3. Если вы используете **WinPhone81** или **WinApp**, добавьте переопределение метода **OnActivated** в класс **App**:
   
       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);
   
           // We just need to handle activation that occurs after web authentication.
           if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
           {
               // Get the client and call the LoginComplete method to complete authentication.
               var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
               client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
           }
       }
   
   Если переопределение метода уже присутствует, добавьте условный код из приведенного выше фрагмента.  Этот код не требуется для универсальных проектов Windows.
4. Повторно выполните сборку и запустите приложение. Потом выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к данным в качестве прошедшего проверку подлинности пользователя.

## <a name="next-steps"></a>Дальнейшие действия
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

