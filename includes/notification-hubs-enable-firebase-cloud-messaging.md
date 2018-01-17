

1. Войдите в [консоль Firebase](https://firebase.google.com/console/). Создайте проект Firebase, если его еще нет.
2. После создания проекта щелкните **Add Firebase to your Android app** (Добавить Firebase в приложение Android) и следуйте приведенным инструкциям.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. В консоли Firebase щелкните значок шестеренки для своего проекта и выберите **Project Settings** (Параметры проекта).

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Откройте вкладку **Общие** в настройках проекта и скачайте файл **google-services.json**, содержащий ключ API сервера и идентификатор клиента.
5. В настройках проекта щелкните вкладку **Cloud Messaging** (Обмен сообщениями в облаке) и скопируйте значение параметров **Legacy server key** (Устаревший ключ сервера). Это значение будет использоваться для настройки политики доступа концентратора уведомлений.
