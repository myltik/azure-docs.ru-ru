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
	ms.date="05/05/2016"
	ms.author="wesmc"/>

# Добавление проверки подлинности в приложение Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##Обзор

В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом учебнике вы добавите проверку подлинности в проект краткого руководства Xamarin.Forms, используя поставщик удостоверений, поддерживаемый службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя, и вы сможете получить доступ к ограниченным табличным данным.

Сначала необходимо изучить учебник [Создание приложения Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для аутентификации. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Регистрация приложения для проверки подлинности и настройка служб приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Добавление проверки подлинности в переносимую библиотеку классов

Для отображения интерфейса входа и данных кэша мобильные приложения используют метод `MobileServiceClient.LoginAsync`, относящийся к конкретной платформе. Чтобы пройти проверку подлинности в проекте Xamarin Forms, следует определить интерфейс `IAuthenticate` в переносимой библиотеке классов. Каждая поддерживаемая платформа будет реализовывать этот интерфейс в проекте для конкретной платформы.

Также понадобится обновить пользовательский интерфейс, определенный в переносимой библиотеке классов, добавив кнопку входа. Пользователь должен нажать эту кнопку для проверки подлинности после запуска приложения.

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


4. Откройте файл TodoList.xaml.cs в **переносимом** проекте. Добавьте следующий флаг к классу `TodoList`, чтобы показать, прошел ли пользователь проверку подлинности.

        bool authenticated = false;


5. В файле TodoList.xaml.cs обновите метод `OnAppearing`, чтобы после прохождения пользователем проверки подлинности только обновлять элементы.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

6. В файле TodoList.xaml.cs в верхней части конструктора для класса `TodoList` определите следующую кнопку входа и щелкните обработчик.

        public TodoList()
        {
            InitializeComponent();

            manager = TodoItemManager.DefaultManager;

            var loginButton = new Button
            {
                Text = "Login",
                TextColor = Xamarin.Forms.Color.Black,
                BackgroundColor = Xamarin.Forms.Color.Lime,
            };
            loginButton.Clicked += loginButton_Clicked;

            Xamarin.Forms.StackLayout bp = buttonsPanel as StackLayout;
            Xamarin.Forms.StackLayout bpParentStack = bp.Parent.Parent as StackLayout;

            bpParentStack.Padding = new Xamarin.Forms.Thickness(10, 30, 10, 20);
            bp.Orientation = StackOrientation.Vertical;
            bp.Children.Add(loginButton);

			...

7. В файле TodoList.xaml.cs добавьте следующий обработчик для события нажатия кнопки входа.

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }


8. Сохраните изменения и создайте проект переносимой библиотеки классов, убедившись в отсутствии ошибок.


##Добавление проверки подлинности в приложение Android

В этом разделе вы добавите проверку подлинности для проекта droid. Пропустите этот раздел, если вы не работаете с устройствами Android.

1. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **droid** и выберите пункт **Назначить запускаемым проектом**.

2. Запустите приложение в отладчике, чтобы убедиться в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (Неавторизованный доступ). Это происходит потому, что доступ на серверной стороне предоставлен только авторизованным пользователям.

3. Затем в проекте droid откройте файл MainActivity.cs и добавьте следующий оператор `using`.

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

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage(message);
            builder.SetTitle(title);
            builder.Create().Show();
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
                    if (user != null)
                    {
                        UIAlertView avAlert = new UIAlertView("Authentication", "You are now logged in " + user.UserId, null, "OK", null);
                        avAlert.Show();
                    }
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

        App.Init(this);

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
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
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

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }



7. Перестройте приложение и запустите его. Выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к таблице в качестве прошедшего проверку пользователя.


##Добавление проверки подлинности в приложение Windows Phone 8.1

В этом разделе вы добавите проверку подлинности для проекта WinPhone81. Пропустите этот раздел, если вы не работаете с устройствами Windows Phone 8.1.

1. В Visual Studio щелкните правой кнопкой мыши проект **WinPhone81** и выберите пункт **Назначить запускаемым проектом**.

2. Запустите приложение в отладчике, чтобы убедиться в том, что после его запуска возникает необработанное исключение с кодом состояния 401 (Неавторизованный доступ). Это происходит потому, что доступ на серверной стороне предоставлен только авторизованным пользователям.


3. Затем в проекте WinPhone81 откройте файл MainPage.xaml.cs и добавьте следующий оператор `using`. Вместо <*Your portable class library namespace*> укажите пространство имен для переносимой библиотеки классов.

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
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
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

            this.NavigationCacheMode = NavigationCacheMode.Required;

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }

7. В Windows Phone необходимо выполнить процедуру входа. Откройте файл App.xaml.cs и добавьте следующий оператор `using` и код в обработчик `OnActivated` в классе `App`.

	```
		using Microsoft.WindowsAzure.MobileServices;
	```

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);

		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}



8. Перестройте приложение и запустите его. Выполните вход, используя выбранный поставщик проверки подлинности, и убедитесь, что у вас есть доступ к таблице в качестве прошедшего проверку пользователя.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0511_2016-->