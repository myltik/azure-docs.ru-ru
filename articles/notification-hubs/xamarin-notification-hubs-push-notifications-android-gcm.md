---
title: Отправка push-уведомлений в приложения Xamarin.Android с помощью Центров уведомлений Azure | Документация Майкрософт
description: Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776718"
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Руководство по отправке push-уведомлений в приложения Xamarin.Android с помощью Центров уведомлений Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android. Вы создадите пустое приложение Xamarin.Android, которое получает push-уведомления с помощью Firebase Cloud Messaging. Вы сможете рассылать через Центр уведомлений push-уведомления на все устройства, где запущено ваше приложение. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * создадите проект Firebase и включите Firebase Cloud Messaging;
> * создадите центр уведомлений;
> * создадите приложение Xamarin.Android и подключите его к центру уведомлений;
> * отправите тестовые уведомления с портала Azure.

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure**. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
- [Visual Studio с Xamarin] на компьютере Windows или [Visual Studio для Mac] на компьютере OS X.
- Активная учетная запись Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>создадите проект Firebase и включите Firebase Cloud Messaging;
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Создание концентратора уведомлений
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Настройка параметров GCM для центра уведомлений

1. Выберите **Google (GCM)** в разделе **параметров уведомления**. 
2. Введите **прежний ключ сервера**, который вы записали в консоли Google Firebase. 
3. На панели инструментов щелкните **Сохранить**. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Концентратор уведомлений настроен для работы с GCM. Также у вас есть строки подключения, с помощью которых вы можете зарегистрировать приложение для получения уведомлений и отправки push-уведомлений.

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>Создание приложения Xamarin.Android и его подключение к центру уведомлений

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Создание проекта Visual Studio и добавление пакетов NuGet
1. В Visual Studio откройте меню **Файл** и выберите **Создать**, а затем — **Проект**. 
   
      ![Visual Studio — создание нового проекта Android](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. В окне **обозревателя решений** разверните раздел **Свойства** и выберите **AndroidManifest.xml**. Обновите имя пакета в соответствии с именем пакета, которое вы указали при добавлении Firebase Cloud Messaging в проект в консоли Google Firebase. 

      ![Имя пакета в GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. 
4. Выберите вкладку **обзора**. Найдите **Xamarin.GooglePlayServices.Base**. Выберите **Xamarin.GooglePlayServices.Base** в списке результатов. Щелкните **Установить**. 

      ![Пакет NuGet для сервисов Google Play](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. В окне **диспетчера пакетов NuGet** выполните поиск пакета **Xamarin.Firebase.Messaging**. В списке результатов выберите **Xamarin.Firebase.Messaging**. Щелкните **Установить**. 
6. Затем найдите пакет **Xamarin.Azure.NotificationHubs.Android**. В списке результатов выберите **Xamarin.Azure.NotificationHubs.Android**. Щелкните **Установить**. 

### <a name="add-the-google-services-json-file"></a>Добавление JSON-файла сервисов Google

1. Скопируйте файл **google-services.json**, который вы загрузили с консоли Google Firebase в папку проекта.
2. Добавьте в этот проект файл **google-services.json**.
3. В окне **обозревателя решений** выберите файл **google-services.json**.
4. В области **свойств** для действия сборки выберите **GoogleServicesJson**. Если вы не видите **GoogleServicesJson**, закройте Visual Studio, перезапустите ее, затем повторно откройте проект и повторите попытку. 

      ![Действие сборки GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Настройка центров уведомлений в проекте

#### <a name="registering-with-firebase-cloud-messaging"></a>Регистрация в Firebase Cloud Messaging

Откройте файл **AndroidManifest.xml** и вставьте следующие элементы `<receiver>` в элемент `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Подготовьте следующую информацию для вашего приложения Android и концентратора уведомлений.
   
   * **Прослушивание строки подключения**. На [портале Azure] на панели мониторинга выберите **Просмотреть строки подключения**. Для этого значения скопируйте строку подключения *DefaultListenSharedAccessSignature*.
   * **Имя центра** — это имя вашего центра на [портале Azure]. Например, *mynotificationhub2*.
     
2. В окне **обозревателя решений** щелкните **проект** правой кнопкой мыши, выберите **Добавить**, а затем — **Класс**. 
4. Создайте класс **Constants.cs** для вашего проекта Xamarin и определите в классе следующие постоянные значения. Замените значения заполнителей на собственные значения.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. Добавьте следующие элементы, используя операторы **MainActivity.cs**:
   
    ```csharp
        using Android.Util;
    ```
4. Добавьте переменную экземпляра в класс **MainActivity.cs**, чтобы отобразить диалоговое окно оповещения при выполнении приложения:
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. Добавьте следующий код в `OnCreate` в классе **MainActivity.cs** после `base.OnCreate(savedInstanceState)`:

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```
7. Создайте класс **MyFirebaseIIDService**, как вы создали класс **констант**. 
8. Добавьте следующие операторы using в класс **MyFirebaseIIDService.cs**:
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. Добавьте следующее объявление **class** в класс **MyFirebaseIIDService.cs** и настройте наследование класса из **FirebaseInstanceIdService**:
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. В класс **MyFirebaseIIDService.cs** добавьте следующий код:
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
11. Создайте еще один класс в проекте и назовите его **MyFirebaseMessagingService**.
12. Добавьте следующие операторы using в класс **MyFirebaseMessagingService.cs**.
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. Добавьте следующий код в класс перед объявлением class и настройте наследование класса из **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. Добавьте следующий код в **MyFirebaseMessagingService.cs**:
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **Выполните сборку** проекта. 
16. **Запустите** приложение на устройстве или в загруженном эмуляторе.

## <a name="send-test-notification-from-the-azure-portal"></a>Отправка тестового уведомления с портала Azure
Можно проверить, поступают ли в приложение уведомления, с помощью параметра *Тестовая отправка* на [портале Azure]. Этот параметр позволяет отправить на устройство тестовое push-уведомление.

![Портал Azure — тестовая отправка](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Push-уведомления обычно отправляются в серверной службе, например в мобильной службе или ASP.NET, с помощью совместимой библиотеки. Если для серверной части библиотека недоступна, для отправки уведомлений также можно напрямую использовать REST API.

## <a name="next-steps"></a>Дополнительная информация
В рамках этого руководства вы отправили широковещательные уведомления на все устройства Android, зарегистрированные в серверной части. Чтобы узнать, как отправлять push-уведомления на конкретные устройства Android, перейдите к следующему руководству: 

> [!div class="nextstepaction"]
>[Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio с Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio для Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[портале Azure]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
