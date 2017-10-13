---
title: "Добавление push-уведомлений в приложение Xamarin.Forms | Документация Майкрософт"
description: "Использование служб Azure для отправки кроссплатформенных push-уведомлений в приложения Xamarin.Forms."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: 912367636f1b26b3b07fbd5fe3fe8ed053218fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Добавление push-уведомлений в приложение Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Обзор
В этом руководстве вы добавите push-уведомления во все проекты, которые были созданы на основе [проекта быстрого запуска Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Это значит, что push-уведомление будет отправляться во все кроссплатформенные клиенты при каждой вставке записи.

Если вы не используете скачанный проект сервера, необходимо добавить пакет расширений для push-уведомлений. Дополнительные сведения см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Предварительные требования
Для iOS потребуется [участие в программе для разработчиков на платформе Apple](https://developer.apple.com/programs/ios/) и физическое устройство iOS. [Симулятор iOS не поддерживает push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Настройка концентратора уведомлений
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Обновление серверного проекта для отправки push-уведомлений
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Настройка и запуск проекта Android (необязательно)
Выполнив инструкции из этого раздела, вы добавите push-уведомления для проекта Xamarin.Forms Droid для платформы Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Включение Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Настройка серверной части мобильных приложений для отправки push-запросов с использованием FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Добавление push-уведомлений в проект Android
Настроив FCM для серверной части, в клиент можно добавить компоненты и код для регистрации в службе FCM. Можно также выполнить регистрацию в Центрах уведомлений Azure, чтобы отправлять push-уведомления через серверную часть мобильных приложений и получать уведомления.

1. В проекте **Droid** щелкните правой кнопкой мыши папку **Components** и выберите **Get More Components…** (Получить дополнительные компоненты). Найдите компонент **Google Cloud Messaging Client** и добавьте его в проект. Этот компонент поддерживает push-уведомления для проекта Xamarin Android.
2. Откройте файл проекта MainActivity.cs и добавьте приведенный ниже оператор в начало файла.

        using Gcm.Client;
3. Добавьте в метод **OnCreate** после строки вызова **LoadApplication** следующий код:

        try
        {
            // Check to ensure everything's set up right
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
4. Добавьте новый вспомогательный метод **CreateAndShowDialog** , как показано ниже:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. Добавьте следующий код в класс **MainActivity** :

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
7. Добавьте в проект **Droid** новый файл класса с именем `GcmService.cs`. В начале файла этого класса обязательно должны присутствовать следующие операторы **using**:

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
8. Добавьте в начало файла (после операторов **using**, но до объявления **namespace**) следующие запросы на разрешение.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. Добавьте следующее определение класса в пространство имен.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > Замените **<PROJECT_NUMBER>** номером своего проекта, который вы записали ранее.    
   >
   >
10. Замените пустой класс **GcmService** следующим кодом, который использует новый получатель широковещательных сообщений:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. Добавьте в класс **GcmService** приведенный далее код. Так вы переопределите обработчик событий **OnRegistered** и реализуете метод **Register**.

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
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

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

    Обратите внимание, что этот код использует параметр `messageParam` в регистрации шаблона.
12. Добавьте следующий код, который реализует метод **OnMessage**:

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
13. Для **GcmServiceBase** также потребуется реализовать методы обработчика **OnUnRegistered** и **OnError**, например так:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Теперь все готово, и вы можете проверить отправку push-уведомлений с помощью приложения, работающего на устройстве Android или в эмуляторе.

### <a name="test-push-notifications-in-your-android-app"></a>Тестирование push-уведомлений в приложении Android
Первые два шага нужны, только если тестирование выполняется в эмуляторе.

1. Убедитесь, что для развертывания или отладки используется виртуальное устройство, на котором в качестве назначения заданы интерфейсы Google API, как показано ниже в диспетчере виртуальных устройств Android.
2. Добавить учетную запись Google на устройстве Android, поочередно щелкнув **Apps** (Приложения) > **Settings** (Параметры) > **Add account** (Добавить учетную запись). Затем следуйте указаниям в отображаемых запросах, чтобы добавить на устройство имеющуюся учетную запись Google или создать новую.
3. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **Droid** и выберите **Назначить запускаемым проектом**.
4. Нажмите кнопку **Запустить** , чтобы создать проект и запустить приложение на устройстве Android или в эмуляторе.
5. В приложении введите задачу, а затем щелкните значок плюса (**+**).
6. После добавления элемента должно отобразиться соответствующее уведомление.

## <a name="configure-and-run-the-ios-project-optional"></a>Настройка и запуск проекта iOS (необязательно)
В этом разделе описано, как запустить проект Xamarin iOS для устройств под управлением iOS. Пропустите этот раздел, если вы не работаете с устройствами iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Настройка концентратора уведомлений для APNs
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Далее следует настроить параметры проекта iOS в Xamarin Studio или Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Добавление push-уведомлений в приложение iOS
1. В проекте **iOS** откройте файл AppDelegate.cs и добавьте в его начало приведенный ниже оператор.

        using Newtonsoft.Json.Linq;
2. В класс **AppDelegate** добавьте переопределение для события **RegisteredForRemoteNotifications**, чтобы выполнить регистрацию для получения уведомлений.

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. Добавьте также в класс **AppDelegate** следующее переопределение для обработчика событий **DidReceiveRemoteNotification**.

        public override void DidReceiveRemoteNotification(UIApplication application,
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
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

    Этот метод обрабатывает входящие уведомления во время выполнения приложения.
4. В классе **AppDelegate** добавьте следующий код в метод **FinishedLaunching**.

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Он обеспечивает поддержку удаленных уведомлений и запрашивает регистрацию push-уведомлений.

Ваше приложение теперь обновлено для поддержки push-уведомлений.

#### <a name="test-push-notifications-in-your-ios-app"></a>Тестирование push-уведомлений в приложении iOS
1. Щелкните проект iOS правой кнопкой мыши и выберите **Назначить запускаемым проектом**.
2. Нажмите кнопку **Запустить** или клавишу **F5** в Visual Studio, чтобы выполнить сборку проекта и запустить приложение на устройстве iOS. Затем нажмите кнопку **ОК**, чтобы принять push-уведомления.

   > [!NOTE]
   > Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.
   >
   >
3. В приложении введите задачу, а затем щелкните значок плюса (**+**).
4. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы его закрыть.

## <a name="configure-and-run-windows-projects-optional"></a>Настройка и запуск проектов для Windows (необязательно)
В этом разделе описано, как запускать проекты Xamarin.Forms WinApp и WinPhone81 на устройствах под управлением Windows. Эти действия применимы для любых проектов универсальной платформы Windows (UWP). Пропустите этот раздел, если вы не работаете с устройствами Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Регистрация приложения для Windows для получения push-уведомлений через службу уведомлений Windows (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Настройка концентратора уведомлений для WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Добавление push-уведомлений в приложение Windows
1. В Visual Studio откройте файл проекта Windows **App.xaml.cs** и добавьте следующие операторы.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Вместо `<your_TodoItemManager_portable_class_namespace>` укажите пространство имен своего переносимого проекта, который содержит класс `TodoItemManager`.
2. В файл App.xaml.cs добавьте следующий метод **InitNotificationsAsync**.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS =
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

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

    Этот метод получает канал push-уведомлений и регистрирует шаблон для получения шаблонных уведомлений из центра уведомлений. Клиенту будет доставлено шаблонное уведомление, поддерживающее параметр *messageParam* .
3. В файле App.xaml.cs измените определение метода обработчика событий **OnLaunched**, добавив в него модификатор `async`. В конец метода добавьте следующую строку кода.

        await InitNotificationsAsync();

    Теперь push-уведомления будут создаваться или обновляться при каждом запуске приложения. Это нужно, чтобы push-канал WNS всегда оставался активным.  
4. В обозревателе решений для Visual Studio откройте файл **Package.appxmanifest** и в разделе **Notifications** задайте для параметра **Toast Capable** значение **Yes**.
5. Выполните сборку приложения и убедитесь в отсутствии ошибок. Теперь клиентское приложение должно зарегистрироваться для получения шаблонных уведомлений из серверной части мобильных приложений. Повторите действия из этого раздела для каждого проекта Windows, входящего в ваше решение.

#### <a name="test-push-notifications-in-your-windows-app"></a>Тестирование push-уведомлений в приложении Windows
1. В Visual Studio щелкните правой кнопкой мыши проект Windows и выберите **Назначить запускаемым проектом**.
2. Нажмите кнопку **Запуск** , чтобы создать проект и запустить приложение.
3. В приложении введите имя нового объекта todoitem и добавьте его, щелкнув знак "плюс" (**+**).
4. После добавления элемента должно отобразиться соответствующее уведомление.

## <a name="next-steps"></a>Дальнейшие действия
Вы можете узнать больше о push-уведомлениях.

* [Диагностика неполадок, связанных с push-уведомлениями](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Существуют различные причины, по которым уведомления могут теряться или не доходить до устройств. В этой статье рассказывается, как проанализировать и определить основную причину сбоев push-уведомлений.

Кроме того, вы можете изучить одно из следующих руководств:

* [Добавление проверки подлинности в приложение](app-service-mobile-xamarin-forms-get-started-users.md)  
  Узнайте больше о проверке подлинности пользователей приложения с помощью поставщика удостоверений.
* [Включение автономной синхронизации для приложения](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Узнайте, как добавить в приложение поддержку автономной работы с помощью серверной части мобильных приложений. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением (&mdash;просматривать, добавлять или изменять данные&mdash;) даже при отсутствии подключения к сети.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
