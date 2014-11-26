<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding">

[Windows Universal][Windows Universal][Windows Phone][Windows Phone][iOS][iOS][Android][Android][Kindle][Kindle][Xamarin.iOS][Xamarin.iOS][Xamarin.Android][Xamarin.Android]

</div>

В данном разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений Android-приложению.
В данном учебнике будет создано пустое Android-приложение, получающее push-уведомления с помощью технологии Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

-   [Включение Google Cloud Messaging][Включение Google Cloud Messaging]
-   [Настройка концентратора уведомлений][Настройка концентратора уведомлений]
-   [Подключение приложения к концентратору уведомлений][Подключение приложения к концентратору уведомлений]
-   [Отправка уведомлений в приложение][Отправка уведомлений в приложение]
-   [Тестирование приложения][Тестирование приложения]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств.

Для работы с данным учебником требуется следующее:

-   пакет Android SDK (предполагается, что будет использоваться среда разработки Eclipse), который можно загрузить [здесь][здесь];
-   [пакет Android SDK для мобильных служб][пакет Android SDK для мобильных служб].

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Android.

<div class="dev-callout">

**Примечание.**
Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

</div>

## <span id="register"></span></a>Включение Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Далее вы будете использовать это значение ключа API, чтобы дать концентратору уведомлений возможность выполнять проверку подлинности с использованием GCM и отправлять push-уведомления от имени вашего приложения.

## <span id="configure-hub"></span></a>Настройка концентратора уведомлений

1.  Выполните вход на [портал управления Azure][портал управления Azure] и щелкните по элементу **+СОЗДАТЬ** в нижней части экрана.

2.  Последовательно щелкните по элементам **Службы приложений**, **Шина обслуживания**, затем **Концентратор уведомлений** и **Быстрое создание**.

    ![][0]

3.  Введите имя для концентратора уведомлений, выберите желаемый регион и нажмите кнопку **Создать новый концентратор уведомлений**.

    ![][1]

4.  Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и щелкните расположенный сверху элемент **Настройка**.

    ![][2]

5.  Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

    ![][3]

6.  Перейдите на расположенную сверху вкладку **Настройка**, введите значение **Ключ API**, полученное в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

    ![][4]

7.  Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните по элементу **Просмотреть строку подключения**. Запишите значения двух строк подключения.

Концентратор уведомлений теперь настроен для работы с GCM, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять push-уведомления.

## <span id="connecting-app"></span></a>Подключение приложения к концентратору уведомлений

### Создание нового проекта Android

1.  В ADT Eclipse создайте новый проект Android (пункты меню — "Файл", "Создать", "Приложение Android").

    ![][5]

2.  Убедитесь, что параметр **Минимальная требуемая версия SDK** задан как *API 8: Android 2.2 (Froyo)* и что следующие две записи SDK заданы как последняя доступная версия. Нажмите кнопку "Next" (Далее) и следуйте указаниям мастера, убедившись, что выбран параметр **Create activity** (Создать действие) для создания нового действия. Подтвердите значок запуска по умолчанию в следующем окне и нажмите кнопку **Finish** (Готово) в последнем окне.

    ![][6]

### Добавление служб Google Play в проект

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Добавление кода

1.  Загрузите пакет Notification Hubs Android SDK [отсюда][пакет Android SDK для мобильных служб]. Извлеките ZIP-файл и скопируйте файл notificationhubs\\notification-hubs-0.1.jar в каталог \\libs проекта в обозревателе пакетов.

2.  Загрузите и распакуйте [Android SDK мобильных служб][пакет Android SDK для мобильных служб], откройте папку **notifications**, скопируйте файл **notifications-1.0.1.jar** в папку *libs* вашего проекта Eclipse и обновите папку *libs*.

    <div class="dev-callout">

    **Примечание.**
    Числа в конце этого имени файла могут меняться в последующих выпусках SDK.

    </div>

    Теперь настройте приложение для получения *registrationId* из GCM и используйте его, чтобы зарегистрировать экземпляр приложения в концентраторе уведомлений.

3.  Добавьте следующую строку в файл AndroidManifest.xml прямо под элементом <uses-sdk></uses-sdk>. Обязательно замените `<your package>` на пакет, выбранный для приложения на шаге 1 (в данном примере это `com.yourCompany.wams_notificationhubs`).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  В классе **MainActivity** добавьте следующие операторы.

        import android.os.AsyncTask;    
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.messaging.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  В верхней части класса добавьте следующие частные члены.

    <div class="dev-callout">

    **Примечание.**
    Обязательно установите для значения SENDER\_ID номер проекта, полученный ранее.

    </div>

        private String SENDER_ID = "<your project number>";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;

6.  Добавьте в метод **OnCreate** следующий код и обязательно подставьте свою строку подключения с доступом к прослушиванию, полученным в предыдущем разделе, и именем концентратора уведомлений, отображаемым в верхней части страницы в Azure для вашего концентратора (а **не** полный URL-адрес).

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

        gcm = GoogleCloudMessaging.getInstance(this);

        String connectionString = "<your listen access connection string>";
        hub = new NotificationHub("<your notification hub name>", connectionString, this);

        registerWithNotificationHubs();

