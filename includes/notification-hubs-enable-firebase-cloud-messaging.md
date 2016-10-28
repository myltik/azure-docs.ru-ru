

1. Войдите в [консоль Firebase](https://firebase.google.com/console/). Создайте проект Firebase, если его еще нет.
2. После создания проекта щелкните **Add Firebase to your Android app** (Добавить Firebase в приложение Android) и следуйте приведенным указаниям.

	![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. В консоли Firebase щелкните значок шестеренки для своего проекта и выберите **Project Settings** (Параметры проекта).

	![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. В настройках проекта щелкните вкладку **Cloud Messaging** и скопируйте значение параметров **Server key** (Ключ сервера) и **Sender ID** (Идентификатор отправителя). Позже эти значения будут использоваться для настройки политики доступа центра уведомлений и обработчика уведомлений в приложении.
  

<!---HONumber=AcomDC_0706_2016-->