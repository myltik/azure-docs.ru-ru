

1. Войдите в [консоль Firebase](https://firebase.google.com/console/). Создайте проект Firebase, если его еще нет.
2. После создания проекта выберите **Add Firebase to your Android app** (Добавить Firebase в приложение Android). и следуйте инструкциям.

    ![Добавление Firebase в приложение Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. В консоли Firebase щелкните значок шестеренки возле имени проекта. Выберите пункт **Project Settings** (Параметры проекта).

    ![Выбор параметров проекта](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Выберите вкладку **General** (Общие) в окне параметров проекта. Затем скачайте файл **google-services.json**, содержащий ключ API сервера и идентификатор клиента.
5. В окне параметров проекта щелкните вкладку **Cloud Messaging** (Обмен сообщениями в облаке) и скопируйте значение параметра **Legacy server key** (Старый ключ сервера). Это значение будет использоваться для настройки политики доступа концентратора уведомлений.
