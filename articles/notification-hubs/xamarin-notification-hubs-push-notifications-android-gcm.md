---
title: Приступая к работе с Центрами уведомлений для приложений Xamarin.Android | Документация Майкрософт
description: Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android.
author: jwhitedev
manager: kpiteira
editor: ''
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7fee7813bbdcf902d5f5ae2d0af7540c8899ad25
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Начало работы с Центрами уведомлений для приложений Xamarin.Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android. Вы создадите пустое приложение Xamarin.Android, которое получает push-уведомления с помощью Firebase Cloud Messaging. По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

В этом учебнике описывается простой сценарий вещания с использованием Центров уведомлений.

## <a name="before-you-begin"></a>Перед началом работы
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Полный код для этого руководства можно найти на портале GitHub [здесь][GitHub].

## <a name="prerequisites"></a>предварительным требованиям
Для работы с данным учебником требуется следующее:

* [Visual Studio с Xamarin] на компьютере Windows или [Visual Studio для Mac] на компьютере OS X.
* Активная учетная запись Google

Для прохождения других учебников, посвященных Центрам уведомлений для приложений Xamarin.Android, необходимо сначала выполнить действия, описанные в этом учебнике.

> [!IMPORTANT]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Включение Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Настройка концентратора уведомлений
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Выберите расположенную сверху вкладку <b>Настройка</b>, введите значение <b>Ключ API</b>, полученное в предыдущем разделе, а затем выберите <b>Сохранить</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Концентратор уведомлений настроен для работы с GCM. Также у вас есть строки подключения, с помощью которых вы можете зарегистрировать приложение для получения уведомлений и отправки push-уведомлений.

## <a name="connect-your-app-to-the-notification-hub"></a>Подключение приложения к центру уведомлений
Сначала создайте проект. 

1. В Visual Studio выберите **Новое решение** > **Приложение Android**, а затем нажмите кнопку **Далее**.
   
      ![Visual Studio — создание нового проекта Android][22]

2. Введите значения в поля **App Name** (Имя приложения) и **Identifier** (Идентификатор). В разделе **Целевые платформы** выберите поддерживаемые платформы и нажмите кнопки **Далее** и **Создать**.
   
      ![Visual Studio — конфигурация приложения Android][23]

    Будет создан проект Android.

3. Откройте свойства проекта, щелкнув правой кнопкой мыши новый проект в представлении Solution (Решение) и выбрав пункт **Options**(Параметры). В разделе **Build** (Сборка) выберите **Android Application** (Приложение Android).
   
    Убедитесь, что **Имя пакета** начинается со строчной буквы.
   
   > [!IMPORTANT]
   > Первая буква имени пакета должна быть строчной. В противном случае возникнут ошибки манифеста приложения при регистрации приложения для push-уведомлений ниже.
   > 
   > 
   
      ![Visual Studio — параметры проекта Android][24]
4. Или выберите другой уровень API для параметра **Minimum Android version** (Минимальная версия Android).
5. Или для параметра **Целевая версия Android** выберите другую версию API, которую нужно использовать в качестве целевой (используйте API уровня 8 или выше).
6. Нажмите кнопку **ОК** и закройте диалоговое окно параметров проекта.

### <a name="add-the-required-packages-to-your-project"></a>Добавление обязательных пакетов в проект

1. Щелкните проект правой кнопкой мыши и выберите **Добавить** > **Добавить пакеты NuGet**.
2. Найдите пакет **Xamarin.Azure.NotificationHubs.Android** и добавьте его в проект.
3. Найдите пакет **Xamarin.Firebase.Messaging** и добавьте его в проект.

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
   * **Имя концентратора** — это имя вашего концентратора на [портале Azure]. Например, *mynotificationhub2*.
     
2. Создайте класс **Constants.cs** для вашего проекта Xamarin и определите в классе следующие постоянные значения. Замените значения заполнителей на собственные значения.
    
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

6. Щелкните правой кнопкой мыши проект и добавьте файл `google-services.json`, скачанный из проекта Firebase ранее. Щелкните правой кнопкой мыши добавленный файл и задайте действие сборки для `GoogleServicesJson`

    ![Visual Studio — настройка google-services.json][25]

7. Создайте новый класс с именем **MyFirebaseIIDService**.

8. Добавьте следующие операторы using в класс **MyFirebaseIIDService.cs**:
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. Добавьте следующий код в класс **MyFirebaseIIDService.cs** перед объявлением **class** и настройте наследование класса из **FirebaseInstanceIdService**:
   
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
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
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

15. Запустите приложение на устройстве или в загруженном эмуляторе.

## <a name="send-notifications-from-the-portal"></a>Отправка уведомлений с портала
Используя параметр *Тестовая отправка* на [портале Azure], вы можете проверить, получает ли приложение уведомления. Этот параметр позволяет отправить на устройство тестовое push-уведомление.

![Портал Azure — тестовая отправка][30]

Push-уведомления обычно отправляются в серверной службе, например в мобильной службе или ASP.NET, с помощью совместимой библиотеки. Ели для серверной части библиотека недоступна, для отправки уведомлений также можно напрямую использовать REST API.

Ниже приведен список других учебников, касающихся отправки уведомлений.

* ASP.NET: см. статью [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET].
* Пакет SDK для Центров уведомлений Azure для Java: сведения об отправке уведомлений Java см. в статье [Использование концентраторов уведомлений из Java](notification-hubs-java-push-notification-tutorial.md). Было протестировано в Eclipse для разработки для Android.
* PHP: см. статью [Использование центров уведомлений в PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>Дополнительная информация
В этом простом примере осуществляется широковещательная рассылка уведомлений на все устройства Android. Инструкции по их отправке определенным целевым пользователям, см. в руководстве [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании центров уведомлений см. в статьях [Общие сведения о концентраторах уведомлений] и [Руководство по использованию центров уведомлений Azure с приложениями Android].

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
[Общие сведения о концентраторах уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Руководство по использованию центров уведомлений Azure с приложениями Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: /manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
