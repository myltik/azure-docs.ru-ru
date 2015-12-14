<properties 
	pageTitle="Начало работы с проверкой подлинности для мобильных приложений в приложении Xamarin.Forms" 
	description="Узнайте, как использовать мобильные приложения для проверки подлинности пользователей приложения Xamarin Forms с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="11/30/2015" 
	ms.author="wesmc"/>

# Добавление проверки подлинности в приложение Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Обзор

В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом учебнике вы добавите проверку подлинности в проект краткого руководства Xamarin.Forms, используя поставщик удостоверений, поддерживаемый службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя, и вы сможете получить доступ к ограниченным табличным данным.

Сначала необходимо ознакомиться с [руководством по быстрому запуску Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для аутентификации. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).


##Регистрация приложения для проверки подлинности и настройка служб приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Добавление проверки подлинности в переносимую библиотеку классов 

Для отображения интерфейса входа и данных кэша мобильные приложения используют метод `MobileServiceClient.LoginAsync`, относящийся к конкретной платформе. Чтобы пройти проверку подлинности в проекте Xamarin Forms, следует определить интерфейс `IAuthenticate` в переносимой библиотеке классов. Каждая поддерживаемая платформа может реализовать этот интерфейс в проекте для конкретной платформы. Прежде чем выполнять вызовы ограниченной таблицы из переносимой библиотеки классов, нужно добавить код для проверки подлинности.

1. В Visual Studio или Xamarin Studio откройте файл App.cs в **переносимом** проекте. Добавьте в файл следующий оператор `using`.

		using System.Threading.Tasks;

2. В файл Appp.cs добавьте следующее определение интерфейса `IAuthenticate` непосредственно перед определением класса `App`.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. В файл App.cs добавьте следующие статические элементы для инициализации интерфейса с реализацией, относящейся к конкретной платформе.

		public class App : Application
		{
	
	        public static IAuthenticate Authenticator { get; private set; }
	
	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }
	
			...

4. Откройте файл TodoList.xaml.cs в **переносимом** проекте и обновите метод `OnAppearing` для выполнения проверки подлинности до обновления элементов таблицы.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            if (App.Authenticator != null)
                await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            await RefreshItems(true, syncItems: false);
        }


5. Сохраните изменения и создайте проект портала, убедившись в отсутствии ошибок.


##Добавление проверки подлинности в приложение Android

В этом разделе вы добавите проверку подлинности для проекта droid. Пропустите этот раздел, если вы не работаете с устройствами Android.

1. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **droid** и выберите пункт **Назначить запускаемым проектом**.

2. Запустите приложение в отладчике, чтобы убедиться в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (Неавторизованный доступ). Это происходит потому, что доступ на серверной стороне предоставлен только авторизованным пользователям.

3. Затем в проекте droid откройте файл и добавьте следующий оператор `using`.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Обновите класс `MainActivity` для реализации интерфейса `IAuthenticate`.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Обновите класс `MainActivity`, добавив показанные ниже поле `MobileServiceUser` и метод `Authenticate`, для поддержки интерфейса `IAuthenticate`.
 
	Если вместо Facebook вы хотите использовать другого поставщика `MobileServiceAuthenticationProvider`, внесите соответствующее изменение.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

6. Обновите метод `OnCreate` класса `MainActivity` для инициализации средства проверки подлинности перед загрузкой приложения.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. Перестройте приложение и запустите его. Выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к таблице в качестве прошедшего проверку пользователя.



##Добавление проверки подлинности в приложение iOS

В этом разделе вы добавите проверку подлинности для проекта iOS. Пропустите этот раздел, если вы не работаете с устройствами iOS.

1. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **iOS** и выберите пункт **Назначить запускаемым проектом**.

2. Запустите приложение в отладчике, чтобы убедиться в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (Неавторизованный доступ). Это происходит потому, что доступ на серверной стороне предоставлен только авторизованным пользователям.

3. Затем в проекте iOS откройте файл AppDelegate.cs и добавьте следующий оператор `using`.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Обновите класс `AppDelegate` для реализации интерфейса `IAuthenticate`.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. Обновите класс `AppDelegate`, добавив показанные ниже поле `MobileServiceUser` и метод `Authenticate`, для поддержки интерфейса `IAuthenticate`.
 
	Если вместо Facebook вы хотите использовать другого поставщика `MobileServiceAuthenticationProvider`, внесите соответствующее изменение.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. Обновите метод `FinishedLaunching` класса `AppDelegate` для инициализации средства проверки подлинности перед загрузкой приложения.

        App.Init((IAuthenticate)this);

		LoadApplication (new App ());



7. Перестройте приложение и запустите его. Выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к таблице в качестве прошедшего проверку пользователя.




##Добавление проверки подлинности в приложение Windows

В этом разделе вы добавите проверку подлинности для проекта WinApp. Пропустите этот раздел, если вы не работаете с устройствами Windows.

1. В Visual Studio щелкните правой кнопкой мыши проект **WinApp** и выберите пункт **Назначить запускаемым проектом**.

2. Запустите приложение в отладчике, чтобы убедиться в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (Неавторизованный доступ). Это происходит потому, что доступ на серверной стороне предоставлен только авторизованным пользователям.

3. Затем в проекте WinApp откройте файл MainPage.xaml.cs и добавьте следующий оператор `using`. Вместо <*Your portable class library namespace*> укажите пространство имен для переносимой библиотеки классов.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Обновите класс `MainPage` для реализации интерфейса `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Обновите класс `MainPage`, добавив показанные ниже поле `MobileServiceUser` и метод `Authenticate`, для поддержки интерфейса `IAuthenticate`.
 
	Если вместо Facebook вы хотите использовать другого поставщика `MobileServiceAuthenticationProvider`, внесите соответствующее изменение.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    var messageDialog = new Windows.UI.Popups.MessageDialog(
							string.Format("you are now logged in - {0}", user.UserId), "Authentication");
                    messageDialog.ShowAsync();
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. Обновите конструктор для класса `MainPage` для инициализации средства проверки подлинности перед загрузкой приложения. Вместо <*Your portable class library namespace*> укажите пространство имен для переносимой библиотеки классов.

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init((IAuthenticate)this);
            
            LoadApplication(new WesmcMobileAppGaTest.App());
        }



7. Перестройте приложение и запустите его. Выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к таблице в качестве прошедшего проверку пользователя.




<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1203_2015-->