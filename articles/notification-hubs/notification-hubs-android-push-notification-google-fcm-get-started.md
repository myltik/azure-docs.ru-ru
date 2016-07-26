<properties
	pageTitle="Отправка push-уведомлений в приложения Android с помощью центров уведомлений Azure и Firebase Cloud Messaging | Microsoft Azure"
	description="Из этого руководства вы узнаете, как использовать центры уведомлений Azure и Firebase Cloud Messaging для отправки push-уведомлений на устройства Android."
	services="notification-hubs"
	documentationCenter="android"
	keywords="push-уведомления, push-уведомление, push-уведомление android, FCM, Firebase Cloud Messaging"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="07/14/2016"
	ms.author="wesmc"/>

# Отправка push-уведомлений в приложения Android с помощью центров уведомлений Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Обзор

> [AZURE.IMPORTANT] В этой статье описывается отправка push-уведомлений с помощью Google Firebase Cloud Messaging (FCM). Если вы используете Google Cloud Messaging (GCM), см. статью [Отправка push-уведомлений в приложения Android с помощью центров уведомлений Azure](notification-hubs-android-push-notification-google-gcm-get-started.md).

В этом руководстве показано, как использовать центры уведомлений Azure и Firebase Cloud Messaging для отправки push-уведомлений в приложение на платформе Android. Вы создадите пустое приложение Android, которое получает push-уведомления с помощью Firebase Cloud Messaging.



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Полный код для этого руководства можно скачать на сайте [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).


##Предварительные требования

> [AZURE.IMPORTANT] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

