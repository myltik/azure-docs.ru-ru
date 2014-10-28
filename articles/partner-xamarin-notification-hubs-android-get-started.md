<properties linkid="manage-services-notification-hubs-getting-started-xamarin-android" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding">

[Windows Universal][Windows Universal][Windows Phone][Windows Phone][iOS][iOS][Android][Android][Kindle][Kindle][Xamarin.iOS][Xamarin.iOS][Xamarin.Android][Xamarin.Android]

</div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение на платформе Xamarin.Android.
В этом учебнике вам предстоит создать пустое приложение Xamarin.Android, получающее push-уведомления с помощью службы push-уведомлений Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений. Готовый код доступен в примере [приложения NotificationHubs][приложения NotificationHubs].

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Включение Google Cloud Messaging][Включение Google Cloud Messaging]
2.  [Настройка концентратора уведомлений][Настройка концентратора уведомлений]
3.  [Подключение приложения к концентратору уведомлений][Подключение приложения к концентратору уведомлений]
4.  [Запуск приложения с помощью эмулятора][Запуск приложения с помощью эмулятора]
5.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Для работы с данным учебником требуется следующее:

-   [Xamarin.Android][1]
-   Активная учетная запись Google
-   [Компонент мобильных служб Azure][Компонент мобильных служб Azure]
-   [Компонент Google Cloud Messaging][Компонент Google Cloud Messaging]

Для прохождения других учебников по концентраторам уведомлений для приложений Xamarin.Android необходимо сначала выполнить действия, указанные в этом учебнике.

<div class="dev-callout">

**Примечание.**
Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

</div>

## <a name="register"></a><span class="short-header">Включение Google Cloud Messaging</span>Включение Google Cloud Messaging

<div class="dev-callout">

**Примечание.**
Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке [accounts.google.com][accounts.google.com].

</div>

1.  Перейдите на веб-сайт [Google apis][Google apis] и войдите с помощью учетной записи Google. Выберите элемент **Создать проект...**.

    ![][]

    <div class="dev-callout">

    **Примечание.**
    При наличии существующего проекта после входа в систему открывается страница **Панель мониторинга**. Чтобы создать новый проект из панели мониторинга, откройте **Проект API**, нажмите **Создать...** в разделе **Другие проекты**, введите имя проекта и нажмите кнопку **Создать проект**.

    </div>

2.  Нажмите кнопку **Обзор** в левом столбце и запишите номер проекта в разделе **Панель мониторинга**.

    Позже в этом учебнике данное значение будет использоваться как переменная PROJECT\_ID для клиента.

3.  В [Google apis][Google apis] выберите элемент **Службы**, включите **Google Cloud Messaging for Android**, нажав переключатель, и примите условия соглашения.

4.  Выберите элемент **Доступ к API** и нажмите **Создать новый ключ сервера...**

    ![][2]

5.  В области **Настройка ключа сервера для проекта API** нажмите кнопку **Создать**.

    ![][3]

6.  Запишите значение **Ключ API**.

    ![][4]

Далее вы будете использовать это значение ключа API, чтобы дать концентратору уведомлений возможность выполнять проверку подлинности с использованием GCM и отправлять push-уведомления от имени вашего приложения.

## <a name="configure-hub"></a><span class="short-header">Настройка концентратора уведомлений</span>Настройка концентратора уведомлений

1.  Войдите на [портал управления Аzure][портал управления Аzure] и нажмите элемент **+СОЗДАТЬ** в нижней части экрана.

2.  Последовательно нажмите элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

    ![][5]

3.  Введите имя для концентратора уведомлений, выберите нужный регион и нажмите элемент **Создать новый концентратор уведомлений**.

    ![][6]

4.  Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и перейдите на расположенную сверху вкладку **Настройка**.

    ![][7]

5.  Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

    ![][8]

6.  Перейдите на расположенную сверху вкладку **Настройка**, введите значение **Ключ API**, полученное в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

    ![][9]

7.  Откройте расположенную сверху вкладку **Панель мониторинга** и нажмите **Сведения о подключении**. Запишите значения двух строк подключения.

    ![][10]

Концентратор уведомлений теперь настроен для работы с GCM, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять push-уведомления.

## <a name="connecting-app"></a><span class="short-header">Подключение приложения</span>Подключение приложения к концентратору уведомлений

### Создание нового проекта

1.  В Xamarin Studio (или Visual Studio) создайте новый проект Android (пункты меню "Файл -\> Создать -\> Решение -\> Приложение Android").

    ![][11]
    ![][12]

