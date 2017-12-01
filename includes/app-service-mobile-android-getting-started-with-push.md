1. В проекте **приложения** откройте файл `AndroidManifest.xml`. Добавьте следующий код после открывающего тега `application` :

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Откройте файл `ToDoActivity.java` и внесите в него следующие изменения:

    - Добавьте оператор import.

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Измените определение объекта `MobileServiceClient` с **private** на **private static**.

        ```java
        private static MobileServiceClient mClient;
        ```

    - Добавьте метод `registerPush`.

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Измените метод **onCreate** класса `ToDoActivity`. После создания экземпляра `MobileServiceClient` обязательно добавьте следующий код:

        ```java
        registerPush();
        ```

3. Добавьте новый класс для обработки уведомлений. В обозревателе проектов откройте узлы **app** > **java** > **пространство_имен_вашего_проекта** и щелкните правой кнопкой мыши узел с именем пакета. Выберите **Создать** и **Java Class** (Класс Java). В поле "Имя" введите `ToDoMessagingService` и нажмите кнопку "ОК". Замените объявление класса следующим кодом:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Добавьте еще один класс, который будет обрабатывать изменения токена. Создайте класс Java `ToDoInstanceIdService` и замените объявление класса следующим кодом:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Ваше приложение теперь обновлено для поддержки push-уведомлений.
