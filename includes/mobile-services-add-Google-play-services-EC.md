

1. Откройте диспетчер пакета SDK для Android, щелкнув **Window** (Окно) из верхней панели инструментов Eclipse. Найдите целевую версию пакета SDK для Android, которая указана в свойствах проекта, откройте ее и выберите **Google APIs** (Интерфейсы API Google).

2. Прокрутите вниз до элемента **Extras** (Дополнительно), разверните его и выберите **Google Play Services** (Службы воспроизведения Google), как показано ниже. Щелкните элемент **Install Packages** (Установить пакеты). Запомните путь к пакету SDK для использования в следующем шаге. Перезапустите Eclipse.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Установите пакет SDK служб Google Play в вашем проекте. В Eclipse щелкните **Файл**, а затем - **Импорт**. Выберите **Android**, затем - **Существующий код Android в рабочую область** и нажмите кнопку **Далее**. Нажмите кнопку **Обзор**, перейдите во вложенную папку, где расположен пакет SDK для Android (обычно это папка с именем `adt-bundle-windows-x86_64` inside the folder that contains Eclipse), then go to the `\extras\google\google_play_services\libproject`), выберите в ней папку "google-play-services-lib" и нажмите кнопку **ОК**. Установите флажок **Копировать проекты в рабочую область** и нажмите кнопку **Готово**.

	![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

4. Далее необходимо сослаться в вашем проекте на только что импортированную библиотеку из пакета SDK служб Google Play. 

5. В **обозревателе пакетов** щелкните проект правой кнопкой мыши и выберите  *Properties*.
 
6. В окне "Свойства" выберите Android в левой части окна.

	![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)


7. Убедитесь, что в разделе **Целевая сборка проекта** задана соответствующая версия  `Google APIs x86` (or `Google APIs` для уровня пакета SDK (в зависимости от вашей платформы разработки).

 
8.  В разделе **Библиотека** выберите **Добавить** и выберите проект служб Google Play (*google-play-services-lib*) и нажмите кнопку **ОК**.

9. Нажмите кнопку **Применить**, а затем кнопку **ОК**.




<!--HONumber=45--> 
