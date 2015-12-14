<properties 
	pageTitle="Добавление push-уведомлений в приложение Xamarin.Forms с помощью службы приложений Azure" 
	description="Использование службы приложений Azure для отправки push-уведомлений в приложение Xamarin.Forms" 
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
	ms.date="11/25/2015" 
	ms.author="wesmc"/>

# Добавление push-уведомлений в приложение Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Обзор

В этом руководстве используется материал [руководства по быстрому запуску Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md), с которым необходимо ознакомиться в первую очередь. Вы добавите поддержку push-уведомлений для каждого проекта, который должен поддерживаться в проекте быстрого запуска Xamarin.Forms. Push-уведомления будут отправляться каждый раз при вставке записи.

Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

[Симулятор iOS не поддерживает push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), поэтому необходимо использовать физическое устройство под управлением iOS. Вам также необходимо зарегистрироваться для [участия в программе разработки решений для Apple](https://developer.apple.com/programs/ios/).

##Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас еще нет учетной записи, зарегистрируйтесь для использования пробной версии Azure и получите до 10 бесплатных мобильных приложений. Вы сможете использовать их даже после окончания срока действия пробной версии. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

* Компьютер Mac с установленным программным обеспечением [Xamarin Studio] и [Xcode] 4.4 или более поздней версии. Приложение Xamarin.Forms можно запустить в Visual Studio на компьютере Windows, но это немного сложнее, так как необходимо подключиться к компьютеру Mac с доступом к сети, на котором выполняется узел сборки Xamarin.iOS. Если вам это интересно, см. статью [Установка Xamarin.iOS в Windows].

* Физическое устройство iOS. Push-уведомления не поддерживаются в симуляторе iOS.

* Ознакомьтесь с [руководством по быстрому запуску Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md).

##Создание концентратора уведомлений для мобильного приложения

Чтобы настроить в приложении отправку уведомлений, создайте новый концентратор уведомлений и настройте его для работы со службами уведомлений платформы, которые будут использоваться.

Чтобы создать концентратор уведомлений, выполните приведенные ниже действия. Если концентратор уже создан, просто выберите его.

1. Войдите на [портал Azure](https://portal.azure.com/). Последовательно щелкните **Просмотр** > **Мобильные приложения** > ваша серверная часть > **Параметры** > **Мобильные службы** > **Push-уведомления** > **Концентратор уведомлений** > **+ Концентратор уведомлений**, а затем укажите имя и пространство имен для нового концентратора уведомлений и нажмите кнопку **ОК**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. В колонке «Создание концентратора уведомлений» нажмите кнопку **Создать**.


##Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Развертывание обновленного серверного проекта в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


##(Необязательно) Настройка и запуск проекта Android

В этом разделе описано, как запустить проект Xamarin Android для устройств под управлением Android. Пропустите этот раздел, если вы не работаете с устройствами Android.


####Включение Google Cloud Messaging (GCM)


[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


####Настройка концентратора уведомлений для GCM

1. Войдите на [портал Azure](https://portal.azure.com/). Щелкните **Обзор** > **Мобильные приложения** > ваше мобильное приложение > **Параметры** > **Push-уведомления** > **Google (GCM)**. Вставьте ранее созданный ключ API для сервера и нажмите кнопку **Сохранить**. Теперь ваша служба настроена для работы с push-уведомлениями в Android.

	![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


####Добавление push-уведомлений в проект Android

1. Щелкните правой кнопкой мыши папку «Компоненты», выберите пункт «Получить дополнительные компоненты», найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект. Этот компонент позволяет упростить работу с push-уведомлениями в проекте Xamarin Android.

2. Откройте файл проекта MainActivity.cs и добавьте приведенную ниже инструкцию using в начало файла.

		using Gcm.Client;

3. Добавьте в метод `OnCreate` следующий код после вызова `LoadApplication`.

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);
	
	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Добавьте для вспомогательного метода `CreateAndShowDialog` следующий код.

		private void CreateAndShowDialog(String message, String title) 
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. В классе `MainActivity` добавьте следующий код, чтобы предоставить текущий класс `MainActivity` для выполнения того или иного пользовательского интерфейса в основном потоке, обрабатывающем события от пользовательского интерфейса.
		
		// Create a new instance field for this activity.
		static MainActivity instance = null;
		
		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

6. Инициализируйте переменную `instance` в начале метода `MainActivity.OnCreate`.

		// Set the current instance of MainActivity.
		instance = this;

7. Добавьте новый файл класса в проект **Droid**. Присвойте новому файлу класса имя **GcmService**.

8. Обязательно добавьте в начало файла следующие операторы `using`.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Android.App;
		using Android.Content;
		using System.Collections.Generic;
		using System.Diagnostics;
		using Android.Util;
		using Newtonsoft.Json.Linq;
		using System.Text;
		using System.Linq;

9. Добавьте следующие запросы на разрешения в начало файла — после инструкций `using` и перед объявлением `namespace`.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
		
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. Добавьте следующее определение класса в пространство имен. Замените **<PROJECT_NUMBER>** номером своего проекта, который вы записали ранее.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]		
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{		
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };		
		}

11. Обновите класс `GcmService`, чтобы использовать новый широковещательный приемник.

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }
		
		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Добавьте следующий код в класс GcmService, который переопределяет обработчик событий OnRegistered и реализует метод `Register`.

	Этот код будет регистрировать текст шаблона для получения шаблонных уведомлений с помощью параметра `messageParam`. Шаблонные уведомления позволяют отправлять уведомления между разными платформами. Дополнительные сведения см. в статье [Шаблоны](https://msdn.microsoft.com/library/azure/dn530748.aspx).
		
		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;
		
		    createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");
		
            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
		
		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
		
		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyGCM}
                };
                
                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

