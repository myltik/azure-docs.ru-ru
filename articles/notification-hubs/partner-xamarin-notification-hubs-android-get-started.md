<properties
	pageTitle="Приступая к работе с Центрами уведомлений для приложений Xamarin.Android | Microsoft Azure"
	description="Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android."
	authors="ysxu"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter="xamarin"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="06/09/2015"
	ms.author="yuaxu;wesmc"/>

# Приступая к работе с центрами уведомлений

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Обзор

В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android. Вы создаете пустое приложение Xamarin.Android, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

В этом учебнике описывается простой сценарий вещания с использованием Центров уведомлений.

##Предварительные требования

Для работы с данным учебником требуется следующее:

+ [Xamarin.Android]
+ Активная учетная запись Google
+ [Компонент мобильных служб Azure]
+ [Компонент обмена сообщениями в Azure]
+ [Компонент клиента Google Cloud Messaging]

Для прохождения других учебников, посвященных Центрам уведомлений для приложений Xamarin.Android, необходимо сначала выполнить действия, описанные в этом учебнике.

> [AZURE.IMPORTANT]Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="register"></a>Включение Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-hub"></a>Настройка центра уведомлений

[AZURE.INCLUDE [notification-hubs-android-configure-push](../../includes/notification-hubs-android-configure-push.md)]

##<a name="connecting-app"></a>Подключение приложения к центру уведомлений

### Создание нового проекта

1. В Xamarin Studio (или Visual Studio) щелкните **Файл** и **Создать**, выберите **Приложение Android** в окне **Новое решение** и нажмите кнопку **ОК**.

   	![][14]

	Будет создан проект Android.

2. Откройте свойства проекта, щелкнув правой кнопкой мыши в представлении решений новый проект и выбрав пункт **Параметры**. Выберите элемент **Приложение Android** в разделе **Сборка**.

   	![][15]

3. Для параметра **Минимальная версия Android** выберите уровень API 8.

4. Для параметра **Целевая версия Android** установите версию API, которую нужно использовать в качестве целевой (используйте API уровня 8 или более высокого).

5. Убедитесь, что **Имя пакета** начинается со строчной буквы.

	> [AZURE.IMPORTANT]Первая буква имени пакета должна быть строчной. В противном случае возникнут ошибки манифеста приложения при регистрации **BroadcastReceiver** и **IntentFilter** для push-уведомлений ниже.

### Добавление в проект необходимых компонентов

Клиент Google Cloud Messaging, доступный в магазине компонентов Xamarin, упрощает процесс поддержки push-уведомлений в Xamarin.Android.

1. Щелкните правой кнопкой мыши папку "Компоненты" в приложении Xamarin.Android и выберите пункт **Получить дополнительные компоненты**

2. Найдите компонент **Обмен сообщениями Azure** и добавьте его в проект.

3. Найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект.


### Настройка центров уведомлений в проекте

1. Подготовьте следующую информацию для вашего приложения Android и концентратора уведомлений.

	- **GoogleProjectNumber**: найдите это значение номера проекта на странице своего приложения на портале разработчиков Google. Вы записали это значение ранее, когда создавали приложение на портале.
	- **Прослушивание строки подключения**: на панели мониторинга на портале Azure щелкните **Просмотреть строки подключения**. Для этого значения скопируйте строку подключения *DefaultListenSharedAccessSignature*.
	- **Имя концентратора** — это имя вашего концентратора с портала Azure. Например, *mynotificationhub2*.

	Создайте класс **Constants.cs** для вашего проекта Xamarin и определите в классе следующие постоянные значения. Замените значения заполнителей на собственные значения.

		public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
		public const string ListenConnectionString = "<Listen connection string>";
		public const string NotificationHubName = "<hub name>";

2. Добавьте следующие элементы, используя операторы **MainActivity.cs**:

		using Android.Util;
		using Gcm.Client;

3. Добавьте переменную экземпляра в класс `MainActivity`, который будет использоваться для отображения диалогового окна оповещения при выполнении приложения.

		public static MainActivity instance;


3. Создайте следующий метод в классе **MainActivity**:

		private void RegisterWithGCM()
		{
			// Check to ensure everything's set up right
			GcmClient.CheckDevice(this);
			GcmClient.CheckManifest(this);

			// Register for push notifications
			Log.Info("MainActivity", "Registering...");
			GcmClient.Register(this, Constants.SenderID);
		}

