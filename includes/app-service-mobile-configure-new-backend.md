
1. Нажмите кнопку **службы приложений** кнопку выберите вашей серверной части мобильных приложений, выберите **краткое руководство**и затем выберите клиентскую платформу (iOS, Android, Xamarin, Cordova).

    ![Портал Azure с примеры использования мобильных приложений выделен][quickstart]

2. Если не настроено подключение к базе данных, создайте следующим образом:

    ![Портал Azure с помощью мобильного приложения подключения к базе данных][connect]

    а. Создание новой базы данных SQL и сервера.

    ![Портал Azure с мобильным приложениям Создание новой базы данных и сервера][server]

    b. Подождите, пока подключение к данным не будет успешно создано.

    ![Azure портала уведомление об успешном создании подключение к данным][notification]

    c. Подключение к данным должно быть успешными.

    ![Уведомления на портале Azure, «уже есть подключение к данным»][already-connection]

3. В разделе **2. Создание API таблицы** выберите **язык серверной части** — Node.js. 
 
4. Примите подтверждение, а затем выберите **таблицы TodoItem создания**.  
    Это действие создает новую таблицу элемент задачи в базе данных. 

    >[!IMPORTANT]
    > Переключение существующей серверной части для Node.js перезаписывает все содержимое. Вместо этого создать серверной части .NET в разделе [работы с сервером серверной части .NET SDK для мобильных приложений][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
