
1. Выберите **Службы приложений**, выберите серверную часть мобильного приложения, щелкните **Быстрый запуск** и выберите свою клиентскую платформу (iOS, Android, Xamarin, Cordova).

![Портал Azure с выделенным пунктом "Mobile Apps Quickstart" (Быстрый запуск мобильных приложений)][quickstart]

2. Если подключение к базе данных не настроено, необходимо создать одно подключение к данным.

![Подключение мобильных приложений к базе данных на портале Azure][connect]

  * Создайте сервер и базу данных SQL.

  ![Создание базы данных и сервера с помощью мобильных приложений на портале Azure][server]

  * Подождите, пока подключение к данным не будет успешно создано.

  ![Портал Azure c уведомлением от мобильных приложений о создании подключения к данным][notification]

  * Подключение к данным должно быть успешными.

  ![Портал Azure c уведомлением от мобильных приложений о создании подключения к данным][already-connection]

3. В разделе **2. Создание API таблицы** выберите **язык серверной части** — Node.js. Примите подтверждение и щелкните **Создание таблицы TodoItem**. В базе данных будет создана таблица *TodoItem* . Помните, что при переключении существующей серверной части на Node.js будет перезаписано все содержимое. Чтобы создать серверную часть .NET, выполните [эти инструкции][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
