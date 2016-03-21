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


4. Откройте файл *ToDoActivity.java* и добавьте следующий оператор импорта:

		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Добавьте в класс следующую частную переменную, где _`<PROJECT_NUMBER>`_ — номер проекта, назначенный Google вашему приложению в предыдущей процедуре:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

6. Измените определение *MobileServiceClient* с **частного** на **публичный статический**, чтобы оно выглядело следующим образом:

		public static MobileServiceClient mClient;

7. Далее необходимо добавить новый класс для обработки уведомлений. В обозревателе проектов откройте узлы **src** > **main** > **java** и щелкните правой кнопкой мыши узел с именем пакета, выберите **Создать** и **Класс Java**.

8. В поле **Имя** введите `MyHandler`, а затем нажмите кнопку **ОК**.


	![](./media/mobile-services-android-get-started-push/android-studio-create-class.png)


9. В файле MyHandler замените объявление класса на

		public class MyHandler extends NotificationsHandler {


10. Добавьте следующие операторы импорта для класса `MyHandler`:

		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;


11. Затем добавьте в класс `MyHandler` следующий член.

		public static final int NOTIFICATION_ID = 1;


12. В классе `MyHandler` добавьте следующий код для переопределения метода **onRegistered**, который регистрирует устройство в Центре уведомлений мобильной службы.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);

		    new AsyncTask<Void, Void, Void>() {

		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId);
		    		    return null;
	    		    }
	    		    catch(Exception e) {
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}


13. В классе `MyHandler` добавьте следующий код для переопределения метода **onReceive**, который отображает уведомление при получении.

		@Override
		public void onReceive(Context context, Bundle bundle) {
        		String msg = bundle.getString("message");

        		PendingIntent contentIntent = PendingIntent.getActivity(context,
                		0, // requestCode
                		new Intent(context, ToDoActivity.class),
                		0); // flags

        		Notification notification = new NotificationCompat.Builder(context)
                		.setSmallIcon(R.drawable.ic_launcher)
                		.setContentTitle("Notification Hub Demo")
                		.setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                		.setContentText(msg)
                		.setContentIntent(contentIntent)
                		.build();

        		NotificationManager notificationManager = (NotificationManager)
                		context.getSystemService(Context.NOTIFICATION_SERVICE);
        		notificationManager.notify(NOTIFICATION_ID, notification);
		}


14. В файле TodoActivity.java обновите метод **onCreate** класса *ToDoActivity*, чтобы зарегистрировать класс обработчика уведомлений. Добавлять этот код нужно обязательно после кода создания экземпляра *MobileServiceClient*.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Ваше приложение теперь обновлено для поддержки push-уведомлений.

<!---HONumber=AcomDC_0309_2016-->