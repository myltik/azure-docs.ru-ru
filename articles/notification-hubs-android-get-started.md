<properties 
	pageTitle="Начало работы с концентраторами уведомлений Azure" 
	description="Узнайте, как использовать центры уведомлений Azure для push-уведомлений." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/15/2014" 
	ms.author="ricksal"/>

# Начало работы с концентраторами уведомлений

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

В этом разделе показано, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение на платформе Android. 
В этом учебнике вы создаете пустое приложение Android, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

* [Включение Google Cloud Messaging](#register)
* [Настройка центра уведомлений](#configure-hub)
* [Подключение приложения к концентратору уведомлений](#connecting-app)
* [Отправка уведомлений в приложение](#send)
* [Тестирование приложения](#run-app)

В этом учебнике описывается простой сценарий вещания с использованием центров уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании центров уведомлений для охвата определенных пользователей и групп устройств. 

Для работы с данным учебником требуется следующее:

+ пакет Android SDK (предполагается, что вы будете использовать), который можно загрузить <a href="http://go.microsoft.com/fwlink/?LinkId=389797">отсюда</a>
+ [пакет Android SDK для мобильных служб]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными центрам уведомлений для приложений Android. 

> [AZURE.NOTE] Для работы с этим учебником требуется активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатное использование Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).

##<a id="register"></a>Включение Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure-hub"></a>Настройка концентратора уведомлений

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>Подключение приложения к концентратору уведомлений

###Создание нового проекта Android

1. В ADT Eclipse создайте новый проект Android (пункты меню - "Файл", "Создать", "Приложение Android").

   	![][13]

2. Убедитесь, что в поле **Минимальная требуемая версия SDK** установлено значение *API 8: Android 2.2 (Froyo)* и что в следующих двух полях SDK указана последняя доступная версия. Нажмите кнопку "Next" (Далее) и следуйте указаниям мастера, убедившись, что выбран параметр **Create activity** (Создать действие) для создания нового действия. Подтвердите значок запуска по умолчанию в следующем окне и нажмите кнопку **Finish** (Готово) в последнем окне.

   	![][14]

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

1. Скачайте пакет SDK Android для центров уведомлений <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">отсюда</a>. Извлеките ZIP-файл и скопируйте файл notificationhubs\notification-hubs-0.1.jar в каталог \libs проекта в обозревателе пакетов.

2. Скачайте и распакуйте [пакет SDK для Android для мобильных служб], откройте папку **notifications**, скопируйте файл **notifications-1.0.1.jar** в папку *libs* проекта Eclipse и обновите папку *libs*.

    > [AZURE.NOTE] Числа в конце этого имени файла могут меняться в последующих выпусках SDK.

	Теперь настройте приложение на получение *registrationId* из GCM и используйте этот идентификатор для регистрации экземпляра приложения в концентраторе уведомлений.

3. Добавьте следующую строку в файл AndroidManifest.xml прямо под элементом <uses-sdk/>. Обязательно замените `<your package>` на пакет, который вы выбрали для приложения на шаге 1 (в данном примере это `com.yourCompany.wams_notificationhubs`).

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. В класс **MainActivity** добавьте следующие инструкции:

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. В верхней части класса добавьте следующие частные члены.

	> [AZURE.NOTE] Обязательно установите для значения SENDER_ID номер проекта, полученный ранее.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. В метод **OnCreate** добавьте следующий код и имя концентратора уведомлений, которое отображается в верхней части страницы службы Azure вашего концентратора (**не** полный URL-адрес). Обязательно замените заполнители на строку подключения с доступом прослушивания, полученную в предыдущем разделе.

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

8. Поскольку Android не отображает уведомлений, необходимо написать собственный приемник. В файле **AndroidManifest.xml** добавьте в середину элемента `<application/>` следующий элемент.

	> [AZURE.NOTE] Замените заполнитель на имя своего пакета.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. В обозревателе пакетов щелкните правой кнопкой мыши пакет (под узлом `src`), выберите пункт **Создать**, а затем - **Класс**.

10. В поле **Имя** введите значение `MyHandler`, в поле **Суперкласс** введите  `com.microsoft.windowsazure.notifications.NotificationsHandler`, затем нажмите кнопку **Готово**

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

>[AZURE.NOTE]Уведомления можно отправлять с помощью центров уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Пример отправки уведомлений с сервера мобильных служб Azure, интегрированного с концентраторами уведомлений, см. в статье [Начало работы с push-уведомлениями в мобильных службах](mobile-services-javascript-backend-android-get-started-push.md).  Пример отправки уведомлений с помощью интерфейсов REST API см. в статье [Использование концентратов уведомлений для Java](notification-hubs-java-backend-how-to.md) или [Использование концентратов уведомлений для PHP](notification-hubs-php-backend-how-to.md).

1. В Visual Studio в меню **Файл** выберите **Создать** и щелкните **Проект...**, затем в шаблоне **Visual C#** выберите **Windows** и **Консольное приложение** и нажмите **ОК**.  

   	![][20]

	Будет создан новый проект консольного приложения.

2. В меню **Инструменты** выберите **Диспетчер пакетов библиотеки**, а затем выберите **Консоль диспетчера пакетов**. 

	Отобразится консоль диспетчера пакетов.

3. В окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus
    
	После этого будет добавлена ссылка на пакет SDK для Azure Service Bus с помощью пакета NuGet <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus</a>. 

4. Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

5. В классе **Program** добавьте следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	Обязательно замените заполнитель "имя центра" именем центра уведомлений, отображаемым на вкладке **Центры уведомлений** портала: Замените заполнитель строки подключения строкой с именем **DefaultFullSharedAccessSignature**, полученной в разделе "Настройка центра уведомлений". 

	>[AZURE.NOTE]Обязательно используйте строку подключения с **полным** доступом, а не с доступом для **прослушивания**. У строки с доступом к прослушиванию нет прав на отправку уведомлений.

5. Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Тестирование приложения

Перед тестированием приложения на эмуляторе нужно выполнить следующие действия по его настройке (пропустите их, если будете тестировать на физическом устройстве).

1. Убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

2. В поле **Окно** щелкните **Диспетчер виртуальных устройств Android**, выберите ваше устройство и нажмите кнопку **Изменить**.

   	![][18]

3. Выберите **Google APIs** в поле **Цель** и нажмите кнопку **ОК**.

   	![][19]

4. 	Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите <strong>Параметры</strong> и щелкните <strong>Добавить учетную запись</strong>). Кроме того, убедитесь, что эмулятор подключен к Интернету.


Следуйте дальнейшим инструкциям, чтобы запустить приложение на устройстве или в эмуляторе.

1. В верхней панели инструментов Eclipse нажмите **Запустить**, а затем выберите свое приложение. 
 
	Это запустит эмулятор (если используется именно он), загрузит туда и запустит приложение. Приложение получает идентификатор *registrationId* из GCM и регистрируется в концентраторе уведомлений.

3. Нажмите в Visual Studio клавишу F5, чтобы запустить консольное приложение. 

	Вашему приложению будет отправлено уведомление.  
 
5. Когда в области уведомлений (верхний левый угол) появится значок, опустите панель уведомлений, чтобы просмотреть его.  

   	![][21]

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Android. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительную информацию об использовании концентраторов уведомлений см.в статье [Общие сведения о концентраторах уведомлений].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

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
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[пакет Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[пакет SDK для Android для мобильных служб]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Ссылки на проект библиотеки]: http://go.microsoft.com/fwlink/?LinkId=389800
[Портал управления Azure]: https://manage.windowsazure.com/
[Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Общие сведения о концентраторах уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: notification-hubs-aspnet-backend-android-notify-users.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-aspnet-backend-android-breaking-news.md


<!--HONumber=49-->