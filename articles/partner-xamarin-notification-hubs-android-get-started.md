<properties linkid="" urlDisplayName="" pageTitle="Приступая к работе с концентраторами уведомлений для Xamarin.Android" metaKeywords="" description="Узнайте, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение Xamarin Android." metaCanonical="" authors="elioda" solutions="" manager="" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Приступая к работе с концентраторами уведомлений" />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android. 
В этом учебнике вы создаете пустое приложение Xamarin.Android, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Включение Google Cloud Messaging]
2. [Настройка концентратора уведомлений]
3. [Подключение приложения к концентратору уведомлений]
4. [Запуск приложения с помощью эмулятора]
5. [Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. <!-- Be sure to follow along with the next tutorial to see how to use notification hubs to address specific users and groups of devices. --> Для работы с данным учебником требуется следующее:

+ Xamarin.Android
+ Активная учетная запись Google

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Android. 

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

<h2><a name="register"></a><span class="short-header">Включение Google Cloud Messaging</span>Включение Google Cloud Messaging</h2>

<p></p>

<div class="dev-callout"><b>Примечание.</b>
<p>Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> и войдите с помощью учетной записи Google. Нажмите кнопку **Создать проект...**.

   	![][1]   
	
	<div class="dev-callout"><b>Примечание.</b>
	<p>При наличии существующего проекта после входа в систему открывается страница <strong>Панель мониторинга</strong>. Чтобы создать новый проект из панели мониторинга, откройте <strong>Проект API</strong>, нажмите <strong>Создать...</strong> в разделе <strong>Другие проекты</strong>, введите имя проекта и нажмите кнопку <strong>Создать проект</strong>.</p>
    </div>

2. Нажмите кнопку **Обзор** в левом столбце и запишите номер проекта в разделе **Панель мониторинга**. 

	Позже в этом учебнике данное значение будет использоваться как переменная PROJECT_ID для клиента.

3. В <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> щелкните элемент **Службы**, щелкните переключатель, чтобы включить **Google Cloud Messaging for Android**, и примите условия соглашения. 

4. Щелкните элемент **Доступ к API** и затем **Создать новый ключ сервера...** 

   	![][2]

5. В области **Настройка ключа сервера для проекта API** нажмите кнопку **Создать**.

   	![][3]

6. Запишите значение **Ключ API**.

   	![][4] 

Далее вы будете использовать это значение ключа API, чтобы дать концентратору уведомлений возможность выполнять проверку подлинности с использованием GCM и отправлять push-уведомления от имени вашего приложения.

<h2><a name="configure-hub"></a><span class="short-header">Настройка концентратора уведомлений</span>Настройка концентратора уведомлений</h2>

1. Войдите на [портал управления Azure] и щелкните элемент **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][7]

3. Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][8]

4. Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и щелкните расположенный сверху элемент **Настройка**.

   	![][9]

5. Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

   	![][10]

6. Перейдите на расположенную сверху вкладку **Настройка**, введите значение **Ключ API**, полученное в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

   	![][11]

7. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Сведения о подключении**. Запишите значения двух строк подключения.

   	![][12]

Концентратор уведомлений теперь настроен для работы с GCM, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять push-уведомления.

<h2><a name="connecting-app"></a><span class="short-header">Подключение приложения</span>Подключение приложения к концентратору уведомлений</h2>

### Создание проекта

1. В Xamarin Studio (или Visual Studio) создайте новый проект Android (Файл, Создать, Решение, Приложение Android).

   	![][13]   
   	![][14]

2. Откройте свойства проекта, щелкнув правой кнопкой мыши в представлении решений новый проект и выбрав **Параметры**. Выберите элемент **Приложение Android** в разделе **Сборка**.

   	![][15]

3. Для параметра **Минимальная версия Android** выберите уровень API 8.

4. Для параметра **Целевая версия Android** установите версию API, которую нужно использовать в качестве целевой (используйте API уровня 8 или более высокого уровня).

5. Убедитесь, что **Имя пакета** начинается со строчной буквы.

	<div class="dev-callout"><b>Примечание.</b>
    <p>Первая буква имени пакета должна быть строчной. В противном случае возникнут ошибки манифеста приложения при регистрации **BroadcastReceiver** и **IntentFilter** для push-уведомлений ниже.</p>
    </div> 