2.  Откройте свойства проекта, щелкнув правой клавишей мыши в представлении решений новый проект и выбрав пункт **Параметры**. Выберите элемент **Приложение Android** в разделе **Сборка**.

    ![][13]

3.  Для параметра **Минимальная версия Android** выберите уровень API 8.

4.  Для параметра **Целевая версия Android** установите версию API, которую нужно использовать в качестве целевой (используйте API уровня 8 или более высокого уровня).

5.  Убедитесь, что **Имя пакета** начинается со строчной буквы.

    <div class="dev-callout">

    **Примечание.**
    Первая буква имени пакета должна быть строчной. В противном случае возникнут ошибки манифеста приложения при регистрации \*\*BroadcastReceiver\*\* и \*\*IntentFilter\*\* для push-уведомлений, приведенных ниже.

    </div>

### Добавление клиента Google Cloud Messaging в проект

Клиент Google Cloud Messaging, доступный в магазине компонентов Xamarin, упрощает процесс поддержки push-уведомлений в Xamarin.Android.

1.  Щелкните правой клавишей мыши по папке "Компоненты" в приложении Xamarin.Android и выберите пункт **Получить дополнительные компоненты...**

2.  Выполните поиск компонента **Клиент Google Cloud Messaging**.

3.  Добавьте данный компонент в приложение Xamarin.Android. Необходимые ссылки на сборки будут добавлены автоматически.

### Добавление Xamarin.NotificationHub в проект

Эта сборка позволяет легко выполнять регистрацию в концентраторах уведомлений Azure. Следуйте приведенной ниже инструкции, чтобы загрузить ее, или найдите ее в разделе [загрузка примера][приложения NotificationHubs].

1.  Посетите [страницу Xamarin.NotificationHub Github][страницу Xamarin.NotificationHub Github], выполните загрузку и построение исходной папки.

2.  Создайте папку \*\*\_external\*\* в папке проекта Xamarin.Android и скопируйте в нее скомпилированную библиотеку **ByteSmith.WindowsAzure.Messaging.Android.dll**.

3.  Откройте проект Xamarin.Android в Xamarin Studio (или Visual Studio).

4.  Щелкните правой клавишей мыши по папке **Ссылки** проекта и выберите команду **Изменить ссылки...**

5.  Откройте вкладку **.Net Assembly**, перейдите к папке своего проекта \*\*\_external\*\*, выберите созданную ранее библиотеку **ByteSmith.WindowsAzure.Messaging.Android.dll** и нажмите кнопку **Добавить**. Нажмите кнопку "ОК", чтобы закрыть диалоговое окно.

### Настройка концентраторов уведомлений в проекте

1.  Создайте класс **Constants.cs** и определите следующие значения констант (замените заполнители конкретными значениями):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2.  Добавьте следующие элементы, используя операторы **MainActivity.cs**:

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

3.  Создайте следующий метод в классе **MainActivity**:

        private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4.  Создайте новый класс **MyBroadcastReceiver**.

    <div class="dev-callout">

    **Примечание.**
    Мы рассмотрим создание \*\*BroadcastReceiver\*\* с нуля. Быстрой альтернативой созданию \*\*MyBroadcastReceiver.cs\*\* вручную является использование ссылки на файл \*\*GcmService.cs\*\*, находящийся в образце проекта Xamarin.Android на GitHub. Дублирование \*\*GcmService.cs\*\* и изменение имен классов могут быть отличным вариантом для начала.

    </div>

5.  Добавьте следующие элементы, используя операторы **MyBroadcastReceiver.cs** (ссылки на компонент и сборку были добавлены ранее):

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

6.  Добавьте следующие разрешения запросов между операторами **using** и объявлением **namespace**:

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  В операторе **MyBroadcastReceiver.cs** измените класс **MyBroadcastReceiver** таким образом, чтобы он соответствовал следующему коду:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

8.  Добавьте другой класс в оператор **MyBroadcastReceiver.cs** с именем **PushHandlerService**, который является производным от **PushHandlerServiceBase**. Убедитесь в том, что для класса используется директива **Служба**:

        [Service] //Must use the service tag
        public class GcmService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public GcmService() : base(Constants.SenderID) 
            {
                Log.Info(MyBroadcastReceiver.TAG, "GcmService() constructor"); 
            }
        }

9.  **GcmServiceBase** реализует методы **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** и **OnError()**. Наш класс реализации **GcmService** должен переопределить эти методы, и эти методы будут срабатывать в ответ на взаимодействие с концентратором уведомлений.

