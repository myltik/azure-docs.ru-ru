<properties linkid="develop-notificationhubs-tutorials-send-breaking-news-ios" urlDisplayName="Breaking News" pageTitle="Notification Hubs Breaking News Tutorial - iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications to iOS devices." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="elioda" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda" />

# Использование концентраторов уведомлений для передачи экстренных новостей

<div class="dev-center-tutorial-selector sublanding">

[Windows Universal][Windows Universal][Windows Phone][Windows Phone][iOS][iOS] [Android][Android]

</div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для рассылки уведомлений об экстренных новостях в приложение Android. По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Данный сценарий является общеупотребимым шаблоном для многих приложений, где требуется отправлять уведомления группам пользователей, ранее проявивших к ним интерес, например, программы чтения RSS, приложений для музыкальных фанатов и т. д.

Широковещательные сценарии реализуются путем включения одного или нескольких *тегов* при создании регистрации в концентраторе уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительные сведения о тегах см. в разделе [Руководство по использованию концентраторов уведомлений][Руководство по использованию концентраторов уведомлений].

В этом учебнике рассматриваются следующие основные шаги для реализации данного сценария:

1.  [Добавление возможности выбора категорий в приложение][Добавление возможности выбора категорий в приложение]
2.  [Регистрация для использования уведомлений][Регистрация для использования уведомлений]
3.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]
4.  [Запуск приложения и создание уведомлений][Запуск приложения и создание уведомлений]

Материал данного раздела основан на приложении, созданном в разделе [Приступая к работе с концентраторами уведомлений][Приступая к работе с концентраторами уведомлений]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с концентраторами уведомлений][Приступая к работе с концентраторами уведомлений].

## <a name="adding-categories"></a>Добавление возможности выбора категорий в приложение

Прежде всего, необходимо добавить элементы пользовательского интерфейса для имеющегося основного действия, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в концентраторе уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

1.  Откройте файл res/layout/activity\_main.xml file и замените содержимое на следующее:

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2.  Откройте файл res/values/strings.xml и добавьте следующие строки кода:

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    Основная графическая структура main\_activity.xml должна выглядеть следующим образом:

    ![][0]

3.  Теперь создайте класс **Notifications** в том же пакете, в котором создан класс **MainActivity**.

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;        

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId) {
                this.context = context;
                this.senderId = senderId;

                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(<hub name>, <connection string with listen access>, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
                            hub.register(regid, categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }

                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Этот класс использует локальное хранилище для хранения категорий новостей, которые данное устройство должно получать. Он также содержит методы для регистрации этих категорий.

4.  В приведенном выше коде замените заполнители `<hub name>`и `<connection string with listen access>` именем концентратора уведомлений и строкой подключения для элемента *DefaultListenSharedAccessSignature*, полученного ранее.

    <div class="dev-callout">

    <b>Примечание.</b>
    Поскольку учетные данные, которые распространяются с помощью клиентского приложения, обычно не безопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Для отправки уведомлений и изменения существующих регистраций используется ключ полного доступа в защищенной серверной службе.

    </div>

5.  В классе **MainActivity** удалите частные поля для **NotificationHub** и **GoogleCloudMessaging**, после чего добавьте поле для **Notifications**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

6.  Затем в методе **onCreate** удалите код инициализации поля **hub** и метод **registerWithNotificationHubs**. Затем добавьте следующие строки, инициализирующие экземпляр класса **Notifications**. Метод должен содержать следующие строки:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);

            notifications = new Notifications(this, SENDER_ID);
        }

7.  Затем добавьте следующий метод:

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Этот метод создает список категорий и использует класс **Notifications** класс для хранения списка в локальном хранилище и регистрации соответствующих тегов в концентраторе уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

Ваше приложение теперь может сохранять набор категорий в локальном хранилище на устройстве и регистрироваться в концентраторе уведомлений всякий раз, когда пользователь изменяет выбранные категории.

## <a name="register"></a>Регистрация для использования уведомлений

Эти действия позволяют зарегистрироваться в концентраторе уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище.

<div class="dev-callout">