4. Добавьте вызов к `RegisterWithGCM` в `OnCreate` методе **MainActivity.cs**

		protected override void OnCreate (Bundle bundle)
		{
			instance = this;

			base.OnCreate (bundle);

			// Set your view from the "main" layout resource
			SetContentView (Resource.Layout.Main);

			// Get your button from the layout resource,
			// and attach an event to it
			Button button = FindViewById<Button> (Resource.Id.myButton);

			RegisterWithGCM();
		}


4. Создайте новый класс **MyBroadcastReceiver**.

	> [AZURE.NOTE]Здесь мы рассмотрим создание класса **BroadcastReceiver** с нуля. Быстрой альтернативой созданию сценария **MyBroadcastReceiver.cs** вручную является использование ссылки на файл **GcmService.cs**, находящийся в образце проекта Xamarin.Android на [NotificationHubs][GitHub]. Вы также можете скопировать файл **GcmService.cs** и переименовать классы.

5. Добавьте следующие элементы, используя операторы **MyBroadcastReceiver.cs** (ссылаясь на компонент и сборку, которые были добавлены ранее):

		using System.Collections.Generic;
		using System.Text;
		using Android.App;
		using Android.Content;
		using Android.Util;
		using Gcm.Client;
		using WindowsAzure.Messaging;

5. Добавьте следующие разрешения запросов между операторами **using** и объявлением **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. В операторе **MyBroadcastReceiver.cs** измените класс **MyBroadcastReceiver** таким образом, чтобы он соответствовал следующему коду:

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
			Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Добавьте в файл **MyBroadcastReceiver.cs** еще один класс под названием **PushHandlerService**, сделав его производным от **GcmServiceBase**. Примените для класса атрибут **Service**:

    	[Service] // Must use the service tag
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID)
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
        	}
    	}


8. **GcmServiceBase** реализует методы **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** и **OnError()**. Наш класс реализации **PushHandlerService** должен переопределить эти методы, и они будут срабатывать в ответ на взаимодействие с концентратором уведомления.


9. Переопределите метод **OnRegistered()** в **PushHandlerService** с помощью следующего кода:

		protected override void OnRegistered(Context context, string registrationId)
		{
			Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
			RegistrationID = registrationId;

			createNotification("PushHandlerService-GCM Registered...",
								"The device has been Registered!");

			Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
										context);
			try
			{
				Hub.UnregisterAll(registrationId);
			}
			catch (Exception ex)
			{
				Log.Error(MyBroadcastReceiver.TAG, ex.Message);
			}

			//var tags = new List<string>() { "falcons" }; // create tags if you want
			var tags = new List<string>() {};

			try
			{
				var hubRegistration = Hub.Register(registrationId, tags.ToArray());
			}
			catch (Exception ex)
			{
				Log.Error(MyBroadcastReceiver.TAG, ex.Message);
			}
		}

	> [AZURE.NOTE]В коде **OnRegistered()** выше обратите внимание на возможность задать теги для регистрации на конкретные каналы обмена сообщениями.

10. Переопределите метод **OnMessage** в **PushHandlerService** с помощью следующего кода:

		protected override void OnMessage(Context context, Intent intent)
		{
			Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

			var msg = new StringBuilder();

			if (intent != null && intent.Extras != null)
			{
				foreach (var key in intent.Extras.KeySet())
					msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
			}

			string messageText = intent.Extras.GetString("message");
			if (!string.IsNullOrEmpty (messageText))
			{
				createNotification ("New hub message!", messageText);
			}
			else
			{
				createNotification ("Unknown message details", msg.ToString ());
			}
		}

11. Добавьте методы **createNotification** и **dialogNotify** к службе **PushHandlerService** для уведомления пользователей при получении уведомления.

	>[AZURE.NOTE]Структура уведомлений в версии платформы Android 5.0 и более поздних версиях существенно отличается от структуры в предыдущих версиях. Если вы проверяете это на платформе Android версии 5.0 или более поздней версии, необходимо запустить приложение, чтобы получить уведомление. Дополнительную информацию см. в разделе [Уведомления Android](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
			dialogNotify (title, desc);
        }

		protected void dialogNotify(String title, String message)
		{

			MainActivity.instance.RunOnUiThread(() => {
				AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
				AlertDialog alert = dlg.Create();
				alert.SetTitle(title);
				alert.SetButton("Ok", delegate {
					alert.Dismiss();
				});
				alert.SetMessage(message);
				alert.Show();
			});
		}