10. Переопределите метод **OnRegistered()** в **PushHandlerService** с использованием следующего кода:

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService-GCM Registered...", "The device has been Registered, Tap to View!");

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

    <div class="dev-callout">

    **Примечание.**
    В коде \*\*OnRegistered()\*\* выше обратите внимание на возможность задать теги для регистрации на конкретные каналы обмена сообщениями.

    </div>

11. Переопределите метод **OnMessage** в **PushHandlerService** с использованием следующего кода:

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

12. Добавьте следующий метод **createNotification** к **PushHandlerService** для уведомления пользователей, как указано.

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

13. Переопределите абстрактные члены **OnUnRegistered()**, **OnRecoverableError()** и **OnError()** для компиляции кода.

## <a name="run-app"></a><span class="short-header">Запустите приложение</span>Запустите свое приложение в эмуляторе

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1.  В меню **Средства** нажмите **Открыть диспетчер эмулятора Android**, выберите свое устройство и нажмите кнопку **Изменить**.

    ![][14]

2.  Выберите **API-интерфейсы Google** в поле **Цель**, а затем нажмите кнопку **OK**.

    ![][15]

3.  На верхней панели инструментов нажмите кнопку **Запуск** и выберите приложение. При этом запускается эмулятор и выполняется приложение.

4.  Приложение получает значение *registrationId* из GCM и регистрируется в концентраторе уведомлений.

    <div class="dev-callout">

    **Примечание.**
    Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите пункты \*\*Параметры\*\* и \*\*Добавить учетную запись\*\*). Кроме того, убедитесь, что эмулятор подключен к Интернету.

    </div>

## <a name="send"></a><span class="short-header">Отправка уведомления</span>Отправка уведомления из серверной части

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью [интерфейса REST][интерфейса REST]. В этом учебнике уведомления отправляются с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

Порядок отправки уведомлений с помощью приложения .NET:

1.  Создайте новое консольное приложение Visual C#.

    ![][16]

2.  Добавьте ссылку на пакет Azure Service Bus SDK с помощью [пакета NuGet WindowsAzure.ServiceBus][пакета NuGet WindowsAzure.ServiceBus]. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.ServiceBus

    и нажмите клавишу ВВОД.

3.  Откройте файл Program.cs и добавьте следующий оператор using:

        using Microsoft.ServiceBus.Notifications;

4.  Добавьте в класс `Program` следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5.  Затем добавьте следующие строки в метод Main:

         SendNotificationAsync();
         Console.ReadLine();

6.  Нажмите клавишу F5, чтобы запустить приложение. Вы получите всплывающее уведомление.

    ![][17]

Чтобы отправить уведомление с помощью мобильной службы, следуйте инструкциям из раздела [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами], а затем выполните следующие действия:

1.  Выполните вход на [портал управления Аzure][портал управления Аzure] и выберите мобильную службу.

2.  Откройте расположенную сверху вкладку **Планировщик**.

    ![][18]

3.  Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.

    ![][19]

4.  После создания задания щелкните по его имени. Откройте вкладку **Скрипт** в верхней панели.

5.  Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученными ранее. Щелкните **Сохранить**.

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

6.  Нажмите кнопку **Запустить один раз** на нижней панели. Вы получите всплывающее уведомление.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Android. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям][Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в разделах [Руководство по использованию концентраторов уведомлений][Руководство по использованию концентраторов уведомлений] и [Инструкции по использованию концентраторов уведомлений для Android][Инструкции по использованию концентраторов уведомлений для Android].

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /ru-ru/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [приложения NotificationHubs]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Включение Google Cloud Messaging]: #register
  [Настройка концентратора уведомлений]: #configure-hub
  [Подключение приложения к концентратору уведомлений]: #connecting-app
  [Запуск приложения с помощью эмулятора]: #run-app
  [Отправка уведомлений из серверной части]: #send
  [1]: http://xamarin.com/download/
  [Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [Компонент Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  []: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
  [2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
  [3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
  [4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png
  [портал управления Аzure]: https://manage.windowsazure.com/
  [5]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [6]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
  [10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png
  [11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
  [12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
  [13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
  [страницу Xamarin.NotificationHub Github]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
  [14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [интерфейса REST]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx
  [16]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
  [пакета NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [17]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
  [18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
  [19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png
  [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
  [Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
  [Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
  [Инструкции по использованию концентраторов уведомлений для Android]: http://msdn.microsoft.com/ru-ru/library/dn282661.aspx