<b>Примечание.</b>
Поскольку registrationId, назначенный службой Google Cloud Messaging (GCM), может измениться в любое время, следует регулярно выполнять регистрацию для использования уведомлений, чтобы предотвратить сбои уведомлений. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для приложений, которые запускаются чаще одного раза в день, в целях экономии пропускной способности регистрацию можно пропустить, если с момента предыдущей регистрации прошло менее одного дня.

</div>

1.  Добавьте в класс **Notifications** следующий код:

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

    Это возвращает категории, определенные в данном классе.

2.  Теперь в конце метода **OnCreate** в классе **MainActivity** добавьте следующий код:

        notifications.subscribeToCategories(notifications.retrieveCategories());

    Это гарантирует, что при каждом запуске приложения оно извлекает категории из локального хранилища и запрашивает для них регистрацию. Метод **InitNotificationsAsync** был создан в рамках работы с учебником "Приступая к работе с концентраторами уведомлений", однако в данном разделе он не требуется.

3.  Затем добавьте в класс **MainActivity** следующий метод:

        @Override
        protected void onStart() {
            super.onStart();

            Set<String> categories = notifications.retrieveCategories();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            world.setChecked(categories.contains("world"));
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            politics.setChecked(categories.contains("politics"));
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            business.setChecked(categories.contains("business"));
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            technology.setChecked(categories.contains("technology"));
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            science.setChecked(categories.contains("science"));
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            sports.setChecked(categories.contains("sports"));
        }

    При этом основное действие обновляется в зависимости от состояния ранее сохраненных категорий.

Теперь приложение готово и может сохранять набор категорий в локальном хранилище устройств и использовать его для регистрации в концентраторе уведомлений всякий раз, когда пользователь изменяет выбранные категории. А сейчас определим серверную часть, которая может отправлять уведомления категорий в это приложение.

## <a name="send"></a><span class="short-header">Отправка уведомлений</span>Отправка уведомлений из серверной части

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>Запуск приложения и создание уведомлений

1.  В Eclipse выполните построение приложения и запустите его на устройстве или в эмуляторе.

    Обратите внимание, что в пользовательском интерфейсе присутствует набор переключателей, позволяющий выбрать категории для подписки.

2.  Включите переключатель для одной или нескольких категорий, а затем щелкните элемент **Подписаться**.

    Приложение преобразует выбранные категории в теги и запрашивает у концентратора уведомлений новую регистрацию устройств для выбранных тегов. Зарегистрированные категории возвращаются и отображаются в диалоговом окне.

3.  Отправьте новое уведомление из серверной части одним из следующих способов:

    -   **Консольное приложение .NET:** запустите консольное приложение.

    -   **Java/PHP:** запустите приложение/сценарий.

    Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике мы рассмотрели, как производить рассылку экстренных новостей по категориям. Далее вам рекомендуется изучить один из следующих учебников, в которых рассматриваются более сложные сценарии использования концентраторов уведомлений:

-   [раздел Использование концентраторов уведомлений для передачи локализованных экстренных новостей][раздел Использование концентраторов уведомлений для передачи локализованных экстренных новостей]

    Узнайте, как расширить возможности приложения экстренных новостей для отправки локализованных уведомлений.

-   [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений]

    Узнайте, как рассылать push-уведомления определенным пользователям, прошедшим проверку подлинности. Это хорошее решение для отправки уведомлений только определенным пользователям.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs.-->

  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/ "Windows Universal"
  [Windows Phone]: /ru-ru/documentation/articles/notification-hubs-windows-phone-send-breaking-news/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-send-breaking-news/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/ "Android"
  [Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
  [Добавление возможности выбора категорий в приложение]: #adding-categories
  [Регистрация для использования уведомлений]: #register
  [Отправка уведомлений из серверной части]: #send
  [Запуск приложения и создание уведомлений]: #test-app
  [Приступая к работе с концентраторами уведомлений]: /ru-ru/documentation/articles/notification-hubs-android-get-started/
  [0]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG
  [notification-hubs-back-end]: ../includes/notification-hubs-back-end.md
  [раздел Использование концентраторов уведомлений для передачи локализованных экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users
