<properties urlDisplayName="Get Started" pageTitle="Начало работы с концентраторами уведомлений Azure "metaKeywords ="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />
# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

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

+ Пакет Android SDK (предполагается использование Eclipse), который можно загрузить  <a href="http://go.microsoft.com/fwlink/?LinkId=389797">отсюда</a>
+  [пакет Android SDK для мобильных служб]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Android. 

<div class="dev-callout"><strong>Примечание</strong> <p>Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

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

1. В ADT Eclipse создайте новый проект Android (пункты меню - "Файл", "Создать", "Приложение Android").

   	![][13]

Убедитесь, что параметр 2. Минимальная требуемая версия SDK** задан как ***API 8: Android 2.2 (Froyo)* и что следующие две записи SDK заданы как последняя доступная версия. Нажмите кнопку "Next" (Далее) и следуйте указаниям мастера, убедившись, что выбран параметр **Create activity** (Создать действие) для создания нового действия. Подтвердите значок запуска по умолчанию в следующем окне и нажмите кнопку **Готово** в последнем окне.

   	![][14]

###Добавление служб Google Play в проект

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

1. Загрузите пакет SDK Android для концентраторов уведомлений  <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">отсюда</a>. Извлеките ZIP-файл и скопируйте файл notificationhubs\notification-hubs-0.1.jar в каталог \libs проекта в обозревателе пакетов.

2. Загрузите и распакуйте [Android SDK мобильных служб], откройте папку **notifications**, скопируйте файл **notifications-1.0.1.jar** в папку *libs* своего проекта Eclipse и обновите папку *libs*.

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
    <p>Обязательно установите для значения SENDER_ID номер проекта, полученный ранее.</p>
    </div> 

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. Добавьте в метод **OnCreate** следующий код и обязательно замените заполнители своей строкой подключения с доступом к прослушиванию, полученной в предыдущем разделе, и именем концентратора уведомлений, отображаемым в верхней части страницы в Azure для вашего концентратора (**не** полный URL-адрес).

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


9. В обозревателе пакетов щелкните правой кнопкой мыши пакет (под узлом "src"), выберите **Создать** и **Класс**.

10. В поле **Имя** введите `MyHandler`, в поле **Суперкласс** введите `com.microsoft.windowsazure.notifications.NotificationsHandler` и нажмите кнопку **Готово**

	![][6]

	При этом создается новый класс MyHandler.

11. Добавьте следующие инструкции импорта:

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

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Примеры отправки уведомлений с сервера мобильных служб Azure, интегрированного с интегрированными концентраторами уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([Сервер .NET](/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/) | [Сервер JavaScript](/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)).  Примеры отправки уведомлений с помощью API REST см. в статье **Использование концентраторов уведомлений из Java/PHP** ([Java](/ru-ru/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/ru-ru/documentation/articles/notification-hubs-php-backend-how-to/)).

1. В Visual Studio из меню **Файл** последовательно выберите **Создать** и **Проект...**, затем в области **Visual C#** щелкните **Windows** и **Консольные приложения**, после чего нажмите кнопку **OK**.  

   	![][20]

	Будет создан новый проект консольного приложения.

2. В меню **Инструменты** щелкните **Диспетчер пакетов библиотеки** и далее **Консоль диспетчера пакетов**. 

	Отобразится консоль диспетчера пакетов.

3. В окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus
    
	Будет добавлена ссылка на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. 

4. Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

5. Добавьте следующий метод в класс **Program**:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	Обязательно замените заполнитель "имя концентратора" на имя концентратора уведомлений, отображаемое на вкладке **Концентраторы уведомлений** портала. Замените разделитель строки подключения строкой с именем **DefaultFullSharedAccessSignature**, полученной в разделе "Настройка концентратора уведомлений". 

	>[WACOM.NOTE]Обязательно используйте строку подключения с **полным** доступом, а не с доступом к **прослушиванию**. У строки с доступом к прослушиванию нет прав на отправку уведомлений.

5. Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Тестирование приложения

Перед тестированием приложения на эмуляторе нужно выполнить следующие действия по его настройке (пропустите их, если будете тестировать на физическом устройстве).

1. Убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

2. В поле **Окно** щелкните **Диспетчер виртуальных устройств Android**, выберите ваше устройство и нажмите кнопку **Изменить**.

   	![][18]

3. Выберите **API-интерфейсы Google** в разделе **Цель**, а затем нажмите кнопку **OK**.

   	![][19]

4. 	Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите <strong>Параметры</strong> и щелкните <strong>Добавить учетную запись</strong>). Кроме того, убедитесь, что эмулятор подключен к Интернету.


Следуйте дальнейшим инструкциям, чтобы запустить приложение на устройстве или в эмуляторе.

1. В верхней панели инструментов Eclipse нажмите кнопку **Запуск** и выберите приложение. 
 
	Это запустит эмулятор (если используется эмулятор), загрузит и запустит приложение. Приложение получает значение *registrationId* из GCM и регистрируется в концентраторе уведомлений.

3. Нажмите в Visual Studio клавишу F5, чтобы запустить консольное приложение. 

	На ваше приложение будет отправлено уведомление.  
 
5. Когда в области уведомлений (верхний левый угол) появится значок, опустите панель уведомлений, чтобы просмотреть его.  

   	![][21]

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Android. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений].


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
[Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
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
[Руководства по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet

