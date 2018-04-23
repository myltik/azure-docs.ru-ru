---
title: Добавление push-уведомлений в приложение Xamarin.Forms | Документация Майкрософт
description: Использование служб Azure для отправки кроссплатформенных push-уведомлений в приложения Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 0bea00d411205541684e807182abd3236c09bd9d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Добавление push-уведомлений в приложение Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Обзор
В этом руководстве вы добавите push-уведомления во все проекты, которые были созданы на основе [проекта быстрого запуска Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Это значит, что push-уведомление будет отправляться во все кроссплатформенные клиенты при каждой вставке записи.

Если вы не используете скачанный проект сервера, необходимо добавить пакет расширений для push-уведомлений. Дополнительные сведения см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>предварительным требованиям
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

1. В проекте **Droid** щелкните правой кнопкой мыши **References > Manage NuGet Packages** (Ссылки > Управление пакетами NuGet).
1. В окне диспетчера пакетов NuGet выполните поиск пакета **Xamarin.Firebase.Messaging** и добавьте его в проект.
1. В окне свойств проекта **Droid** настройте компиляцию приложения с помощью Android 7.0 или более поздней версии.
1. Добавьте файл **google-services.json**, скачанный из консоли Firebase, в корень проекта **Droid** и задайте для свойства Build Action (Действие сборки) значение **GoogleServicesJson**. Дополнительные сведения см. в разделе [Add the Google Services JSON File](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File) (Добавление JSON-файла служб Google).

#### <a name="registering-with-firebase-cloud-messaging"></a>Регистрация в Firebase Cloud Messaging

1. Откройте файл **AndroidManifest.xml** и вставьте следующие элементы `<receiver>` в элемент `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>Реализация службы идентификатора экземпляра Firebase

1. Добавьте в проект **Droid** новый класс с именем `FirebaseRegistrationService`. В начале файла этого класса обязательно должны быть указаны следующие инструкции `using`:

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. Замените пустой класс `FirebaseRegistrationService` следующим кодом:

        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class FirebaseRegistrationService : FirebaseInstanceIdService
        {
            const string TAG = "FirebaseRegistrationService";

            public override void OnTokenRefresh()
            {
                var refreshedToken = FirebaseInstanceId.Instance.Token;
                Log.Debug(TAG, "Refreshed token: " + refreshedToken);
                SendRegistrationTokenToAzureNotificationHub(refreshedToken);
            }

            void SendRegistrationTokenToAzureNotificationHub(string token)
            {
                // Update notification hub registration
                Task.Run(async () =>
                {
                    await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
                });
            }
        }

    Класс `FirebaseRegistrationService` отвечает за создание маркеров безопасности, которые предоставляют приложению доступ к FCM. Метод `OnTokenRefresh` вызывается, когда приложение получает маркер регистрации из FCM. Этот метод извлекает маркер из свойства `FirebaseInstanceId.Instance.Token`, которое асинхронно обновляется средствами FCM. Метод `OnTokenRefresh` вызывается редко, так как маркер обновляется только при установке или удалении приложения, при удалении данных приложения пользователем, при удалении идентификатора экземпляра приложением либо при компрометации маркера. Кроме того, служба идентификатора экземпляра FCM потребует, чтобы маркер приложения периодически обновлялся (обычно каждые 6 месяцев).

    Метод `OnTokenRefresh` также вызывает метод `SendRegistrationTokenToAzureNotificationHub`, который позволяет связать маркер регистрации пользователя с концентратором уведомлений Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Регистрация в концентраторе уведомлений Azure

1. Добавьте в проект **Droid** новый класс с именем `AzureNotificationHubService`. В начале файла этого класса обязательно должны быть указаны следующие инструкции `using`:

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. Замените пустой класс `AzureNotificationHubService` следующим кодом:

        public class AzureNotificationHubService
        {
            const string TAG = "AzureNotificationHubService";

            public static async Task RegisterAsync(Push push, string token)
            {
                try
                {
                    const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                    JObject templates = new JObject();
                    templates["genericMessage"] = new JObject
                    {
                        {"body", templateBody}
                    };

                    await push.RegisterAsync(token, templates);
                    Log.Info("Push Installation Id: ", push.InstallationId.ToString());
                }
                catch (Exception ex)
                {
                    Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
                }
            }
        }

    Метод `RegisterAsync` создает простой шаблон уведомления в формате JSON и регистрируется для получения шаблонов уведомлений из концентратора уведомлений с помощью маркера регистрации Firebase. Это гарантирует, что все уведомления, отправленные из концентратора уведомлений Azure, будут направляться на устройство, представленное маркером регистрации.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Отображение содержимого push-уведомления

1. Добавьте в проект **Droid** новый класс с именем `FirebaseNotificationService`. В начале файла этого класса обязательно должны быть указаны следующие инструкции `using`:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V7.App;
        using Android.Util;
        using Firebase.Messaging;

1. Замените пустой класс `FirebaseNotificationService` следующим кодом:

        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class FirebaseNotificationService : FirebaseMessagingService
        {
            const string TAG = "FirebaseNotificationService";

            public override void OnMessageReceived(RemoteMessage message)
            {
                Log.Debug(TAG, "From: " + message.From);

                // Pull message body out of the template
                var messageBody = message.Data["message"];
                if (string.IsNullOrWhiteSpace(messageBody))
                    return;

                Log.Debug(TAG, "Notification message body: " + messageBody);
                SendNotification(messageBody);
            }

            void SendNotification(string messageBody)
            {
                var intent = new Intent(this, typeof(MainActivity));
                intent.AddFlags(ActivityFlags.ClearTop);
                var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

                var notificationBuilder = new NotificationCompat.Builder(this)
                    .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                    .SetContentTitle("New Todo Item")
                    .SetContentText(messageBody)
                    .SetContentIntent(pendingIntent)
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                    .SetAutoCancel(true);

                var notificationManager = NotificationManager.FromContext(this);
                notificationManager.Notify(0, notificationBuilder.Build());
            }
        }

    Метод `OnMessageReceived`, который вызывается, когда приложение получает уведомление от FCM, извлекает содержимое сообщения и вызывает метод `SendNotification`. Этот метод преобразует содержимое сообщения в локальное уведомление, которое запускается во время выполнения приложения. При этом уведомление отображается в области состояния.

Теперь все готово, и вы можете проверить отправку push-уведомлений с помощью приложения, работающего на устройстве Android или в эмуляторе.

### <a name="test-push-notifications-in-your-android-app"></a>Тестирование push-уведомлений в приложении Android
Первые два шага нужны, только если тестирование выполняется в эмуляторе.

1. Убедитесь, что устройство или эмулятор, на котором выполняется развертывание или отладка, настроены с помощью Сервисов Google Play. Чтобы проверить это, посмотрите, установлены ли на устройстве или эмуляторе приложения **Play**.
2. Добавить учетную запись Google на устройстве Android, поочередно щелкнув **Apps** (Приложения) > **Settings** (Параметры) > **Add account** (Добавить учетную запись). Затем следуйте указаниям в отображаемых запросах, чтобы добавить на устройство имеющуюся учетную запись Google или создать новую.
3. В Visual Studio или Xamarin Studio щелкните правой кнопкой мыши проект **Droid** и выберите **Назначить запускаемым проектом**.
4. Нажмите кнопку **Запустить** , чтобы создать проект и запустить приложение на устройстве Android или в эмуляторе.
5. В приложении введите задачу, а затем щелкните значок плюса (**+**).
6. После добавления элемента должно отобразиться соответствующее уведомление.

## <a name="configure-and-run-the-ios-project-optional"></a>Настройка и запуск проекта iOS (необязательно)
В этом разделе описано, как запустить проект Xamarin iOS для устройств под управлением iOS. Пропустите этот раздел, если вы не работаете с устройствами iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

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

## <a name="next-steps"></a>Дополнительная информация
Вы можете узнать больше о push-уведомлениях.

* [Отправка push-уведомлений из мобильных приложений Azure](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Удаленные уведомления с помощью Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
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
