1. Откройте диспетчер пакета SDK для Android, щелкнув значок на панели инструментов Android Studio или выбрав **Сервис** **Android** -> **Диспетчер SDK** в меню. Найдите целевую версию пакета SDK для Android, которая используется в проекте, откройте ее и выберите **Интерфейсы API Google**, если они еще не установлены.

2. Щелкните вкладку **Пакет инструментов SDK**. Если служба Google Play еще не установлена, щелкните **Google Play Services** (см. рисунок ниже). Нажмите кнопку **Применить**, чтобы начать установку.
 
	Запишите путь к пакету SDK. Он вам потребуется в дальнейшем.

   	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Откройте файл **build.gradle** в каталоге приложения.

	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. Добавьте следующую строку в раздел *android*.

		useLibrary 'org.apache.http.legacy'

5. Добавьте следующую строку в *зависимости*:

   		compile 'com.google.android.gms:play-services-base:6.5.87'

7. В разделе *defaultConfig* измените значение *minSdkVersion* на 9.
 
8. Щелкните значок **Sync Project with Gradle Files** (Синхронизировать проект с файлами Gradle) на панели инструментов.

9. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=Nov15_HO1-->