### Добавление в проект PushSharp

1. Теперь нужно добавить в проект **PushSharp** в качестве ссылки. Для этого нам следует скомпилировать последнюю версию PushSharp и добавить скомпилированную библиотеку DLL в качестве ссылки на наш проект Xamarin.Android.

2. Посетите [страницу PushSharp Github] и загрузите последнюю версию. После извлечения коллекции файлов перейдите к следующей папке с примером проекта:

	**/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

	.. и откройте следующий файл проекта в Xamarin Studio (или Visual Studio):
	
	**  PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3. Постройте образец клиента MonoForAndroid PushSharp в режиме **Выпуск**.

4. Создайте папку **_external** в папке проекта Xamarin.Android

5. Скопируйте следующий файл из образца клиента MonoForAndroid PushSharp в созданную папку **_external** в папке проекта Xamarin.Android:

	 **\bin\Release\PushSharp.Client.MonoForAndroid.dll**

6. Откройте проект Xamarin.Android в Xamarin Studio (или Visual Studio). 

7. Щелкните правой кнопкой мыши папку **Ссылки** проекта и выберите команду **Изменить ссылки...**

8. Откройте вкладку **Сборка .NET**, перейдите к папке своего проекта **_external**, выберите созданную ранее библиотеку **PushSharp.Client.MonoForAndroid.dll** и нажмите кнопку **Добавить**. Нажмите кнопку "ОК", чтобы закрыть диалоговое окно. 

### Добавьте в проект Xamarin.NotificationHub

1. Теперь нужно добавить библиотеки для поддержки класса NotificationHub. Посетите [страницу Xamarin.NotificationHub Github], выполните загрузку и построение исходной папки.

2. Скопируйте скомпилированный файл **ByteSmith.WindowsAzure.Messaging.Android.dll** в каталог **_external** в папке проекта Xamarin.Android.

3. Откройте проект Xamarin.Android в Xamarin Studio (или Visual Studio).

4. Щелкните правой кнопкой мыши папку **Ссылки** проекта и выберите команду **Изменить ссылки...**

5. Откройте вкладку **Сборка .NET**, перейдите к папке своего проекта **_external**, выберите созданную ранее библиотеку **ByteSmith.WindowsAzure.Messaging.Android.dll** и нажмите кнопку **Добавить**. Нажмите кнопку "ОК", чтобы закрыть диалоговое окно. 

### Настройка концентраторов уведомлений в проекте

1. Создайте класс **Constants.cs** и определите следующие значения констант (замените заполнители конкретными значениями):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>"";
        public const string NotificationHubPath = "<hub path>";

2. Добавьте следующие элементы, используя операторы **MainActivity.cs**:

		using ByteSmith.WindowsAzure.Messaging;
		using PushSharp.Client;

3. Создайте следующий метод в классе **MainActivity**:

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            PushClient.CheckDevice(this);
            PushClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            PushClient.Register(this, Constants.SenderID);
        }

4. Создайте новый класс **MyBroadcastReceiver**.

	<div class="dev-callout"><b>Примечание.</b>
    <p>Мы рассмотрим создание **BroadcastReceiver** с нуля. Быстрой альтернативой созданию **MyBroadcastReceiver.cs** вручную является использование ссылки на файл **PushService.cs**, находящийся в образце PushSharp проекта Xamarin.Android на GitHub. Дублирование **PushService.cs** и изменение имен классов могут быть отличным вариантом для начала.</p>
    </div> 

5. Добавьте следующие элементы, используя операторы **MyBroadcastReceiver.cs**:

		using ByteSmith.WindowsAzure.Messaging;
		using PushSharp.Client;

5. Добавьте следующие разрешения запросов между операторами **using** и объявлением **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. В **MyBroadcastReceiver.cs** измените класс **MyBroadcastReceiver**, как указано ниже:

    	[BroadcastReceiver(Permission=GCMConstants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : PushHandlerBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }
        
7. Добавьте другой класс в **MyBroadcastReceiver.cs** с именем **PushHandlerService**, который является производным от **PushHandlerServiceBase**. Убедитесь в том, что для класса используется директива **Служба**:

    	[Service] //Must use the service tag
    	public class PushHandlerService : PushHandlerServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID) 
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor"); 
        	}
    	}


