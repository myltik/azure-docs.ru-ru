1. Откройте диспетчер пакетов SDK для Android, щелкнув значок на панели инструментов Android Studio или выбрав **Сервис** -> **Android** -> **SDK Manager** (Диспетчер пакетов SDK) в меню. Найдите нужную версию пакета SDK для Android, которая используется в проекте. Откройте ее, щелкнув **Show Package Details** (Отображать окно свойств пакета), и выберите **Google APIs** (Интерфейсы API Google), если они еще не установлены.
2. Щелкните вкладку **Пакет инструментов SDK**. Если сервисы Google Play еще не установлены, щелкните **Google Play Services** (Сервисы Google Play), как показано ниже. Нажмите кнопку **Применить**, чтобы начать установку. 
   
    Запишите путь к пакету SDK. Он вам потребуется в дальнейшем. 
   
       ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Откройте файл **build.gradle** в каталоге приложения.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Добавьте следующую строку в *зависимости*: 
   
           compile 'com.google.android.gms:play-services-gcm:9.2.0'
5. Щелкните значок **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle) на панели инструментов.
6. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу *application* .
   
        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />



<!--HONumber=Nov16_HO2-->


