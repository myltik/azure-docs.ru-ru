1.  Откройте файл `AndroidManifest.xml`. В коде в следующих двух действиях замените _`**my_app_package**`_ именем пакета приложения для вашего проекта, которое является значением атрибута `package` тега `manifest`.

2.  Добавьте следующие новые разрешения после существующего элемента `uses-permission`:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  Добавьте следующий код после открывающего тега `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

4.  Загрузите и распакуйте [Android SDK мобильных служб][], откройте папку **notifications**, скопируйте файл **notifications-1.0.1.jar** в папку *libs* вашего проекта Eclipse и обновите папку *libs*.

    <div class="dev-callout"><b>Примечание.</b>
<p>Числа в конце этого имени файла могут меняться в последующих выпусках SDK.</p>
</div>

5.  Откройте файл *ToDoItemActivity.java* и добавьте следующий оператор импорта:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  Добавьте в класс следующую закрытую переменную, где *`<PROJECT_NUMBER>`* — номер проекта, назначенный Google вашему приложению в предыдущей процедуре:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  В методе **onCreate** перед созданием экземпляра MobileServiceClient добавьте следующий код, который регистрирует обработчик уведомлений для устройства:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Позднее мы определим класс MyHandler.class, на который ссылается этот код.

8.  В обозревателе пакетов щелкните правой кнопкой мыши пакет (под узлом `src`), щелкните команду **Создать**, выберите **Класс**.

9.  В поле **Имя** введите `MyHandler`, в поле **Суперкласс** укажите `com.microsoft.windowsazure.notifications.NotificationsHandler`, затем щелкните кнопку **Готово**

    ![][]

    При этом создается новый класс MyHandler.

10. Добавьте следующие операторы импорта для класса `MyHandler`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

        import com.microsoft.windowsazure.mobileservices.Registration;
        import com.microsoft.windowsazure.mobileservices.RegistrationCallback;

11. Далее добавьте следующих участников для класса `MyHandler`:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. В классе `MyHandler` добавьте следующий код для переопределения метода **onRegistered**: который регистрирует ваше устройство в концентраторе уведомлений мобильной службы.

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // handle error
                      }
                }
            });
        }

13. В классе `MyHandler` добавьте следующий код для переопределения метода **onReceive**, который отображает уведомление при получении.

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

Ваше приложение теперь обновлено для поддержки push-уведомлений.

<!-- URLs. -->

  [Android SDK мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  []: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
