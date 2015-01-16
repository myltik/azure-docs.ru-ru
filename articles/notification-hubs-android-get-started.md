<properties urlDisplayName="Get Started" pageTitle="Приступая к работе с центрами уведомлений Azure" metaKeywords="" description="Узнайте, как использовать центры уведомлений Azure для push-уведомлений." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="11/21/2014" ms.author="ricksal" />
# Приступая к работе с центрами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Универсальное приложение Windows</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение на платформе Android. 
В этом учебнике вы создаете пустое приложение Android, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

* [Включение Google Cloud Messaging](#register)
* [Настройка центра уведомлений](#configure-hub)
* [Подключение приложения к центру уведомлений](#connecting-app)
* [Отправка уведомлений в приложение](#send)
* [Тестирование приложения](#run-app)

В этом учебнике описывается простой сценарий вещания с использованием центров уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании центров уведомлений для охвата определенных пользователей и групп устройств. 

Для работы с данным учебником требуется следующее:

+ пакет Android для SDK (предполагается использование Eclipse), который можно скачать <a href="http://go.microsoft.com/fwlink/?LinkId=389797">здесь</a>
+ мыши [Пакет SDK для мобильных служб для Android]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Android. 

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительную информацию см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатное пробное использование Azure</a>.</p></div>

##<a id="register"></a>Включение Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Далее вы будете использовать это значение ключа API, чтобы дать центру уведомлений возможность выполнять проверку подлинности с использованием GCM и отправлять push-уведомления от имени вашего приложения.

##<a id="configure-hub"></a>Настройка центра уведомлений

1. Войдите на [портал управления Аzure] и нажмите элемент **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][7]

3. Введите имя центра уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][8]

4. Выберите недавно созданное пространство имен **обычно это *имя_центра_уведомлений*-ns**), и перейдите на расположенную сверху вкладку **Настройка**.

   	![][9]

5. Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный центр уведомлений.

   	![][10]

6. Перейдите на расположенную сверху вкладку **Настройка**, введите значение **Ключ API**, полученное в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

   	![][11]

7. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Просмотреть строку подключения**. Запишите значения двух строк подключения.


Концентратор уведомлений теперь настроен для работы с GCM, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять push-уведомления.

##<a id="connecting-app"></a>Подключение приложения к центру уведомлений

###Создание нового проекта Android

1. В ADT Eclipse создайте новый проект Android (пункты меню - "Файл", "Создать", "Приложение Android").

   	![][13]

2. Убедитесь, что для параметра **Minimum Required SDK** (Минимальная требуемая версия SDK) задано значение "API 8: Android 2.2 (Froyo)" и что следующие две записи SDK заданы как последняя доступная версия. Нажмите кнопку "Далее" и следуйте указаниям мастера, убедившись, что выбран параметр **Создать действие** для создания нового действия. Подтвердите значок запуска по умолчанию в следующем окне и нажмите кнопку **Готово** в последнем окне.

   	![][14]

###Добавление служб Google Play в проект

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

1. Скачайте пакет SDK для Android для центров уведомлений <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">здесь</a>. Извлеките ZIP-файл и скопируйте файл notificationhubs\notification-hubs-0.1.jar в каталог \libs проекта в обозревателе пакетов.

2. Скачайте и распакуйте [пакет SDK для Android для мобильных служб], откройте папку **notifications**, скопируйте файл **notifications-1.0.1.jar** в папку libs проекта Eclipse и обновите папку libs.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Числа в конце этого имени файла могут меняться в последующих выпусках SDK.</p>
    </div>

	Теперь настройте приложение для получения "registrationId" из GCM и используйте его, чтобы зарегистрировать экземпляр приложения в центре уведомлений.

3. Добавьте следующую строку в файл AndroidManifest.xml прямо под элементом <uses-sdk/>. Обязательно замените <your package> на пакет, выбранный для приложения на шаге 1 (в данном примере это "com.yourCompany.wams_notificationhubs").

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

6. Добавьте в метод **OnCreate** следующий код и обязательно замените заполнители на свою строку подключения с доступом к прослушиванию, полученным в предыдущем разделе, и именем центра уведомлений, отображаемым в верхней части страницы в Azure для вашего центра (**не** полный URL-адрес).

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

8. Поскольку Android не отображает уведомлений, необходимо написать собственный приемник. В **AndroidManifest.xml** добавьте следующий элемент внутрь элемента <application/>.

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


9. В обозревателе пакетов щелкните правой кнопкой мыши пакет (под узлом "src"), щелкните **Создать**, а затем - **Класс**.

10. В поле **Имя** введите MyHandler, в поле **Суперкласс** введите "com.microsoft.windowsazure.notifications.NotificationsHandler" и нажмите кнопку **Готово**

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

Уведомления можно отправлять с помощью центров уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Примеры отправки уведомлений с сервера мобильных служб Azure, интегрированного с центрами уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([Сервер .NET](/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/) | [Серверная служба JavaScript](/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)).  Примеры отправки уведомлений с помощью API REST см. в статье **Использование центров уведомлений из Java/PHP** ([Java](/ru-ru/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/ru-ru/documentation/articles/notification-hubs-php-backend-how-to/)).

1. В Visual Studio в меню **Файл** выберите **Создать**, **Проект...**, а затем в разделе **Visual C#** щелкните **Windows**, выберите **Консольное приложение** и нажмите кнопку **OК**.  

   	![][20]

	Будет создан новый проект консольного приложения.

2. В меню **Сервис** щелкните **Диспетчер пакетов библиотеки**, затем щелкните **Консоль диспетчера пакетов**. 

	Отобразится консоль диспетчера пакетов.

3. В окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus
    
	Будет добавлена ссылка на пакет SDK для Azure Service Bus с помощью пакета <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">NuGet WindowsAzure.ServiceBus</a>. 

4. Откройте файл Program.cs и добавьте следующий оператор "using":

        using Microsoft.ServiceBus.Notifications;

5. Добавьте в класс **Программа** следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	Обязательно замените заполнитель "имя центра" на имя центра уведомлений, отображаемое на вкладке **Концентраторы уведомлений** портала: Замените разделитель строки подключения строкой с именем **DefaultFullSharedAccessSignature**, полученной в разделе "Настройка центра уведомлений" 

	>[WACOM.NOTE]Обязательно используйте строку подключения с **полным** доступом, а не с доступом для **прослушивания**. У строки с доступом к прослушиванию нет прав на отправку уведомлений.

5. Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Тестирование приложения

Перед тестированием приложения на эмуляторе нужно выполнить следующие действия по его настройке (пропустите их, если будете тестировать на физическом устройстве).

1. Убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

2. В поле **Окно** щелкните **Диспетчер виртуальных устройств Android**, выберите свое устройство и нажмите кнопку **Изменить**.

   	![][18]

3. Выберите **Google APIs** (API-интерфейсы Google) в разделе **Target** (Цель), а затем нажмите кнопку **ОК**.

   	![][19]

4. 	Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите пункт <strong>Параметры</strong> и щелкните <strong>Добавление учетной записи</strong>). Кроме того, убедитесь, что эмулятор подключен к Интернету.


Следуйте дальнейшим инструкциям, чтобы запустить приложение на устройстве или в эмуляторе.

1. В верхней панели инструментов Eclipse нажмите кнопку **Запуск** и выберите приложение. 
 
	Это запустит эмулятор (если используется именно он), загрузит туда и запустит приложение. Приложение получит значение "registrationId" из GCM и зарегистрируется в центре уведомлений.

3. Нажмите в Visual Studio клавишу F5, чтобы запустить консольное приложение. 

	Вашему приложению будет отправлено уведомление.  
 
5. Когда в области уведомлений (верхний левый угол) появится значок, опустите панель уведомлений, чтобы просмотреть его.  

   	![][21]

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Android. Для охвата определенных пользователей см. учебник [Использование центров уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. статью [Использование центров уведомлений для передачи экстренных новостей]. Дополнительную информацию об использовании центров уведомлений см. в [руководстве по использованию центров уведомлений].


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
[Пакет SDK для мобильных служб для Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-android
[Авторизация пользователей с помощью сценариев]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Ссылки на проект библиотеки]: http://go.microsoft.com/fwlink/?LinkId=389800
[Портал управления Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководство по использованию центров уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx

[Использование центров уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование центров уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet


<!--HONumber=35.1-->
