1. Откройте диспетчер пакета SDK для Android, щелкнув значок на панели инструментов Android Studio или выбрав **Сервис** **Android** -> **Диспетчер SDK** в меню. Щелкните ссылку **Launch Standalone SDK Manager** (Запустить автономный диспетчер пакетов SDK).

2. Найдите целевую версию пакета SDK для Android, который используется в проекте, и откройте ее. Если интерфейсы **API Google** еще не установлены, выберите их и щелкните **Install packages…** (Установить пакеты).

3. В Android Studio выберите **File** (Файл) > **Project Structure** (Структура проекта). Щелкните **Notifications** (Уведомления), выберите **Google Cloud Messaging** и нажмите клавишу **ОК**.

<!--
3. Open **AndroidManifest.xml** and add this tag to the *application* tag.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
-->

<!---HONumber=AcomDC_0309_2016-->