7.  В MainActivity.java создайте следующий метод:

        @SuppressWarnings("unchecked")
        private void registerWithNotificationHubs() {
           new AsyncTask() {
              @Override
              protected Object doInBackground(Object... params) {
                 try {
                    String regid = gcm.register(SENDER_ID);
                    hub.register(regid);
                 } catch (Exception e) {
                    return e;
                 }
                 return null;
             }
           }.execute(null, null, null);
        }

8.  Поскольку Android не отображает уведомлений, необходимо написать собственный приемник. В **AndroidManifest.xml** добавьте следующий элемент внутрь элемента `<application/>`.

    <div class="dev-callout">

    **Примечание.**
    Замените заполнитель на имя своего пакета.

    </div>

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

9.  В обозревателе пакетов щелкните правой кнопкой мыши пакет (под узлом `src`), щелкните команду **Создать**, выберите **Класс**.

10. В поле **Имя** введите `MyHandler`, в поле **Суперкласс** укажите `com.microsoft.windowsazure.notifications.NotificationsHandler`, затем щелкните кнопку **Готово**

    ![][7]

    При этом создается новый класс MyHandler.

11. Добавьте следующие операторы импорта:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

12. Добавьте в класс следующий код:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;


        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("msg");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

## <a name="send"></a>Отправка уведомления в приложение

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью [интерфейса REST][интерфейса REST]. В данном учебнике уведомления будут отправляться с помощью консольного приложения .NET. Пример отправки уведомлений из серверного компонента мобильных служб Azure, интегрированого с концентраторами уведомлений, приведен в разделе **Приступая к работе с push-уведомлениями в мобильных службах** ([серверная часть .NET][серверная часть .NET] | [серверная часть JavaScript][серверная часть .NET]). Пример отправки уведомлений с помощью REST API-интерфейсов приведен в разделе **Использование концентраторов уведомлений в Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  В среде Visual Studio выберите в меню **Файл** команду **Создать** и затем **Проект...**, а потом в разделе **Visual C#** выберите **Windows** и **Консольное приложение**, после чего нажмите кнопку **OK**.

    ![][8]

    Это создаст новый проект консольного приложения.

2.  В меню **Сервис** выберите **Диспетчер пакетов библиотеки**, затем **Консоль диспетчера пакетов**.

    Это откроет консоль диспетчера пакетов.

3.  В окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus

    Это добавит ссылку на пакет Azure Service Bus SDK с помощью [пакета NuGet WindowsAzure.ServiceBus][пакета NuGet WindowsAzure.ServiceBus].

4.  Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

5.  В классе **Program** добавьте следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

    Обязательно замените заполнитель "имя концентратора" на имя концентратора уведомлений, отображаемое на портале на вкладке **Концентраторы уведомлений**. Также замените заполнитель строки подключения на строку под названием **DefaultFullSharedAccessSignature**, полученную в разделе "Настройка концентратора уведомлений".

    > [WACOM.NOTE]Обратите внимание, что эта строка подключения имеет **полный** доступ, а не доступ к **прослушиванию**. Доступ к прослушиванию не дает права отправлять уведомления.

6.  Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="run-app"></a>Тестирование приложения

Перед тестированием приложения на эмуляторе нужно выполнить следующие действия по его настройке (пропустите их, если будете тестировать на физическом устройстве).

1.  Убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

2.  В меню **Окно** щелкните по команде **Диспетчер виртуальных устройств Android**, выберите ваше устройство и нажмите кнопку **Изменить**.

    ![][9]

3.  Выберите **API-интерфейсы Google** в поле **Цель**, а затем нажмите кнопку **ОК**.

    ![][10]

4.  Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите **Параметры** и **Добавить учетную запись**). Кроме того, убедитесь, что эмулятор подключен к Интернету.

Следуйте дальнейшим инструкциям, чтобы запустить приложение на устройстве или в эмуляторе.

1.  На верхней панели инструментов Eclipse нажмите кнопку **Запуск** и выберите свое приложение.

    Это запустит эмулятор (если используется именно он), загрузит туда и запустит приложение. Приложение получает значение *registrationId* из GCM и регистрируется в концентраторе уведомлений.

2.  Нажмите в Visual Studio клавишу F5, чтобы запустить консольное приложение.

    Вашему приложению будет отправлено уведомление.

3.  Когда в области уведомлений (верхний левый угол) появится значок, опустите панель уведомлений, чтобы просмотреть его.

    ![][11]

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Android. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям][Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений][руководстве по использованию концентраторов уведомлений].

 

  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /ru-ru/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Включение Google Cloud Messaging]: #register
  [Настройка концентратора уведомлений]: #configure-hub
  [Подключение приложения к концентратору уведомлений]: #connecting-app
  [Отправка уведомлений в приложение]: #send
  [Тестирование приложения]: #run-app
  [здесь]: http://go.microsoft.com/fwlink/?LinkId=389797
  [пакет Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [1]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [2]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [3]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [4]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
  [5]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
  [6]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
  [7]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
  [интерфейса REST]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx
  [серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Java]: /ru-ru/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ru-ru/documentation/articles/notification-hubs-php-backend-how-to/
  [8]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
  [пакета NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [9]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [10]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [11]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
  [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
  [Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
  [руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