- Кроме упомянутой выше действующей учетной записи Azure, для работы с этим руководством вам понадобится последняя версия [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
- Android версии 2.3 или более поздней.
- Репозиторий Google версии 27 или более поздней.
- Службы Google Play версии 9.0.2 или более поздней.
- Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Центрам уведомлений для приложений Android.


##Создание проекта Android Studio

1. В Android Studio создайте новый проект Android Studio.

   	![Android Studio — новый проект](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. Выберите форм-фактор **Phone and Tablet** (Телефон и планшет) и минимальную версию пакета SDK (с помощью параметра **Minimum SDK**), которые нужно поддерживать. Нажмите кнопку **Далее**.

   	![Android Studio — рабочий процесс создания проекта](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. Выберите для основного действия значение **Empty Activity** (Пустое действие), нажмите кнопку **Next** (Далее), а затем — **Finish** (Готово).


##Создание проекта с поддержкой Firebase Cloud Messaging

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##Настройка нового центра уведомлений

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. В колонке **Параметры** центра уведомлений щелкните **Службы уведомлений** и **Google (GCM)**. Введите ключ сервера FCM, скопированный ранее из [консоли Firebase](https://firebase.google.com/console/), и нажмите кнопку **Сохранить**.

&emsp;&emsp;![Центры уведомлений Azure — Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Теперь центр уведомлений настроен для работы с Firebase Cloud Messaging, а у вас есть строки подключения, с помощью которых вы можете зарегистрировать приложение для получения и отправки push-уведомлений.

##<a id="connecting-app"></a>Подключение приложения к центру уведомлений

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Добавление служб Play](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Добавление библиотек Центров уведомлений Azure


1. В файле `Build.Gradle` в классе **app** добавьте следующие строки в раздел **dependencies**.

		compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
		compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. После раздела **dependencies** добавьте следующий репозиторий:

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

### Обновление файла AndroidManifest.xml


1. Чтобы обеспечить поддержку FCM, нужно реализовать службу прослушивания идентификаторов экземпляра в своем коде. Таким образом можно [получать маркеры регистрации](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) с помощью [API идентификаторов экземпляра Google Firebase](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). В этом руководстве мы назовем класс `MyInstanceIDService`.
 
	Добавьте приведенное ниже определение службы внутри тега `<application>` в файле AndroidManifest.xml.

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. Получив маркер регистрации в FCM из API идентификатора экземпляра Firebase, мы будем использовать его для [регистрации в центре уведомлений Azure](notification-hubs-push-notification-registration-management.md). Мы обеспечим поддержку этой регистрации в фоновом режиме с помощью службы `IntentService` с именем `RegistrationIntentService`. Кроме того, эта служба будет отвечать за обновление маркера регистрации в FCM.
 
	Добавьте приведенное ниже определение службы внутри тега `<application>` в файле AndroidManifest.xml.

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. Теперь мы определим получателя уведомлений. Добавьте следующее определение получателя внутри тега `<application>` в файле AndroidManifest.xml. Замените заполнитель `<your package>` фактическим именем своего пакета, отображенным в верхней части файла `AndroidManifest.xml`.

		<receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
		    android:permission="com.google.android.c2dm.permission.SEND">
		    <intent-filter>
		        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
		        <category android:name="<your package name>" />
		    </intent-filter>
		</receiver>



4. Добавьте следующие разрешения, связанные с FCM, под тегом `</application>`. Обязательно замените `<your package>` именем пакета, отображенным в верхней части файла `AndroidManifest.xml`.

	Дополнительные сведения об этих разрешениях см. в статье [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest) (Настройка клиентского приложения GCM для Android) и [Migrate a GCM Client App for Android to Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm) (Перенос клиентского приложения GCM для Android в Firebase Cloud Messaging).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### Добавление кода


1. В представлении проекта разверните узел **app** > **src** > **main** > **java**. Щелкните правой кнопкой мыши папку пакета в **java**, щелкните **New** (Создать) и выберите **Java Class** (Класс Java). Добавьте новый класс с именем `NotificationSettings`.

	![Android Studio — новый класс Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

	Обязательно обновите эти три заполнителя в следующем коде для класса `NotificationSettings`:
	* **SenderId**: идентификатор отправителя, полученный ранее на вкладке **Cloud Messaging** параметров проекта в [консоли Firebase](https://firebase.google.com/console/).
	* **HubListenConnectionString**: укажите для центра строку подключения **DefaultListenAccessSignature**. Эту строку подключения можно скопировать, щелкнув **Политики доступа** в колонке **Параметры** центра на [портале Azure].
	* **HubName**: используйте имя центра уведомлений, которое отображается на [портале Azure] в колонке центра.

	Код `NotificationSettings`:

		public class NotificationSettings {
		    public static String SenderId = "<Your project number>";
		    public static String HubName = "<Your HubName>";
		    public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
		}

2. Добавьте еще один новый класс с именем `MyInstanceIDService`, используя приведенные выше шаги. Таким образом мы выполним реализацию службы прослушивания идентификаторов экземпляра.

	Код для этого класса будет вызывать службу `IntentService`, чтобы [обновить маркер FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) в фоновом режиме.

		import android.content.Intent;
		import android.util.Log;
		import com.google.firebase.iid.FirebaseInstanceIdService;
		
		
		public class MyInstanceIDService extends FirebaseInstanceIdService {
		
		    private static final String TAG = "MyInstanceIDService";
		
		    @Override
		    public void onTokenRefresh() {
		
		        Log.d(TAG, "Refreshing GCM Registration Token");
		
		        Intent intent = new Intent(this, RegistrationIntentService.class);
		        startService(intent);
		    }
		};


3. Добавьте еще один новый класс в проект `RegistrationIntentService`. Таким образом мы выполним реализацию службы `IntentService`, которая будет [обновлять маркер FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) и [выполнять регистрацию в центре уведомлений](notification-hubs-push-notification-registration-management.md).

	Используйте для этого класса следующий код:

		import android.app.IntentService;
		import android.content.Intent;
		import android.content.SharedPreferences;
		import android.preference.PreferenceManager;
		import android.util.Log;		
		import com.google.firebase.iid.FirebaseInstanceId;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class RegistrationIntentService extends IntentService {
		
		    private static final String TAG = "RegIntentService";
		
		    private NotificationHub hub;
		
		    public RegistrationIntentService() {
		        super(TAG);
		    }
		
		    @Override
		    protected void onHandleIntent(Intent intent) {
		
		        SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
		        String resultString = null;
		        String regID = null;
		        String storedToken = null;
		
		        try {
		            String FCM_token = FirebaseInstanceId.getInstance().getToken();
		            Log.d(TAG, "FCM Registration Token: " + FCM_token);
		
		            // Storing the registration id that indicates whether the generated token has been
		            // sent to your server. If it is not stored, send the token to your server,
		            // otherwise your server should have already received the token.
		            if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1,tag2").getRegistrationId();
		
		                resultString = "New NH Registration Successfully - RegId : " + regID;
		                Log.d(TAG, resultString);
		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		                sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
		            }
		
		            // Check if the token may have been compromised and needs refreshing.
		            else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1,tag2").getRegistrationId();
		
		                resultString = "New NH Registration Successfully - RegId : " + regID;
		                Log.d(TAG, resultString);
		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		                sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
		            }
		
		            else {
		                resultString = "Previously Registered Successfully - RegId : " + regID;
		            }
		        } catch (Exception e) {
		            Log.e(TAG, resultString="Failed to complete registration", e);
		            // If an exception happens while fetching the new token or updating our registration data
		            // on a third-party server, this ensures that we'll attempt the update at a later time.
		        }
		
		        // Notify UI that registration has completed.
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(resultString);
		        }
		    }
		}


		
4. Над объявлением класса `MainActivity` добавьте следующие операторы `import`:

		import com.google.android.gms.common.ConnectionResult;
		import com.google.android.gms.common.GoogleApiAvailability;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.content.Intent;
		import android.util.Log;
		import android.widget.TextView;
		import android.widget.Toast;

5. В верхней части класса добавьте следующие частные члены. Мы будем использовать эту [проверку доступности служб Google Play в соответствии с рекомендациями Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

	    public static MainActivity mainActivity;
    	public static Boolean isVisible = false;	
	    private static final String TAG = "MainActivity";
	    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. В классе `MainActivity` добавьте следующий метод проверки доступности служб Google Play:

	    /**
	     * Check the device to make sure it has the Google Play Services APK. If
	     * it doesn't, display a dialog that allows users to download the APK from
	     * the Google Play Store or enable it in the device's system settings.
	     */
	    private boolean checkPlayServices() {
	        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
	        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
	        if (resultCode != ConnectionResult.SUCCESS) {
	            if (apiAvailability.isUserResolvableError(resultCode)) {
	                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
	                        .show();
	            } else {
	                Log.i(TAG, "This device is not supported by Google Play Services.");
	                ToastNotify("This device is not supported by Google Play Services.");
	                finish();
	            }
	            return false;
	        }
	        return true;
	    }


7. В классе `MainActivity` добавьте следующий код, который будет проверять службы Google Play, прежде чем вызывать службу `IntentService`. Это позволит получить маркер регистрации в FCM и выполнить регистрацию в центре уведомлений.

	    public void registerWithNotificationHubs()
	    {
	        if (checkPlayServices()) {
	            // Start IntentService to register this application with FCM.
	            Intent intent = new Intent(this, RegistrationIntentService.class);
	            startService(intent);
	        }
	    }


8. В методе `OnCreate` класса `MainActivity` добавьте следующий код, чтобы начать регистрацию при создании действия:

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        mainActivity = this;
	        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
	        registerWithNotificationHubs();
	    }


9. Добавьте эти дополнительные методы в класс `MainActivity`, чтобы проверять состояние приложения и отображать в нем полученные данные.

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }
	
	    public void ToastNotify(final String notificationMessage) {
	        runOnUiThread(new Runnable() {
	            @Override
	            public void run() {
	                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                TextView helloText = (TextView) findViewById(R.id.text_hello);
	                helloText.setText(notificationMessage);
	            }
	        });
	    }


10. Метод `ToastNotify` использует элемент управления `TextView` *Hello World*, чтобы постоянно передавать в приложение сведения о состоянии и уведомления. В файле макета activity\_main.xml добавьте код ниже для этого элемента управления.

        android:id="@+id/text_hello"

11. Затем мы добавим подкласс для получателя, определенного в файле AndroidManifest.xml. Добавьте еще один новый класс в проект `MyHandler`.

12. Добавьте в начало файла `MyHandler.java` следующие операторы импорта:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.media.RingtoneManager;
		import android.net.Uri;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Добавьте в класс `MyHandler` следующий код, чтобы сделать его подклассом класса `com.microsoft.windowsazure.notifications.NotificationsHandler`.

	Этот код переопределяет метод `OnReceive` так, чтобы обработчик сообщал о полученных уведомлениях. Кроме того, обработчик отправляет push-уведомление в диспетчер уведомлений Android с помощью метода `sendNotification()`. Метод `sendNotification()` должен выполняться, когда незапущенное приложение получает уведомление.

		public class MyHandler extends NotificationsHandler {
		    public static final int NOTIFICATION_ID = 1;
		    private NotificationManager mNotificationManager;
		    NotificationCompat.Builder builder;
		    Context ctx;
		
		    @Override
		    public void onReceive(Context context, Bundle bundle) {
		        ctx = context;
		        String nhMessage = bundle.getString("message");
		        sendNotification(nhMessage);
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(nhMessage);
		        }
		    }
		
		    private void sendNotification(String msg) {
		
		        Intent intent = new Intent(ctx, MainActivity.class);
		        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
		
		        mNotificationManager = (NotificationManager)
		                ctx.getSystemService(Context.NOTIFICATION_SERVICE);
		
		        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		                intent, PendingIntent.FLAG_ONE_SHOT);
		
		        Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
		        NotificationCompat.Builder mBuilder =
		                new NotificationCompat.Builder(ctx)
		                        .setSmallIcon(R.mipmap.ic_launcher)
		                        .setContentTitle("Notification Hub Demo")
		                        .setStyle(new NotificationCompat.BigTextStyle()
		                                .bigText(msg))
		                        .setSound(defaultSoundUri)
		                        .setContentText(msg);
		
		        mBuilder.setContentIntent(contentIntent);
		        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		    }
		}


14. В Android Studio в строке меню щелкните **Build** (Сборка) > **Rebuild Project** (Повторить сборку проекта), чтобы убедиться в отсутствии ошибок.
15. Запустите приложение на устройстве и убедитесь, что регистрация в центре уведомлений успешно выполнена.

	> [AZURE.NOTE] Сбой регистрации может произойти при первом запуске до вызова метода `onTokenRefresh()` службы идентификаторов экземпляра. Чтобы заново начать регистрацию в центре уведомлений, обновите страницу.

##Отправка push-уведомлений

Чтобы проверить получение push-уведомлений в приложении, отправьте уведомление с [портала Azure] \(найдите раздел **Устранение неполадок** в колонке центра, как показано ниже).

![Центры уведомлений Azure — тестовая отправка](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## Отправка push-уведомлений непосредственно из приложения (необязательное действие)

>[AZURE.IMPORTANT] Этот пример с отправкой уведомлений из клиентского приложения приводится только в целях обучения. Так как в клиентском приложении требуется наличие `DefaultFullSharedAccessSignature`, пользователь может получить доступ к концентратору уведомлений для отправки несанкционированных уведомлений вашим клиентам.

Обычно уведомления отправляются с сервера базы данных. Но в некоторых случаях бывает удобно отправлять push-уведомления непосредственно из клиентского приложения. В этом разделе показано, как отправлять уведомления из клиента с помощью интерфейса [REST API центра уведомлений Azure](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. В представлении проекта Android Studio разверните узел **App** > **src** > **main** > **res** > **layout**. Откройте файл макета `activity_main.xml` и щелкните вкладку **Text** (Текст), чтобы обновить текстовое содержимое файла. Поместите в него приведенный ниже код, который добавляет новые элементы управления `Button` и `EditText` для отправки push-уведомлений в центр уведомлений. Добавьте этот код в конец, непосредственно перед `</RelativeLayout>`.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. В представлении проекта в Android Studio разверните узел **app** > **src** > **main** > **res** > **values**. Откройте файл `strings.xml` и добавьте строковые параметры, на которые ссылаются новые элементы управления `Button` и `EditText`. Добавьте их в конец файла, непосредственно перед `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. В файле `NotificationSetting.java` добавьте приведенный ниже параметр в класс `NotificationSettings`.

	Добавьте в `HubFullAccess` следующую строку подключения к центру: **DefaultFullSharedAccessSignature**. Чтобы скопировать эту строку подключения на [портале Azure], щелкните **Политики доступа** в колонке **Параметры** центра уведомлений.

		public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";

4. В файле `MainActivity.java` над классом `MainActivity` добавьте следующие операторы `import`.

		import java.io.BufferedOutputStream;
		import java.io.BufferedReader;
		import java.io.InputStreamReader;
		import java.io.OutputStream;
		import java.net.HttpURLConnection;
		import java.net.URL;
		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

6. В файле `MainActivity.java` в начало класса `MainActivity` добавьте следующие переменные.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;

6. Необходимо создать токен подписи программного доступа (SaS) для аутентификации запроса POST для отправки сообщений в центр уведомлений. Чтобы это сделать, проанализируйте данные ключа из строки подключения и создайте токен SaS, как упоминалось в справочнике по [основным понятиям](http://msdn.microsoft.com/library/azure/dn495627.aspx) REST API. Ниже приведен пример реализации.

	В файле `MainActivity.java` добавьте в класс `MainActivity` следующий метод, который анализирует строку подключения.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
	     * to parse the connection string so a SaS authentication token can be
	     * constructed.
	     *
	     * @param connectionString This must be the DefaultFullSharedAccess connection
	     *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);
	
	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }


7. В файле `MainActivity.java` добавьте в класс `MainActivity` следующий метод, который создает маркер проверки подлинности SaS.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
	     * construct a SaS token from the access key to authenticate a request.
	     *
	     * @param uri The unencoded resource URI string for this operation. The resource
	     *            URI is the full URI of the Service Bus resource to which access is
	     *            claimed. For example,
	     *            "http://<namespace>.servicebus.windows.net/<hubName>"
	     */
	    private String generateSasToken(String uri) {
	
	        String targetUri;
	        String token = null;
	        try {
	            targetUri = URLEncoder
	                    .encode(uri.toString().toLowerCase(), "UTF-8")
	                    .toLowerCase();
	
	            long expiresOnDate = System.currentTimeMillis();
	            int expiresInMins = 60; // 1 hour
	            expiresOnDate += expiresInMins * 60 * 1000;
	            long expires = expiresOnDate / 1000;
	            String toSign = targetUri + "\n" + expires;
	
	            // Get an hmac_sha1 key from the raw key bytes
	            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
	            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
	
	            // Get an hmac_sha1 Mac instance and initialize with the signing key
	            Mac mac = Mac.getInstance("HmacSHA256");
	            mac.init(signingKey);
	
	            // Compute the hmac on input data bytes
	            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
	
	            // Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
	            String signature = URLEncoder.encode(
	                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
	
	            // Construct authorization string
	            token = "SharedAccessSignature sr=" + targetUri + "&sig="
	                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
	        } catch (Exception e) {
	            if (isVisible) {
	                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
	            }
	        }
	
	        return token;
	    }



8. В файле `MainActivity.java` добавьте в класс `MainActivity` метод, который обрабатывает событие нажатия кнопки **Отправить уведомление** и отправляет push-уведомление в центр с помощью встроенного интерфейса REST API.

	    /**
	     * Send Notification button click handler. This method parses the
	     * DefaultFullSharedAccess connection string and generates a SaS token. The
	     * token is added to the Authorization header on the POST request to the
	     * notification hub. The text in the editTextNotificationMessage control
	     * is added as the JSON body for the request to add a GCM message to the hub.
	     *
	     * @param v
	     */
	    public void sendNotificationButtonOnClick(View v) {
	        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
	        final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";
	
	        new Thread()
	        {
	            public void run()
	            {
	                try
	                {
	                    // Based on reference documentation...
	                    // http://msdn.microsoft.com/library/azure/dn223273.aspx
	                    ParseConnectionString(NotificationSettings.HubFullAccess);
	                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
	                            "/messages/?api-version=2015-01");
	
	                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
	
	                    try {
	                        // POST request
	                        urlConnection.setDoOutput(true);
	
	                        // Authenticate the POST request with the SaS token
	                        urlConnection.setRequestProperty("Authorization", 
								generateSasToken(url.toString()));
	
	                        // Notification format should be GCM
	                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
	
	                        // Include any tags
	                        // Example below targets 3 specific tags
	                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
	                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
							//		"tag1 || tag2 || tag3");
	
	                        // Send notification message
	                        urlConnection.setFixedLengthStreamingMode(json.length());
	                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
	                        bodyStream.write(json.getBytes());
	                        bodyStream.close();
	
	                        // Get reponse
	                        urlConnection.connect();
	                        int responseCode = urlConnection.getResponseCode();
	                        if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
	                        }
	                    } finally {
	                        urlConnection.disconnect();
	                    }
	                }
	                catch(Exception e)
	                {
	                    if (isVisible) {
	                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
	                    }
	                }
	            }
	        }.start();
	    }



##Тестирование приложения

####Тестирование push-уведомлений в эмуляторе

Если вы хотите проверить отправку push-уведомлений в эмуляторе, убедитесь, что образ эмулятора поддерживает уровень API Google, выбранный для приложения. Если образ не поддерживает собственные API-интерфейсы Google, будет создано исключение **SERVICE\_NOT\_AVAILABLE**.

Кроме того, убедитесь, что вы добавили учетную запись Google в запущенный эмулятор. Для этого щелкните **Параметры** > **Учетные записи**. В противном случае попытки регистрации в GCM могут привести к порождению исключения **AUTHENTICATION\_FAILED**.

####Запуск приложения

1. Запустите приложение и убедитесь, что для успешной регистрации сообщается идентификатор регистрации.

   	![Тестирование на устройстве Android — регистрация канала](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. Введите сообщение уведомления для отправки на все устройства Android, которые зарегистрированы в центре.

   	![Тестирование на устройстве Android — отправка сообщения](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. Нажмите кнопку **Send Notification** (Отправить уведомление). На всех устройствах с запущенным приложением будет показан экземпляр `AlertDialog` с push-уведомлением. На устройствах, на которых приложение не запущено, но которые были ранее зарегистрированы для приема push-уведомлений, полученное уведомление будет добавлено в диспетчер уведомлений Android. Уведомления можно просматривать, проводя пальцем вниз от левого верхнего угла.

   	![Тестирование на устройстве Android — уведомления](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##Дальнейшие действия

На следующем этапе мы рекомендуем ознакомиться с руководством [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]. В нем описана процедура отправки уведомлений с сервера ASP.NET определенным пользователям с использованием тегов.

Если вы хотите разделить пользователей по группам интересов, изучите руководство по [использованию центров уведомлений для передачи экстренных новостей].

Дополнительную информацию о центрах уведомлений см. в статье [Общие сведения о концентраторах уведомлений].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Общие сведения о концентраторах уведомлений]: notification-hubs-push-notification-overview.md
[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: notification-hubs-aspnet-backend-android-notify-users.md
[использованию центров уведомлений для передачи экстренных новостей]: notification-hubs-aspnet-backend-android-breaking-news.md
[портала Azure]: https://portal.azure.com
[портале Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0720_2016-->