13. Для обработки входящих push-уведомлений необходимо реализовать обработчик событий `OnMessage`. В этом коде мы будем обрабатывать уведомления и отправлять их в диспетчер уведомлений.

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");
		
		    var msg = new StringBuilder();
		
		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }
		
		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();
		
		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }
		
		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }
		
		    createNotification("Unknown message details", msg.ToString());
		}
		
		void createNotification(string title, string desc)
		{
		    //Create notification
		    var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
		
		    //Create an intent to show ui
		    var uiIntent = new Intent(this, typeof(MainActivity));
		
		    //Create the notification
		    var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
		
		    //Auto cancel will remove the notification once the user touches it
		    notification.Flags = NotificationFlags.AutoCancel;
		
		    //Set the notification info
		    //we use the pending intent, passing our ui intent over which will get called
		    //when the notification is tapped.
		    notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
		
		    //Show the notification
		    notificationManager.Notify(1, notification);
		}
	
14. Необходимо также реализовать обработчики `OnUnRegistered` и `OnError` для приемника.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}



####Тестирование push-уведомлений в приложении Android

1. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **droid** и выберите пункт **Назначить запускаемым проектом**.
 
2. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.
	
	> [AZURE.NOTE]Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите задачу, а затем щелкните значок «плюс» (**+**).

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.





##(Необязательно) Настройка и запуск проекта iOS

В этом разделе описано, как запустить проект Xamarin iOS для устройств под управлением iOS. Пропустите этот раздел, если вы не работаете с устройствами iOS.

