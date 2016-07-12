<properties
	pageTitle="Добавление push-уведомлений в приложение Xamarin.Forms | Microsoft Azure"
	description="Использование служб Azure для отправки кроссплатформенных push-уведомлений в приложения Xamarin.Forms."
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
	ms.date="06/20/2016"
	ms.author="wesmc"/>

# Добавление push-уведомлений в приложение Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Обзор

В этом руководстве показано, как использовать службы Azure для отправки push-уведомлений в приложения Xamarin.Forms, которые выполняются на разных платформах (Android, iOS и Windows). Push-уведомления отправляются с сервера мобильных приложений Azure с использованием центров уведомлений Azure. При этом используется регистрация шаблонов, что позволяет отправлять одни и те же сообщения на устройства на всех платформах через разные службы push-уведомлений (PNS). Дополнительные сведения об отправке межплатформенных push-уведомлений см. в документации по [центрам уведомлений Azure](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

Push-уведомления добавляются во все проекты, которые поддерживает приложение Xamarin.Forms. Push-уведомления отправляются каждый раз, когда на сервере добавляется новая запись.

##Предварительные требования

Для продуктивной работы с этим руководством мы рекомендуем сначала изучить руководство [Создание приложения Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Завершив работу, вы получите проект Xamarin.Forms — кроссплатформенное приложение TodoList.

Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Для отправки push-уведомления на устройства iOS необходимо [участие в программе разработчиков Apple](https://developer.apple.com/programs/ios/). [Симулятор iOS не поддерживает push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), поэтому нужно использовать физическое устройство под управлением iOS.

##<a name="create-hub"></a>Создание центра уведомлений

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Необязательно) Настройка и запуск проекта Android

Выполнив инструкции из этого раздела, вы добавите push-уведомления для проекта Xamarin.Forms Droid для платформы Android.


###Включение Google Cloud Messaging (GCM)

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###Настройка серверной части мобильного приложения для отправки push-запросов с использованием GCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###Добавление push-уведомлений в проект Android

Теперь, когда сервер настроен для использования Google Cloud Messaging (GCM), мы можем добавить в клиент компоненты и код, которые позволят приложению регистрироваться в GCM, регистрироваться в узлах уведомлений Azure для получения push-уведомлений через сервер мобильного приложения, а также получать уведомления.

1. Щелкните правой кнопкой мыши папку **Компоненты** в проекте **Droid**, выберите **Получить дополнительные компоненты...**, найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект. Этот компонент поддерживает push-уведомления для проекта Xamarin Android.


2. Откройте файл проекта MainActivity.cs и добавьте приведенную ниже инструкцию using в начало файла.

		using Gcm.Client;

3. Добавьте в метод **OnCreate** после строки вызова **LoadApplication** следующий код:

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
	        CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Добавьте новый вспомогательный метод **CreateAndShowDialog**, как показано ниже:

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Добавьте следующий код в класс **MainActivity**:

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

	Этот класс представляет текущий экземпляр **MainActivity** и позволяет выполнить действия в основном потоке пользовательского интерфейса.

6. Инициализируйте переменную `instance` в начале метода **OnCreate**, как показано ниже.

		// Set the current instance of MainActivity.
		instance = this;

2. Добавьте в проект **Droid** новый файл класса с именем `GcmService.cs`. В начале файла этого класса обязательно должны присутствовать следующие операторы **using**:

		using Android.App;
		using Android.Content;
		using Android.Media;
		using Android.Support.V4.App;
		using Android.Util;
		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Text;


9. Добавьте в начало файла (после инструкций **using**, но до объявления **namespace**) следующие запросы на разрешение.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Добавьте следующее определение класса в пространство имен.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

	>[AZURE.NOTE]Замените **<PROJECT\_NUMBER>** номером своего проекта, который вы записали ранее.

11. Замените пустой класс **GcmService** следующим кодом, который использует новый получатель широковещательных сообщений:

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Добавьте в класс **GcmService** следующий код, который переопределяет обработчик событий **OnRegistered** и реализует метод **Register**.

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

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

		Note that this code uses the `messageParam` parameter in the template registration. 

13. Добавьте следующий код, который реализует метод **OnMessage**:

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

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

	Он обрабатывает входящие уведомления и передает их для отображения в диспетчер уведомлений.

14. Также для **GcmServiceBase** потребуется реализовать методы обработчика **OnUnRegistered** и **OnError**, например так:

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}

Теперь все готово, и вы можете проверить отправку push-уведомлений с помощью приложения, работающего на устройстве Android или в эмуляторе.

###Тестирование push-уведомлений в приложении Android

Первые два шага нужны, только если тестирование выполняется в эмуляторе.

1. Убедитесь, что для развертывания или отладки используется виртуальное устройство, на котором в качестве назначения заданы интерфейсы Google API, как показано ниже в диспетчере виртуальных устройств Android (AVD).

2. Добавьте учетную запись Google на устройство Android, щелкнув **Приложения** > **Параметры** > **Добавить учетную запись**. Следуйте указаниям, чтобы использовать на устройстве существующую учетную запись Google или создать новую.

1. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **Droid** и выберите пункт **Назначить запускаемым проектом**.

2. Нажмите кнопку **Запустить**, чтобы создать проект и запустить приложение на устройстве Android или в эмуляторе.

3. В приложении введите задачу, а затем щелкните значок плюса (**+**).

4. После добавления элемента должно отобразиться соответствующее уведомление.


##(Необязательно) Настройка и запуск проекта iOS

В этом разделе описано, как запустить проект Xamarin iOS для устройств под управлением iOS. Пропустите этот раздел, если вы не работаете с устройствами iOS.

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Настройка концентратора уведомлений для APNs

1. Войдите на [портал Azure](https://portal.azure.com/). Щелкните **Обзор** > **Мобильные приложения** > ваше мобильное приложение > **Параметры** > **Push-уведомления** > **Apple (APNS)** > **Отправка сертификата**. Отправьте экспортированный ранее P12-файл сертификата push-уведомлений. Если вы создали сертификат push-уведомлений для разработки и тестирования, не забудьте выбрать параметр **Песочница**. В противном случае выберите параметр **Рабочая среда**. Теперь ваша служба настроена для работы с push-уведомлениями в iOS.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	Далее следует настроить параметры проекта iOS в Xamarin Studio или Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Добавление push-уведомлений в приложение iOS

1. Добавьте следующий оператор `using` в верхнюю часть файла **AppDelegate.cs**.

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


4. Кроме того, в файле AppDelegate.cs добавьте переопределение для события **RegisteredForRemoteNotifications**, чтобы выполнить регистрацию для получения уведомлений.

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

5. Кроме того, в файле AppDelegate.cs добавьте переопределение для события **DidReceivedRemoteNotification**, чтобы входящие уведомления обрабатывались во время выполнения приложения.

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

2. Нажмите кнопку **Запустить** или клавишу **F5** в Visual Studio, чтобы выполнить сборку проекта и запустить приложение на устройстве iOS. Затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

	> [AZURE.NOTE] Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

3. В приложении введите задачу, а затем щелкните значок плюса (**+**).

4. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.


##(Необязательно) Настройка и запуск проектов для Windows

В этом разделе описано, как запускать проекты Xamarin.Forms WinApp и WinPhone81 на устройствах под управлением Windows. Эти действия применимы для любых проектов универсальной платформы Windows (UWP). Пропустите этот раздел, если вы не работаете с устройствами Windows.


####Регистрация приложения Windows для получения push-уведомлений с помощью WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Настройка концентратора уведомлений для WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Добавление push-уведомлений в приложение Windows

1. В Visual Studio откройте файл проекта **App.xaml.cs** и добавьте следующие директивы **using**.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using <your_TodoItemManager_portable_class_namespace>;

	Вместо `<your_TodoItemManager_portable_class_namespace>` укажите пространство имен вашего переносимого проекта, который содержит класс `TodoItemManager`.
 

2. В файле App.xaml.cs добавьте следующий метод **InitNotificationsAsync**:

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
				"<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
			{
				{"body", templateBodyWNS},
				{"headers", headers} // Needed for WNS.
			};

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
				.RegisterAsync(channel.Uri, templates);
        }

	Этот метод получает канал push-уведомлений и регистрирует шаблон для получения шаблонных уведомлений от узла уведомлений. Клиенту будет доставлено шаблонное уведомление, поддерживающее параметр *messageParam*.

3. В файле App.xaml.cs измените метод обработки события **OnLaunched**, добавив модификатор `async`, а затем добавьте в конец метода следующую строку кода:

        await InitNotificationsAsync();

	Теперь push-уведомления будут создаваться или обновляться при каждом запуске приложения. Это нужно, чтобы push-канал WNS всегда оставался активным.

4. В обозревателе решений для Visual Studio откройте файл **Package.appxmanifest** и в разделе **Notifications** задайте для параметра **Toast Capable** значение **Yes**.

5. Выполните сборку приложения и убедитесь в отсутствии ошибок. Теперь клиентское приложение должно зарегистрироваться для получения шаблонных уведомлений из серверной части мобильного приложения. Повторите действия из этого раздела для каждого проекта Windows, входящего в ваше решение.


####Тестирование push-уведомлений в приложении Windows

1. В Visual Studio щелкните правой кнопкой мыши проект Windows и выберите пункт **Назначить запускаемым проектом**.

2. Нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение.

3. В приложении введите имя нового объекта todoitem и добавьте его, нажав значок плюса (**+**).

4. После добавления элемента должно отобразиться соответствующее уведомление.

##Дальнейшие действия

Дополнительные сведения о push-уведомлениях:

* [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags). Теги позволяют адресовать push-уведомления определенным сегментам клиентов. Узнайте, как указать теги для определенного устройства.

* [Центры уведомлений Azure — рекомендации по диагностике](../notification-hubs/notification-hubs-push-notification-fixer.md). Существуют различные причины, по которым уведомления могут теряться или не доходить до устройств. В этой статье рассказывается, как проанализировать и определить основную причину сбоев для push-уведомлений.

Мы также рекомендуем изучить одно из следующих руководств:

* [Добавление проверки подлинности в приложение](app-service-mobile-xamarin-forms-get-started-users.md). Узнайте, как аутентифицировать пользователей приложения с помощью поставщика удостоверений.

* [Включение автономной синхронизации для приложения](app-service-mobile-xamarin-forms-get-started-offline-data.md). Узнайте, как добавить поддержку автономной работы приложения с помощью серверной части мобильного приложения. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением (просматривать, добавлять или изменять данные) даже при отсутствии подключения к сети.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0629_2016-->