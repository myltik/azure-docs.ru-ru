---
title: Отправка push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging | Документация Майкрософт
description: Сведения о том, как использовать Центры уведомлений Azure и Google Cloud Messaging для отправки push-уведомлений на конкретные устройства Android.
services: notification-hubs
documentationcenter: android
author: dimazaid
manager: kpiteira
editor: spelluru'
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/06/2018
ms.author: dimazaid
ms.openlocfilehash: 00d6a2de3f51ef8ade50f29b14a4bc944ae5fee6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-specific-android-devices-using-azure-notification-hubs-and-google-cloud-messaging"></a>Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как использовать Центры уведомлений Azure для рассылки широковещательных уведомлений о последних новостях в приложение Android. По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Этот сценарий часто используется во многих приложениях, которые отправляют уведомления определенным группам пользователей, подтвердившим интерес к этим уведомлениям. Типичные примеры: средства чтения RSS, приложения для любителей музыки и т. д.

Широковещательные сценарии реализуются путем включения одного или нескольких *тегов* при создании регистрации в концентраторе уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительные сведения о тегах см. в статье [Маршрутизация и выражения тегов](notification-hubs-tags-segment-push-message.md).

Вот какие действия выполняются в этом руководстве: 

> [!div class="checklist"]
> * добавление возможности выбора категорий в мобильное приложение;
> * регистрация для получения уведомлений с тегами; 
> * отправка уведомлений с тегами. 
> * Тестирование приложения

## <a name="prerequisites"></a>предварительным требованиям
В этом руководстве используется приложение, которое вы создали при работе со статьей [Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging][get-started]. Прежде чем начинать работу с этим руководством, выполните все действия из статьи [Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging][get-started].

## <a name="add-category-selection-to-the-app"></a>Добавление возможности выбора категорий в приложение
Прежде всего, необходимо добавить элементы пользовательского интерфейса для имеющегося основного действия, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в концентраторе уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

1. Откройте файл res/layout/activity_main.xml и замените его содержимое следующим текстом:
   
    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
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
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Откройте файл res/values/strings.xml и добавьте следующие строки кода:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    Графическая структура файла main_activity.xml должна выглядеть следующим образом:
   
    ![][A1]
3. Создайте класс `Notifications` в том же пакете, в котором существует класс **MainActivity**.

    ```java   
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName, 
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                            categories.toArray(new String[categories.size()]));
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
    ```
       
    Этот класс использует локальное хранилище для хранения категорий новостей, которые данное устройство должно получать. Он также содержит методы для регистрации этих категорий.
4. В классе **MainActivity** удалите частные поля для **NotificationHub** и **GoogleCloudMessaging**, после чего добавьте поле для **Notifications**:

    ```java   
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. Затем в методе **onCreate** удалите код инициализации поля **hub** и метод **registerWithNotificationHubs**. Затем добавьте следующие строки, которые инициализируют экземпляр класса **Notifications**. 

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Убедитесь, что в классе NotificationSettings правильно указаны имя центра и строка подключения.

    > [AZURE.NOTE] Так как учетные данные, которые распространяются с помощью клиентского приложения, обычно небезопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Для отправки уведомлений и изменения существующих регистраций используется ключ полного доступа в защищенной серверной службе.
1. Теперь добавьте следующие операторы import:

    ```java   
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
1. Добавьте этот метод `subscribe` для обработки нажатия кнопки "Подписаться":        
   
    ```java
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
    ```
       
    Этот метод создает список категорий и использует класс **Notifications** класс для хранения списка в локальном хранилище и регистрации соответствующих тегов в концентраторе уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

Ваше приложение теперь может сохранять набор категорий в локальном хранилище на устройстве и регистрироваться в центре уведомлений всякий раз, когда пользователь изменяет выбранные категории.

## <a name="register-for-notifications"></a>Регистрация для использования уведомлений
Эти действия позволяют зарегистрироваться в центре уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище.

> [!NOTE]
> Поскольку registrationId, назначенный службой Google Cloud Messaging (GCM), может в любой момент измениться, следует регулярно регистрироваться для получения уведомлений, чтобы предотвратить сбои в их передаче. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для тех приложений, которые запускаются часто, более одного раза в день, возможно, лучше пропустить регистрацию, чтобы сэкономить трафик, если с момента прошлой регистрации прошло меньше суток.
> 
> 

1. В конце метода **OnCreate** в классе **MainActivity** добавьте следующий код:
   
    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```
   
    Этот код гарантирует, что при каждом запуске приложения категории извлекаются из локального хранилища и для них запрашивается регистрация. 
2. Затем обновите метод `onStart()` класса `MainActivity` следующим образом.
   
    ```java
    @Override
    protected void onStart() {
   
        super.onStart();
        isVisible = true;
   
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
    ```
   
    Этот код обновляет основное действие в зависимости от состояния ранее сохраненных категорий.

Теперь приложение готово и может сохранять набор категорий в локальном хранилище устройств и использовать его для регистрации в концентраторе уведомлений всякий раз, когда пользователь изменяет выбранные категории. Теперь определите серверную часть, которая может отправлять в это приложение уведомления по категориям.

## <a name="send-tagged-notifications"></a>Отправка уведомлений с тегами
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Тестирование приложения
1. Запустите приложение в Android Studio на устройстве Android или в эмуляторе. В пользовательском интерфейсе присутствует набор переключателей, позволяющий выбрать категории для подписки.
2. Включите переключатели одной или нескольких категорий, затем нажмите **Подписаться**. Приложение преобразует выбранные категории в теги и запрашивает у концентратора уведомлений новую регистрацию устройств для выбранных тегов. Зарегистрированные категории возвращаются и отображаются во всплывающем уведомлении.

    ![Подписка на категории](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
1. Запустите консольное приложение .NET, которое отправляет уведомления по каждой категории. Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.

    ![Уведомления о технологических новостях](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как рассылать широковещательные уведомления на конкретные устройства Android, зарегистрированные для получения уведомлений по категориям. Сведения о том, как отправлять push-уведомления конкретным пользователям, вы найдете в следующем руководстве: 

> [!div class="nextstepaction"]
>[Отправка push-уведомлений конкретным адресатам](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md).

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
