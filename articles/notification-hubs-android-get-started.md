<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Приступая к работе" pageTitle="Приступая к работе с концентраторами уведомлений Azure" metaKeywords="" description="Узнайте, как использовать концентраторы уведомлений Azure для работы с push-уведомлениями." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Приступая к работе с концентраторами уведомлений" authors="ricksal" solutions="" manager="dwrede" editor="" />
# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение на платформе Android. 
В этом учебнике вы создаете пустое приложение Android, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

* [Включение Google Cloud Messaging](#register)
* [Настройка концентратора уведомлений](#configure-hub)
* [Подключение приложения к концентратору уведомлений](#connecting-app)
* [Отправка уведомлений в приложение](#send)
* [Тестирование приложения](#run-app)

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств. 

Для работы с данным учебником требуется следующее:

+ Пакет Android SDK (предполагается использование Eclipse), который можно загрузить <a href="http://go.microsoft.com/fwlink/?LinkId=389797">здесь</a>
+ [пакет Android SDK для мобильных служб]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Android. 

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

##<a id="register"></a>Включение Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Далее вы будете использовать это значение ключа API, чтобы дать концентратору уведомлений возможность выполнять проверку подлинности с использованием GCM и отправлять push-уведомления от имени вашего приложения.

##<a id="configure-hub"></a>Настройка концентратора уведомлений

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

7. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Просмотреть строку подключения**. Запишите значения двух строк подключения.


Концентратор уведомлений теперь настроен для работы с GCM, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять push-уведомления.

##<a id="connecting-app"></a>Подключение приложения к концентратору уведомлений

###Создание нового проекта Android

1. В ADT Eclipse создайте новый проект Android (File (Файл), New (Создать), Android Application (Приложение Android Application)).

   	![][13]

2. Убедитесь, что для параметра **Minimum Required SDK** (Минимальная версия SDK) задано значение *API 8: Android 2.2 (Froyo)*, а для двух следующих записей SDK задана самая новая доступная версия. Нажмите кнопку "Next" (Далее) и следуйте указаниям мастера, убедившись, что выбран параметр **Create activity** (Создать действие) для создания нового действия. Подтвердите значок запуска по умолчанию в следующем окне и нажмите кнопку **Finish** (Готово) в последнем окне.

   	![][14]

###Добавление служб Google Play в проект

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

1. Загрузите пакет SDK Android для концентраторов уведомлений <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">здесь</a>. Извлеките ZIP-файл и скопируйте файл notificationhubs\notification-hubs-0.1.jar в каталог \libs проекта в обозревателе пакетов.

2. Загрузите и распакуйте [пакет Android SDK для мобильных служб], откройте папку **notifications**, скопируйте файл **notifications-1.0.1.jar** в папку *libs* вашего проекта Eclipse и обновите папку *libs*.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Числа в конце этого имени файла могут меняться в последующих выпусках SDK.</p>
    </div>

	Теперь настройте приложение для получения *registrationId* из GCM и используйте его, чтобы зарегистрировать экземпляр приложения в концентраторе уведомлений.

3. Добавьте следующую строку в файл AndroidManifest.xml прямо под элементом <uses-sdk/>. Обязательно замените `<your package>` на пакет, выбранный для приложения на шаге 1 (в данном примере это `com.yourCompany.wams_notificationhubs`).

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. В **MainActivity** добавьте следующие операторы:

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. В верхней части класса добавьте следующие частные члены.

	<div class="dev-callout"><b>Примечание.</b>
    <p>Обязательно установите для SENDER_ID полученное ранее значение номера проекта.</p>
    </div> 

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. Добавьте в метод **OnCreate** следующий код и обязательно замените заполнители на свою строку подключения с доступом к прослушиванию, полученным в предыдущем разделе, и именем концентратора уведомлений, отображаемым в верхней части страницы в Azure для вашего концентратора (**не** полный URL-адрес).

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<your listen access connection string>";
		hub = new NotificationHub("<your notification hub name>", connectionString, this);
		
		registerWithNotificationHubs();

7. В MainActivity.java создайте следующий метод:

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

8. Поскольку Android не отображает уведомления, необходимо создать собственный приемник. В **AndroidManifest.xml** добавьте следующий элемент внутрь элемента `<application/>`.

	<div class="dev-callout"><b>Примечание.</b>
    <p>Замените заполнитель на имя своего пакета.</p>
    </div> 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. В обозревателе пакетов щелкните правой кнопкой мыши пакет (под узлом `src`), выберите **Создать**, а затем **Класс**.

10. В поле **Имя** введите `MyHandler`, в поле **Суперкласс** введите `com.microsoft.windowsazure.notifications.NotificationsHandler` и нажмите кнопку **Готово**

	![][6]

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
	

##<a name="send"></a>Отправка уведомления в приложение

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента, использующего <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейс REST</a>. В этом учебнике показано два способа отправки уведомлений: с помощью консольного приложения .NET и с помощью мобильных служб с использованием скрипта узла.

###Порядок отправки уведомлений с помощью консольного приложения .NET:

1. Создайте новое консольное приложение Visual C#: 

   	![][20]

2. Добавьте ссылку на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Консоль диспетчера пакетов** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.ServiceBus

    и нажмите клавишу ВВОД.

3. Откройте файл Program.cs и добавьте следующий оператор "using":

        using Microsoft.ServiceBus.Notifications;

4. Добавьте в класс `Program` следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5. Затем добавьте следующие строки в метод Main:

         SendNotificationAsync();
		 Console.ReadLine();


###Порядок отправки уведомления с помощью мобильной службы

1. Выполните вход на [портал управления Azure] и выберите мобильную службу. Если у вас еще нет мобильной службы, следуйте инструкциям из раздела [Приступая к работе с мобильными службами].

2. Откройте расположенную сверху вкладку **Планировщик**.

   	![][22]

3. Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.

   	![][23]

4. После создания задания щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.

5. Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители на имя концентратора уведомлений и строку подключения для элемента *DefaultFullSharedAccessSignature*, полученного ранее. Щелкните **Сохранить**.

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


##<a name="run-app"></a>Тестирование приложения

Для тестирования работы этого приложения на реальном телефоне просто подключите его к компьютеру с помощью кабеля USB.

Порядок тестирования этого приложения с помощью эмулятора:

1. Убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

2. В поле **Окно** щелкните **Диспетчер виртуальных устройств Android**, выберите ваше устройство и нажмите кнопку **Изменить**.

   	![][18]

3. Выберите **API-интерфейсы Google** в поле **Цель**, а затем нажмите кнопку **ОК**.

   	![][19]

4. 	Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите <strong>Параметры</strong> и <strong>Добавить учетную запись</strong>). Кроме того, убедитесь, что эмулятор подключен к Интернету.



Независимо от выбранного устройства выполните следующие действия:

1. В верхней панели инструментов Eclipse нажмите кнопку **Запуск** и выберите приложение. При этом ваше приложение загружается на подключенный телефон, либо запускается эмулятор, который загружает и запускает это приложение.

2. Приложение получает значение *registrationId* из GCM и регистрируется в концентраторе уведомлений.

3. Теперь воспользуйтесь один из методов из предыдущего раздела для отправки уведомления в свое приложение.  

	- При использовании консольного приложения .Net нажмите клавишу F5 в Visual Studio, чтобы запустить приложение, которое будет отправлять уведомление. 
	- Если же вы используете скрипт мобильных служб, щелкните элемент **Запустить один раз** в нижней строке экрана мобильных служб, после чего скрипт отправляет уведомление.
 
5. В области уведомлений (в левом верхнем углу) отображается значок. Раскройте область уведомления для просмотра самого уведомления.  

   	![][21]

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Android. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в разделах [Руководство по использованию концентраторов уведомлений] и [Инструкции по использованию концентраторов уведомлений для Android].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[пакет Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-android
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Ссылки на проект библиотеки]: http://go.microsoft.com/fwlink/?LinkId=389800
[Портал управления Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для Android]: http://msdn.microsoft.com/ru-ru/library/dn282661.aspx

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet


