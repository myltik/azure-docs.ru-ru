1. Откройте диспетчер пакета SDK для Android, щелкнув значок на панели инструментов Android Studio или выбрав **Сервис** **Android** -> **Диспетчер SDK** в меню. Найдите целевую версию пакета SDK для Android, которая используется в проекте, откройте ее и выберите **Интерфейсы API Google**, если они еще не установлены.

2. Прокрутите вниз до элемента**Дополнительно**, разверните его и выберите **Службы Google Play**, как показано ниже. Щелкните элемент **Install Packages** (Установить пакеты). Запомните путь к пакету SDK для использования в следующем шаге.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Откройте файл **build.gradle** в каталоге приложения.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. Добавьте следующую строку в *зависимости*:

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. В разделе *defaultConfig* измените значение *minSdkVersion* на 9.
 
6. Щелкните значок **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle) на панели инструментов.

7. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=July15_HO2-->