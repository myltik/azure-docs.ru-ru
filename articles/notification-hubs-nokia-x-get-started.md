<properties linkid="develop-notificationhubs-tutorials-get-started-nokia-x" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="kirillg" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo"></tags>

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding">

[Windows Universal][Windows Universal][Windows Phone][Windows Phone][iOS][iOS][Android][Android][Kindle][Kindle][Nokia X][Nokia X][Xamarin.iOS][Xamarin.iOS][Xamarin.Android][Xamarin.Android]

</div>

В этом разделе рассказывается о том, как использовать **Концентраторы уведомлений Azure** для отправки push-уведомлений в приложение Android на **Nokia X**. В этом учебнике вы создадите пустое приложение Android, принимающее push-уведомления с использованием службы уведомлений Nokia. По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

-   [Настройка службы уведомлений Nokia][Настройка службы уведомлений Nokia]
-   [Настройка концентратора уведомлений][Настройка концентратора уведомлений]
-   [Подключение приложения к концентратору уведомлений][Подключение приложения к концентратору уведомлений]
-   [Отправка уведомления в приложение][Отправка уведомления в приложение]
-   [Тестирование приложения][Тестирование приложения]

<div class="dev-callout">

<b>Примечание.</b>

Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

</div>

Для работы с данным учебником требуется следующее:

1.  Среда разработки Nokia X, которую можно настроить в соответствии с инструкциями, представленными [здесь][здесь]. Обязательно установите специализированные пакеты для Nokia X и настройте эмулятор Nokia X, выполнив инструкции.
2.  Установка устройств Nokia X (необязательно), которую можно настроить в соответствии с инструкциями, представленными [здесь][1].
3.  Пакет Android SDK (предполагается использование Eclipse), который можно загрузить [здесь][2].
4.  Пакет SDK мобильных служб в Android, который можно загрузить [здесь][3]<a>.

## <span id="register"></span></a>Настройка службы уведомлений Nokia

1.  Войдите в [консоль разработчика API уведомлений Nokia][консоль разработчика API уведомлений Nokia]

2.  Откройте вкладку **Создать службы** и создайте новую службу, указав **Идентификатор отправителя** и **Описание службы**

    ![][]

3.  Запишите **Идентификатор отправителя** и **Ключ авторизации** после успешного создания службы.

4.  Чтобы отобразить все созданные службы с соответствующими **Идентификаторами отправителя** и **Ключами авторизации, откройте вкладку **Мои службы****

    ![][4]

5.  Подробные сведения см. [здесь][5].

## <span id="configure-hub"></span></a>Настройка концентратора уведомлений

1.  Войдите на [портал управления Azure][портал управления Azure] и щелкните элемент **+СОЗДАТЬ** в нижней части экрана.

2.  Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

    ![][6]

3.  Введите имя для концентратора уведомлений, выберите нужный регион и (необязательно) пространство имен, затем щелкните элемент **Создать новый концентратор уведомлений**.

    ![][7]

4.  Перейдите к только что созданному концентратору уведомлений. Откройте вкладку Serviсe Bus слева, щелкните пространство имен, в котором был создан концентратор уведомлений, после чего откройте вкладку "Концентраторы уведомлений".

    ![][8]

5.  Выбрав созданный концентратор уведомлений, щелкните вкладку **Настройка** вверху.

    ![][9]

6.  Прокрутите содержимое, найдите пункт **параметры уведомлений nokia x**, вставьте ключ авторизации, полученный от службы уведомлений Nokia, после чего нажмите кнопку **Сохранить** внизу страницы.

    ![][10]

7.  Выберите вкладку **Панель мониторинга** вверху и внизу страницы щелкните **Сведения о подключении**.

    ![][11]

8.  Запишите две строки подключения SAS для **DefaultListenSharedAccessSignature** и **DefaultFullSharedAccessSignature**, которые будут использоваться позже в учебнике.

## <span id="connect-hub"></span></a>Подключение приложения к концентратору уведомлений

### Создание нового проекта Android

1.  В ADT Eclipse создайте новый проект Android (File (Файл) -\> New (Создать) -\> Android Application (Приложение Android)).

2.  Убедитесь, что в поле "минимальная версия SDK" указано значение **API 16: Android 4.1 (Jelly Bean)**, и что для двух следующих записей SDK указана последняя доступная версия. Нажмите кнопку "Next" (Далее) и следуйте указаниям мастера, убедившись, что выбран параметр **Create activity** (Создать действие) для создания **нового действия**. Подтвердите значок запуска по умолчанию в следующем окне и нажмите кнопку **Finish** (Готово) в последнем окне.

    ![][12]

