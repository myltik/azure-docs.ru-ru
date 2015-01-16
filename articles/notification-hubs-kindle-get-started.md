<properties urlDisplayName="Get Started" pageTitle="Начало работы с концентраторами уведомлений Azure "metaKeywords ="" description="Узнайте, как использовать центры уведомлений Azure для отправки push-уведомлений." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-kindle" ms.devlang="Java" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Универсальные приложения Windows</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle" class="current">Kindle</a><a href="/ru-ru/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение Kindle. 
В этом учебнике вы создаете пустое приложение Kindle, которое получает push-уведомления с помощью Google Cloud Messaging (GCM).

Для работы с данным учебником требуется следующее:

+ Пакет Android SDK (предполагается, что будет использоваться Eclipse) можно загрузить <a href="http://go.microsoft.com/fwlink/?LinkId=389797">отсюда</a>.
+ Чтобы настроить среду разработки для Kindle, выполните действия, указанные <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">здесь</a>.

##Добавление нового приложения на портал разработчика

1. Для начала создайте приложение на [портале разработчика].

	![][0]

2. Скопируйте **Ключ приложения**.

	![][1]

3. На портале щелкните название своего приложения, а затем перейдите на вкладку **Сообщения устройства**.

	![][2]

4. Щелкните **Создать новый профиль безопасности**, затем создайте профиль безопасности (например, **профиль безопасности TestAdm**). Затем щелкните **Сохранить**.

	![][3]

5. Щелкните "Профили безопасности" для просмотра только что созданного профиля. Скопируйте значения **Код клиента** и **Секрет клиента** для последующего использования.

	![][4]

## Создание ключа API

1. Откройте командную строку с привилегиями администратора.
2. Перейдите в папку пакета Android SDK.
3. Введите следующую команду:

    	keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

	![][5]

4.  В качестве пароля для **keystore** введите **android**.

5.  Скопируйте отпечаток **MD5**.
6.  Вернувшись на портал разработчика, на вкладке **Обмен сообщениями** щелкните **Android/Kindle**, введите имя пакета для своего приложения (например, **com.sample.notificationhubtest**) и значение **MD5**, а затем щелкните **Создать ключ API**.

## Добавление учетных данных для концентратора

На портале добавьте секрет клиента и код клиента на вкладку **Настройка** концентратора уведомлений.

## Настройка приложения

<div class="dev-callout"><b>Примечание.</b>
    <p>При создании приложения используйте API не ниже 17 уровня.</p>
    </div>

Добавьте библиотеки ADM в проект Eclipse.

1. Чтобы получить библиотеку ADM, [загрузите пакет SDK]. Распакуйте ZIP-файл пакета SDK.
2. В Eclipse щелкните правой кнопкой мыши проект и выберите **Свойства**. Слева выберите **Путь построения Java**, затем сверху выберите **Библиотеки  **. Щелкните **Добавить внешний JAR-файл** и выберите файл `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` из каталога, в который был распакован пакет Amazon SDK.
3. Загрузите пакет NotificationHubs Android SDK (ссылка).
4. Извлеките содержимое пакета, а затем перетащите файл `notification-hubs-sdk.jar` в папку `libs  `в Eclipse.

Изменение манифеста приложения для поддержки ADM

1. Добавьте пространство имен Amazon в корневом элементе манифеста.


		xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Добавьте разрешения в качестве первого элемента в элементе манифеста. Вместо **[YOUR PACKAGE NAME]** укажите пакет, который используется для создания приложения. 

		<permission
	     android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
	     android:protectionLevel="signature" />

		<uses-permission android:name="android.permission.INTERNET"/>

		<uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />
 
		<!-- This permission allows your app access to receive push notifications
		from ADM. -->
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />
 
		<!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
		<uses-permission android:name="android.permission.WAKE_LOCK" />

