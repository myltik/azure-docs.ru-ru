1. Откройте диспетчер пакета SDK для Android, щелкнув значок на панели инструментов Android Studio или выбрав **Сервис** **Android** -> **Диспетчер SDK** в меню. Найдите целевую версию пакета SDK для Android, которая используется в проекте, откройте ее и выберите **Интерфейсы API Google**, если они еще не установлены.

2. Перейдите к пунктам **Файл**, **Структура проекта**. Затем включите push-уведомления в разделе **Уведомления**.

3. Откройте файл **AndroidManifest.xml** и добавьте этот тег к тегу *application*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->