1. В проекте **приложения** откройте файл `AndroidManifest.xml`. В коде в следующих двух действиях замените _`**my_app_package**`_ именем пакета приложения для вашего проекта, которое является значением атрибута `package` тега `manifest`. 

2. Добавьте следующие новые разрешения после существующего элемента `uses-permission`:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Добавьте следующий код после открывающего тега `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Добавьте следующую строку под словом *dependencies* в файле **build.gradle** в каталоге приложения и повторно синхронизируйте Gradle с проектом:

	    compile(group: 'com.microsoft.azure', name: 'azure-notifications-handler', version: '1.0.1', ext: 'jar')


5. Откройте файл *ToDoItemActivity.java* и добавьте следующий оператор импорта:

		import com.microsoft.windowsazure.notifications.NotificationsManager;


6. Добавьте в класс следующую частную переменную, где _`<PROJECT_NUMBER>`_ — номер проекта, назначенный Google вашему приложению в предыдущей процедуре:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. Измените определение *MobileServiceClient* с **частного** на **публичный статический**, чтобы оно выглядело следующим образом:

		public static MobileServiceClient mClient;



8. Далее необходимо добавить новый класс для обработки уведомлений. В обозревателе проектов откройте узлы **src** > **main** > **java** и щелкните правой кнопкой мыши узел с именем пакета, выберите **Создать** и **Класс Java**.

9. В поле **Имя** введите `MyHandler`, а затем нажмите кнопку **ОК**.


	![](./media/mobile-services-android-get-started-push/android-studio-create-class.png)


10. В файле MyHandler замените объявление класса на

		public class MyHandler extends NotificationsHandler {


11. Добавьте следующие операторы импорта для класса `MyHandler`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. Затем добавьте в класс `MyHandler` следующие члены:

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. В классе `MyHandler` добавьте следующий код для переопределения метода **onRegistered**, который регистрирует устройство в Центре уведомлений мобильной службы.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. В классе `MyHandler` добавьте следующий код для переопределения метода **onReceive**, который отображает уведомление при получении.

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
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


15. В файле TodoActivity.java обновите метод **onCreate** класса *ToDoActivity*, чтобы зарегистрировать класс обработчика уведомлений. Добавлять этот код нужно обязательно после кода создания экземпляра *MobileServiceClient*.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Ваше приложение теперь обновлено для поддержки push-уведомлений.

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=62-->