3.  Убедитесь, что правильно задано конечное значение сборки проекта (платформа = 4.1.2, уровень API = 16 для этого примера). Щелкните правой кнопкой на пункте "Проект", выберите "Свойства", после чего в диалоговом окне "Свойства проекта" выберите "Android".

    ![][13]

4.  Добавьте JAR-файл служб уведомления Nokia в проект. Эта вспомогательная библиотека уведомлений Nokia **push.jar** предоставляет API-интерфейс, аналогичный вспомогательной библиотеке GCM.
    Перейдите в пункт Свойства проекта -\> Путь сборки Java -\> Библиотеки -\> Добавить внешние JAR-файлы и выберите файл **push.jar**, находящийся в **\\extras\\nokia\\nokia\_x\_services\\libs\\nna\\push.jar**. Документация Javadoc для библиотеки находится в папке **\\extras\\nokia\\nokia\_x\_services\\javadocs\\nna**, где установлена интегрированная среда разработки Eclipse.

5.  Обязательно скопируйте эту библиотеку push.jar в папку \\libs вашего проекта в обозревателе решений.

6.  Загрузите пакет SDK Android для концентраторов уведомлений [здесь][3]. Извлеките ZIP-файл и скопируйте файл notificationhubs\\notification-hubs-0,3.jar в каталог \\libs проекта в обозревателе пакетов.

    <div class="dev-callout">

    <b>Примечание.</b>
    Числа в конце этого имени файла могут меняться в последующих выпусках SDK.

    </div>

### Добавление кода

1.  Откройте файл **AndroidManifest.xml** и замените элемент приложения следующими строками кода, обязательно подставив вместо `[YourPackageName]` имя вашего пакета, указанное при создании приложения для Android.

        <!-- Push Notifications connects to Internet services. -->
        <uses-permission android:name="android.permission.INTERNET" />

        <!-- Keeps the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

         <!--
         Creates a custom permission so only this app can receive its messages.

         NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
               where PACKAGE is the application's package name.
        -->
        <permission 
            android:name="[YourPackageName].permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />

        <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />

        <application
            android:allowBackup="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name="[YourPackageName].MainActivity"
                android:label="@string/app_name" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>

            <receiver
                android:name="com.nokia.push.PushBroadcastReceiver"
                android:permission="com.nokia.pushnotifications.permission.SEND" >
                <intent-filter>

                    <!-- Receives the actual messages. -->
                    <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
                    <!-- Receives the registration id. -->
                    <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
                    <category android:name="[YourPackageName]" />
                </intent-filter>
            </receiver>

            <service android:name="[YourPackageName].PushIntentService" />
        </application> 

2.  В обозревателе решений щелкните правой кнопкой мыши пакет (в узле `src`), выберите **Создать**, щелкните **Класс** и создайте новый класс с именем **ConfigurationSettings.java**
    ![][14]

    Затем добавьте в него следующий код, определяющий используемые в примере постоянные значения.

        public class ConfigurationSettings {
            public static String NotificationHubName = "";
            public static String NotificationHubConnectionString = "";
            public static String SenderId = "";
        }

    Введите в указанных выше константах **SenderId** вашей конфигурации из консоли push-уведомлений Nokia, а также **NotificationHubName** и **NotificationHubConnectionString** (DefaultListenSharedAccessSignature) из портала управления.

3.  В файле **MainActivity.java** добавьте следующее выражение импорта:

        import com.nokia.push.PushRegistrar;

    Затем в методе onCreate добавьте следующий код:

        // Make sure the device has the proper dependencies.
        PushRegistrar.checkDevice(this);

        // Make sure the manifest was properly set - comment out this line
        // while developing the app, then uncomment it when it's ready
        PushRegistrar.checkManifest(this);

        // Register the device with the Nokia Notification service
        PushRegistrar.register(this, ConfigurationSettings.SenderId);