[AZURE.INCLUDE [Включение push-уведомлений Apple через концентраторы уведомлений Xamarin](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Настройка концентратора уведомлений для APNs

1. Войдите на [портал Azure](https://portal.azure.com/). Щелкните **Обзор** > **Мобильные приложения** > ваше мобильное приложение > **Параметры** > **Push-уведомления** > **Apple (APNS)** > **Отправка сертификата**. Отправьте экспортированный ранее P12-файл сертификата push-уведомлений. Если вы создали сертификат push-уведомлений для разработки и тестирования, не забудьте выбрать параметр **Песочница**. В противном случае выберите параметр **Рабочая среда**. Теперь ваша служба настроена для работы с push-уведомлениями в iOS.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	Далее следует настроить параметры проекта iOS в Xamarin Studio или Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Добавление push-уведомлений в приложение iOS

1. Добавьте следующий оператор `using` в начало файла **AppDelegate.cs**.

        using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;


2. В проекте iOS откройте файл AppDelegate.cs и измените метод `FinishedLaunching`, чтобы добавить поддержку удаленных уведомлений, как показано ниже.

		public override bool FinishedLaunching (UIApplication app, NSDictionary options)
		{
			global::Xamarin.Forms.Forms.Init ();

			Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();

            // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
            // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
            //SQLitePCL.CurrentPlatform.Init();
            
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

			LoadApplication (new App ());

			return base.FinishedLaunching (app, options);
		}


4. В файле AppDelegate.cs также добавьте переопределение для события **RegisteredForRemoteNotifications**, чтобы выполнить регистрацию для получения уведомлений.

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. В файле AppDelegate.cs также добавьте переопределение для события **DidReceivedRemoteNotification**, чтобы входящие уведомления обрабатывались во время выполнения приложения.

        public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Ваше приложение теперь обновлено для поддержки push-уведомлений.

####Тестирование push-уведомлений в приложении iOS

1. Щелкните проект iOS правой кнопкой мыши и выберите пункт **Назначить запускаемым проектом**.

2. Нажмите кнопку **Выполнить** или клавишу **F5** в Visual Studio, чтобы выполнить сборку проекта и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.
	
	> [AZURE.NOTE]Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

3. В приложении введите задачу, а затем щелкните значок «плюс» (**+**).

4. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.




##(Необязательно) Настройка и запуск проекта Windows

В этом разделе описано, как запустить проект Xamarin WinApp для устройств под управлением Windows. Пропустите этот раздел, если вы не работаете с устройствами Windows.


####Регистрация приложения Windows для получения push-уведомлений с помощью WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Настройка концентратора уведомлений для WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Добавление push-уведомлений в приложение Windows

1. В Visual Studio откройте файл **App.xaml.cs** в проекте **WinApp**. Добавьте следующие операторы `using`.

		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using WesmcMobileAppGaTest;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. В файл App.xaml.cs добавьте приведенный ниже метод `InitNotificationsAsync`. Этот метод получает канал push-уведомлений и регистрирует шаблон для получения шаблонных уведомлений из концентратора уведомлений. Этому клиенту будет доставлено шаблонное уведомление, поддерживающее параметр `messageParam`.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyWNS},
                  {"headers", headers} // Only needed for WNS & MPNS
                };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
        }

3. В файле App.xaml.cs обновите обработчик событий `OnLaunched` с помощью атрибута `async` и вызовите метод `InitNotificationsAsync`.

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            Frame rootFrame = Window.Current.Content as Frame;

            // Do not repeat app initialization when the Window already has content,
            // just ensure that the window is active
            if (rootFrame == null)
            {
                // Create a Frame to act as the navigation context and navigate to the first page
                rootFrame = new Frame();
                // Set the default language
                rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
                rootFrame.NavigationFailed += OnNavigationFailed;
                Xamarin.Forms.Forms.Init(e); 
                if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
                {
                    //TODO: Load state from previously suspended application
                }
                // Place the frame in the current Window
                Window.Current.Content = rootFrame;
            }

            if (rootFrame.Content == null)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(typeof(MainPage), e.Arguments);
            }
            // Ensure the current window is active
            Window.Current.Activate();

            await InitNotificationsAsync();
        }

4. В обозревателе решений для Visual Studio откройте файл **Package.appxmanifest** и в разделе **Notifications** задайте для параметра **Toast Capable** значение **Yes**.

5. Выполните сборку приложения и убедитесь в отсутствии ошибок. Теперь клиентское приложение должно зарегистрироваться для получения шаблонных уведомлений из серверной части мобильного приложения.


####Тестирование push-уведомлений в приложении Windows

1. В Visual Studio щелкните правой кнопкой мыши проект **WinApp** и выберите пункт **Назначить запускаемым проектом**.


2. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.
	
	> [AZURE.NOTE]Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

3. В приложении введите задачу, а затем щелкните значок «плюс» (**+**).

4. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.



<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Установка Xamarin.iOS в Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_1203_2015-->