3. Вставьте приведенный ниже элемент в качестве первого потомка элемента приложения. Замените **[YOUR SERVICE NAME]** именем обработчика сообщений ADM, который будет создан в следующем разделе (включая пакет), а **[YOUR PACKAGE NAME]** именем пакета, с помощью которого создается приложение.

		<amazon:enable-feature
		      android:name="com.amazon.device.messaging"
		             android:required="true"/>
		<service
		    android:name="[YOUR SERVICE NAME]"
		    android:exported="false" />
		 
		<receiver
		    android:name="[YOUR SERVICE NAME]$Receiver"
		 
		    <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
		    android:permission="com.amazon.device.messaging.permission.SEND" >
		 
		    <!-- To interact with ADM, your app must listen for the following intents. -->
		    <intent-filter>
		  <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
		  <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
		 
		  <!-- Replace the name in the category tag with your app's package name. -->
		  <category android:name="[YOUR PACKAGE NAME]" />
		    </intent-filter>
		</receiver>

## Создание обработчика сообщений ADM

1. Создайте новый класс с наследованием из `com.amazon.device.messaging.ADMMessageHandlerBase` и назовите его `MyADMMessageHandler`, как показано на следующем рисунке:

	![][6]

2. Добавьте следующие инструкции `импорта`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.support.v4.app.NotificationCompat;
		import com.amazon.device.messaging.ADMMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub

3. Добавьте в созданный класс приведенный ниже код. Не забудьте подставить имя концентратора и строку подключения (listen).

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
      	private static NotificationHub hub;
		public static NotificationHub getNotificationHub(Context context) {
			Log.v("com.wa.hellokindlefire", "getNotificationHub");
			if (hub == null) {
				hub = new NotificationHub("[hub name]", "[listen connection string]", context);
			}
			return hub;
		}

		public MyADMMessageHandler() {
				super("MyADMMessageHandler");
			}
	
			public static class Receiver extends ADMMessageReceiver
    		{
        		public Receiver()
        		{
            		super(MyADMMessageHandler.class);
        		}
    		}
	
			private void sendNotification(String msg) {
				Context ctx = getApplicationContext();
		
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

4. В метод `OnMessage()` добавьте следующий код:
	
		String nhMessage = intent.getExtras().getString("msg");
		sendNotification(nhMessage);
 
5. В метод `OnRegistered` добавьте следующий код:

			try {
		getNotificationHub(getApplicationContext()).register(registrationId);
			} catch (Exception e) {
		Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
			}

6.	В метод `OnRegistered` добавьте следующий код:

			try {
				getNotificationHub(getApplicationContext()).unregister();
			} catch (Exception e) {
				Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
			}

7. Затем в методе `MainActivity` добавьте следующую инструкцию import:

		import com.amazon.device.messaging.ADM;				

8. Теперь в конце метода `OnCreate` добавьте следующий код:

		final ADM adm = new ADM(this);
		if (adm.getRegistrationId() == null)
		{
		   adm.startRegister();
		} else {
			new AsyncTask() {
			      @Override
			      protected Object doInBackground(Object... params) {
			         try {			        	 MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
			         } catch (Exception e) {
			        	 Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
			        	 return e;
			         }
			         return null;
			     }
			   }.execute(null, null, null);
		}

## Добавление ключа API в приложение

1. В Eclipse создайте новый файл с именем **api_key.txt** в активах каталога проекта.
2. Откройте файл и скопируйте **Ключ API**, созданный на портале разработчика Amazon.

## Запуск приложения

1. Запустите эмулятор.
2. В эмуляторе сверху щелкните **Параметры**, затем щелкните **Моя учетная запись** и зарегистрируйтесь с действующей учетной записью Amazon.
3. В Eclipse запустите приложение.

<div class="dev-callout"><b>Примечание.</b>
    <p>Если возникает проблема, проверьте время эмулятора (или устройства). Значение времени должно быть точным. Для изменения времени эмулятора Kindle выполните следующую команду из каталога средств для платформы Android SDK:  </p>
</div>
		adb shell  date -s "yyyymmdd.hhmmss"

## Отправка сообщения

Чтобы отправить сообщение с использованием .NET, используйте следующий код:

		static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[портал разработчика]: https://developer.amazon.com/home.html
[загрузка пакета SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