4.  Добавьте еще один новые класс с именем **PushIntentService.java** и введите следующий код, который зарегистрирует концентратор уведомлений, а также будет обрабатывать сообщения, полученные от данного концентратора уведомлений.

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import android.util.Log;

        import com.microsoft.windowsazure.messaging.NotificationHub;
        import com.nokia.push.PushBaseIntentService;

        /**
         * IntentService responsible for handling push notification messages.
         */
        public class PushIntentService extends PushBaseIntentService {

            private NotificationManager mNotificationManager;
            private static NotificationHub hub;
            public static final int NOTIFICATION_ID = 1;
            private static final String TAG = "NokiaXPush/PushIntentService";

            /**
             * Constructor.
             */
            public PushIntentService() {
            }

            @Override
            protected String[] getSenderIds(Context context) {
                return new String[] { ConfigurationSettings.SenderId };
            }   

            @Override
            protected void onRegistered(Context context, String registrationId) {
                Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
                RegisterWithNotificationHub(context, registrationId);
            }

            public static void RegisterWithNotificationHub(Context context, String registrationId) {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName, 
                            ConfigurationSettings.NotificationHubConnectionString, 
                            context);
                }
                try {
                    hub.register(registrationId);
                    Log.i(TAG, "Registered with Notification Hub - '" 
                            + ConfigurationSettings.NotificationHubName + "'" 
                            + " with RegistrationID - '"
                            + registrationId + "'");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            protected void onMessage(Context context, Intent intent) {
                String message = intentExtrasToString(intent.getExtras());
                Log.i(TAG, "Received message. Extras: " + message);
                generateNotification(context, message);
            }

            /**
             * Extracts the key-value pairs from the given Intent extras and returns
             * them in a string.
             */
            private String intentExtrasToString(Bundle extras) {
                StringBuilder sb = new StringBuilder();
                sb.append("{ ");

                for (String key : extras.keySet()) {
                    sb.append(sb.length() <= 2 ? "" : ", ");
                    sb.append(key).append("=").append(extras.get(key));
                }

                sb.append(" }");
                return sb.toString();
            }

            /**
             * Issues a notification to inform the user that server has sent a message.
             */
            private void generateNotification(Context context, String message)
            {
                mNotificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);

                PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
                    new Intent(context, MainActivity.class), 0);

                NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(context)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle("Notification Hub Demo")
                    .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(message))
                    .setContentText(message);

                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }

            @Override
            protected void onError(Context arg0, String errorId) {
                Log.i(TAG, "Received error:  " + errorId);
            }

            @Override
            protected void onUnregistered(Context arg0, String errorId) {
                Log.i(TAG, "Received recoverable error: " + errorId);
            }
        }

## <a name="send"></a>Отправка уведомления в приложение

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента, использующего [интерфейс REST][интерфейс REST]. В этом учебнике демонстрируется использование консольного приложения .NET.

1.  Создайте новое консольное приложение Visual C#:

    ![][15]

2.  Добавьте ссылку на пакет Windows Azure Service Bus SDK с помощью [пакета NuGet WindowsAzure.ServiceBus][пакета NuGet WindowsAzure.ServiceBus]. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите следующее и нажмите Enter:

        Install-Package WindowsAzure.ServiceBus

3.  Откройте файл Program.cs и добавьте следующий оператор "using":

        using Microsoft.ServiceBus.Notifications;

4.  Добавьте в класс `Program` следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
            await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
        }

5.  Затем добавьте следующие строки в метод Main:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-app"></a>Тестирование приложения

Для тестирования работы этого приложения на реальном телефоне просто подключите его к компьютеру с помощью кабеля USB.

Порядок тестирования этого приложения с помощью эмулятора:

1.  В верхней панели инструментов Eclipse нажмите кнопку "Запуск" и выберите приложение.

2.  При этом ваше приложение загружается на подключенный телефон, либо запускается эмулятор, который загружает и запускает это приложение.

3.  Приложение получает значение registrationId из от службы уведомлений Nokia и регистрируется в концентраторе уведомлений.

    <div class="dev-callout">

    <b>Примечание.</b>
    <p>
    Если приложение Android успешно зарегистрировано в концентраторе уведомлений, в журналах "Eclipse Logcat" появится сообщение следующего содержания: 'Зарегистрировано в концентраторе уведомлений - '<yournotificationhubname>' с RegistrationID - '<registrationidreturnedbynokianotificationservice'' < p>

    </div>

4.  Чтобы отправить контрольное уведомление при использовании консольного приложения .Net, просто нажмите клавишу F5 в Visual Studio, чтобы запустить приложение. Данное приложение отправит уведомление, которое отобразится в верхней области уведомлений вашего устройства или эмулятора.

<!-- Images. -->
<!-- URLs. -->

  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /ru-ru/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Nokia X]: /ru-ru/documentation/articles/notification-hubs-nokia-x-get-started/ "Nokia X"
  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Настройка службы уведомлений Nokia]: #register
  [Настройка концентратора уведомлений]: #configure-hub
  [Подключение приложения концентратору уведомлений]: #connect-hub
  [Отправка уведомления в приложение]: #send
  [Тестирование приложения]: #test-app
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [здесь]: http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html
  [1]: http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html
  [2]: http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409
  [3]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [консоль разработчика API уведомлений Nokia]: https://console.push.nokia.com/ncm/Web/index.jsp
  []: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
  [4]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
  [5]: http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html
  [портал управления Azure]: https://manage.windowsazure.com/
  [6]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
  [7]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
  [8]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
  [9]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
  [10]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
  [11]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
  [12]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
  [13]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png
  [14]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
  [интерфейс REST]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx
  [15]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
  [пакета NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