12. Переопределите абстрактные члены **OnUnRegistered()**, **OnRecoverableError()** и **OnError()** для компиляции кода.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

			createNotification("GCM Unregistered...", "The device has been unregistered!");
		}

		protected override bool OnRecoverableError(Context context, string errorId)
		{
			Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

			return base.OnRecoverableError (context, errorId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
		}


##<a name="run-app"></a>Запустите свое приложение в эмуляторе

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

> [AZURE.IMPORTANT]Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите пункт **Параметры** и нажмите кнопку **Добавить учетную запись**). Кроме того, убедитесь, что эмулятор подключен к Интернету.

>[AZURE.NOTE]Структура уведомлений в версии платформы Android 5.0 и более поздних версиях существенно отличается от структуры в предыдущих версиях. Если вы проверяете это на платформе Android версии 5.0 или более поздней версии, необходимо запустить приложение, чтобы получить уведомление. Дополнительную информацию см. в разделе [Уведомления Android](http://go.microsoft.com/fwlink/?LinkId=615880).


1. В меню **Средства** нажмите **Открыть диспетчер эмулятора Android**, выберите свое устройство и нажмите кнопку **Изменить**.

   	![][18]

2. Выберите **API-интерфейсы Google** в разделе **Цель**, а затем нажмите кнопку **ОК**.

   	![][19]

3. На верхней панели инструментов нажмите кнопку **Запуск** и выберите приложение. При этом запускается эмулятор и выполняется приложение.

  Приложение получает значение *registrationId* из GCM и регистрируется в концентраторе уведомлений.

##Отправка уведомлений из серверной части


Можно проверить получение уведомлений в приложении, отправив уведомления на портале Azure с помощью вкладки отладки центра уведомлений, как показано на следующем экране.

![][30]


Push-уведомления обычно отправляются в серверной службе, например мобильными службами или ASP.NET, с помощью совместимой библиотеки. Также можно напрямую использовать API REST для отправки уведомлений, если библиотека недоступна для серверной части.

Ниже приведен список других учебников, касающихся отправки уведомлений.

- ASP.NET: см. раздел [Использование центров уведомлений для отправки push-уведомлений пользователям].
- Пакет SDK Центров уведомлений Azure для Java: см. раздел [Использование Центров уведомлений из Java](notification-hubs-java-backend-how-to.md) для отправки уведомлений из Java. Было протестировано в Eclipse для разработки для Android.
- PHP: см. статью [Использование Центров уведомлений из PHP](notification-hubs-php-backend-how-to.md).


В следующих подразделах этого учебника вы отправляете уведомления с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

###Порядок отправки уведомлений с помощью приложения .NET


Корпорация Майкрософт предоставляет пакет SDK для служебной шины Azure для отправки уведомлений на платформе .NET. В этом разделе вы с помощью Visual Studio создадите консольное приложение .NET для использования пакета SDK для служебной шины Azure для отправки уведомления.

1. Создайте новое консольное приложение Visual C#.

   	![][20]

2. Добавьте ссылку на пакет SDK служебной шины Azure с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис** > **Диспетчер пакетов библиотеки** > **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.ServiceBus

    и нажмите клавишу ВВОД.

2. Откройте файл Program.cs и добавьте следующий оператор using:

        using Microsoft.ServiceBus.Notifications;

3. Добавьте в класс `Program` следующий метод: Замените текст заполнителя на значение вашей строки подключения *DefaultFullSharedAccessSignature* и имя концентратора на портале Azure.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ "data" : {"message":"Hello from Azure!"}}");
        }

4. Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

5. Нажмите клавишу F5, чтобы запустить приложение. Вы получите уведомление в приложении.

   	![][21]

###Порядок отправки уведомления с помощью мобильной службы

1. Выполните действия, описанные в разделе [Приступая к работе с мобильными службами].

1. Войдите на [портал Azure] и выберите мобильную службу.

2. Откройте расположенную сверху вкладку **Планировщик**.

   	![][22]

3. Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.

   	![][23]

4. Создав задание, щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.

5. Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученными ранее. Щелкните **Сохранить**.

        var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
    	  function (error)
    	  {
        	if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
	    );

6. Нажмите кнопку **Запустить один раз** на нижней панели. Вы получите всплывающее уведомление.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется широковещательная рассылка уведомлений на все устройства Android. Чтобы сориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании Центров уведомлений см. в разделах [Руководство по использованию Центров уведомлений] и [Инструкции по использованию Центров уведомлений для Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[портал Azure]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководство по использованию Центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию Центров уведомлений для Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /manage/services/notification-hubs/notify-users-aspnet
[Использование центров уведомлений для отправки push-уведомлений пользователям]: /manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Компонент клиента Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Компонент обмена сообщениями в Azure]: http://components.xamarin.com/view/azure-messaging

<!---HONumber=August15_HO9-->