8. **PushHandlerServiceBase** реализует методы **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** и **OnError()**. Наш класс реализации **PushHandlerService** должен переопределить эти методы, и эти методы будут срабатывать в ответ на взаимодействие с концентратором уведомления.

9. Переопределите метод **OnRegistered()** в **PushHandlerService** с использованием следующего кода:

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                await Hub.UnregisterAllAsync(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message); 
                Debugger.Break();
            }
        }

	<div class="dev-callout"><b>Примечание.</b>
    <p>В коде **OnRegistered()** выше обратите внимание на возможность задать теги для регистрации на конкретные каналы обмена сообщениями.</p>
    </div> 
    
10. Переопределите метод **OnMessage** в **PushHandlerService** с использованием следующего кода:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

11. Добавьте следующий метод **createNotification** к **PushHandlerService** для уведомления пользователей, как указано выше:

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
        
12. Переопределите абстрактные члены **OnUnRegistered()**, **OnRecoverableError()** и **OnError()** для компиляции кода.


<h2><a name="run-app"></a><span class="short-header">Запустите приложение</span>Запустите свое приложение в эмуляторе</h2>

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1. В меню **Средства** щелкните **Открыть диспетчер эмулятора Android**, выберите свое устройство и нажмите **Изменить**.

   	![][18]

2. Выберите **API-интерфейсы Google** в разделе **Цель**, а затем нажмите кнопку **OK**.

   	![][19]

3. В верхней панели инструментов нажмите кнопку **Запуск** и выберите приложение. При этом запускается эмулятор и выполняется приложение.

4. Приложение получает значение *registrationId* из GCM и регистрируется в концентраторе уведомлений.

	<div class="dev-callout"><b>Примечание.</b>
    <p>Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите **Параметры** и щелкните **Добавить учетную запись**). Кроме того, убедитесь, что эмулятор подключен к Интернету.</p>
    </div> 

<h2><a name="send"></a><span class="short-header">Отправка уведомления</span>Отправка уведомления из серверной части</h2>

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике уведомления отправляются с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

Порядок отправки уведомлений с помощью приложения .NET:

1. Создайте новое консольное приложение Visual C#: 

   	![][20]

2. Добавьте ссылку на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.ServiceBus

    и нажмите клавишу ВВОД.

2. Откройте файл Program.cs и добавьте следующий оператор "using":

        using Microsoft.ServiceBus.Notifications;

3. Добавьте в класс `Program` следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

4. Затем добавьте следующие строки в метод Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Нажмите клавишу F5, чтобы запустить приложение. Вы получите всплывающее уведомление.

   	![][21]

Чтобы отправить уведомление с помощью мобильной службы, следуйте инструкциям из раздела [Приступая к работе с мобильными службами], а затем выполните следующие действия:

1. Выполните вход на [портал управления Azure] и выберите мобильную службу.

2. Откройте расположенную сверху вкладку **Планировщик**.

   	![][22]

3. Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.

   	![][23]

4. После создания задания щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.

5. Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученными ранее. Щелкните **Сохранить**.

        var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
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

В этом простом примере осуществляется рассылка уведомлений на все устройства Android. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в разделах [Руководство по использованию концентраторов уведомлений] и [Инструкции по использованию концентраторов уведомлений для Android].

<!-- Anchors. -->
[Включение Google Cloud Messaging]: #register
[Настройка концентратора уведомлений]: #configure-hub
[Подключение приложения к концентратору уведомлений]: #connecting-app
[Запуск приложения с помощью эмулятора]: #run-app
[Отправка уведомлений из серверной части]: #send
[Следующие шаги]:#next-steps

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

<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js

[Портал управления Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководства по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для Android]: http://msdn.microsoft.com/ru-ru/library/dn282661.aspx

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
[страницу PushSharp Github]: https://github.com/Redth/PushSharp
[страницу Xamarin.NotificationHub Github]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329

