1. Откройте диспетчер пакета SDK для Android, щелкнув значок на панели инструментов Android Studio в правом углу (если вы сомневаетесь, наведите указатель мыши на значок). Найдите целевую версию пакета SDK для Android, которая используется в проекте, откройте ее и выберите **Интерфейсы API Google**, если она уже не установлена.

2. Прокрутите вниз до элемента **Extras** (Дополнительно), разверните его и выберите **Google Play Services** (Службы воспроизведения Google), как показано ниже. Щелкните элемент **Install Packages** (Установить пакеты). Запомните путь к пакету SDK для использования в следующем шаге. 

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Откройте файл **build.gradle** в каталоге приложения.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. Добавьте следующую строку под строкой  *dependencies*: 

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. Под строкой  *defaultConfig* измените значение параметра  *minSdkVersion* на 9.
 
6. Нажмите кнопку **Синхронизировать проект с файлами Gradle** на панели инструментов.

7. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу  *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/версия_служб_google_play" />
 





<!--HONumber=45--> 
