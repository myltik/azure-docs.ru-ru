1. Создайте класс в проекте с именем `ToDoBroadcastReceiver`.
2. Добавьте следующие инструкции using в класс **ToDoBroadcastReceiver** :
   
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
3. Добавьте следующие разрешения запросов между операторами **using** и объявлением **namespace**:
   
        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
   
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
4. Замените существующее определение класса **ToDoBroadcastReceiver** следующим:
   
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
        public class ToDoBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            // Set the Google app ID.
            public static string[] senderIDs = new string[] { "<PROJECT_NUMBER>" };
        }
   
    В приведенном выше коде необходимо заменить *`<PROJECT_NUMBER>`* номером проекта, назначенным службой Google при подготовке приложения к работе на портале разработчика Google. 
5. В файле проекта ToDoBroadcastReceiver.cs добавьте следующий код, который определяет класс **PushHandlerService** :
   
        // The ServiceAttribute must be applied to the class.
        [Service] 
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
   
            public PushHandlerService() : base(ToDoBroadcastReceiver.senderIDs) { }
        }
   
    Обратите внимание, что этот класс является производным от **GcmServiceBase**, поэтому к нему необходимо применить атрибут **Service**.
   
   > [!NOTE]
   > Класс **GcmServiceBase** реализует методы **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** и **OnError()**. Эти методы необходимо переопределить в классе **PushHandlerService** .
   > 
   > 
6. Добавьте приведенный ниже код в класс **PushHandlerService**, который переопределяет обработчик событий **OnRegistered**. 
   
        protected override void OnRegistered(Context context, string registrationId)
        {
            System.Diagnostics.Debug.WriteLine("The device has been registered with GCM.", "Success!");
   
            // Get the MobileServiceClient from the current activity instance.
            MobileServiceClient client = ToDoActivity.CurrentActivity.CurrentClient;
            var push = client.GetPush();
   
            // Define a message body for GCM.
            const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
   
            // Define the template registration as JSON.
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
              {"body", templateBodyGCM }
            };
   
            try
            {
                // Make sure we run the registration on the same thread as the activity, 
                // to avoid threading errors.
                ToDoActivity.CurrentActivity.RunOnUiThread(
   
                    // Register the template with Notification Hubs.
                    async () => await push.RegisterAsync(registrationId, templates));
   
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Push Installation Id", push.InstallationId.ToString()));
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Error with Azure push registration: {0}", ex.Message));
            }
        }
   
    В этом методе возвращенный идентификатор регистрации GCM используется для регистрации push-уведомлений в службе Azure. Теги могут добавляться к регистрации только после ее создания. Дополнительные сведения см. в разделе [Практическое руководство. Включение принудительной отправки push-уведомлений с использованием тегов](../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).
7. Переопределите метод **OnMessage** в **PushHandlerService** с использованием следующего кода:
   
       protected override void OnMessage(Context context, Intent intent)
       {          
           string message = string.Empty;
   
           // Extract the push notification message from the intent.
           if (intent.Extras.ContainsKey("message"))
           {
               message = intent.Extras.Get("message").ToString();
               var title = "New item added:";
   
               // Create a notification manager to send the notification.
               var notificationManager = 
                   GetSystemService(Context.NotificationService) as NotificationManager;
   
               // Create a new intent to show the notification in the UI. 
               PendingIntent contentIntent = 
                   PendingIntent.GetActivity(context, 0, 
                   new Intent(this, typeof(ToDoActivity)), 0);              
   
               // Create the notification using the builder.
               var builder = new Notification.Builder(context);
               builder.SetAutoCancel(true);
               builder.SetContentTitle(title);
               builder.SetContentText(message);
               builder.SetSmallIcon(Resource.Drawable.ic_launcher);
               builder.SetContentIntent(contentIntent);
               var notification = builder.Build();
   
               // Display the notification in the Notifications Area.
               notificationManager.Notify(1, notification);
   
           }
       }
8. Переопределите методы **OnUnRegistered()** и **OnError()** с помощью следующего кода:
   
       protected override void OnUnRegistered(Context context, string registrationId)
       {
           throw new NotImplementedException();
       }
   
       protected override void OnError(Context context, string errorId)
       {
           System.Diagnostics.Debug.WriteLine(
               string.Format("Error occurred in the notification: {0}.", errorId));
       }



<!--HONumber=Nov16_HO3-->


