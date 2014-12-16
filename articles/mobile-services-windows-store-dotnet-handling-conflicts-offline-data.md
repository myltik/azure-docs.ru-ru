<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Обработка конфликтов с автономными данными в мобильных службах (Windows Store) | Центр мобильных разработок" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="wesmc" />


# Обработка конфликтов синхронизации автономных данных в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#" class="current">C# в Магазине Windows</a>
<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ru-ru/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS">iOS</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показывается, как синхронизировать данные и обрабатывать конфликты при использовании возможностей автономной работы мобильных служб Azure.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">14:36:00</span></div>
</div>

В этом учебнике будут загружаться решения Windows Universal C# для приложения, которое поддерживает обработку конфликтов автономной синхронизации. Можно интегрировать мобильную службу с приложением и запустить клиенты Магазина Windows 8.1 и Windows Phone 8.1 для формирования конфликта синхронизации и его устранения.

Этот учебник основан на инструкциях и примере приложения предыдущего учебника [Приступая к работе с автономными данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с автономными данными].


В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows] 
2. [Тестирование приложения с мобильной службой]
3. [Ручное обновление данных в серверной части для создания конфликта]

Для этого учебника требуется среда Visual Studio 2013 в Windows 8.1.


## <a name="download-app"></a>Загрузка примера проекта

![][0]

В этом учебнике приведен пример того, как образец [Todo Offline Mobile Services] обрабатывает конфликты синхронизации между автономным локальным хранилищем и базой данных мобильной службы в Azure.

1. Загрузите ZIP-файл для [Mobile Services Samples Github Repository] и извлеките его в рабочий каталог. 

2. Если вы еще не установили SQLite для Windows 8.1 и Windows Phone 8.1, как упоминалось в разделе учебника [Приступая к работе с автономными данными], установите обе среды выполнения.

3. В Visual Studio 2013 откройте файл решения *mobile-services-samples\TodoOffline\WindowsUniversal\TodoOffline-Universal.sln*. Нажмите клавишу **F5** для повторного построения и запуска проекта. Убедитесь, что пакеты NuGet восстанавливаются и ссылки заданы правильно.

    >[AZURE.NOTE] Может потребоваться удалить все старые ссылки на среду выполнения SQLite и заменить их обновленными, как упоминалось в разделе учебника [Приступая к работе с автономными данными].

4. В приложении введите какой-либо текст в инструкции **Insert a TodoItem**, а затем нажмите кнопку **Сохранить**, чтобы добавить некоторые элементы todo в локальное хранилище. Затем закройте приложение.

Обратите внимание, что приложение еще не подключено к мобильной службе, поэтому кнопки **Push** и **Pull** будут вызывать исключения.




## <a name="test-app"></a>Тестирование приложения с мобильной службой

Теперь пришло время проверить работу приложения с мобильными службами.

1. На портале управления Azure найдите ключ приложения мобильной службы, нажав кнопку **Управление ключами** в панели команд на вкладке **Панель мониторинга**. Скопируйте **ключ приложения**.

2. В обозревателе решений в Visual Studio откройте файл App.xaml.cs в проекте примера клиентского приложения. Добавьте в инициализацию **MobileServiceClient** URL-адрес вашей мобильной службы и ключ приложения:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. В Visual Studio нажмите клавишу **F5**, чтобы построить и повторно запустить приложение.

    ![][0]


## <a name="handle-conflict"></a>Обновление данных в серверной части для создания конфликта

В реальной ситуации конфликт синхронизации происходит, когда одно приложение отправляет обновления записи в базе данных, а затем другое приложение пытается отправить изменение в ту же запись, используя устаревшую версию поля в этой записи. Как было сказано в разделе [Приступая к работе с автономными данными], свойство системной версии необходимо для поддержки автономных функций синхронизации. Эти сведения проверяются при каждом обновлении базы данных. Если экземпляр приложения пытается обновить запись с помощью устаревшей версии, возникает конфликт и выявляется, как определить `MobileServicePreconditionFailedException` в приложении. Если приложение не перехватывает `MobileServicePreconditionFailedException`, то в результате будет сформировано `MobileServicePushFailedException`, показывающее число выявленных ошибок синхронизации.

>[AZURE.NOTE] Для поддержки синхронизации удаленных записей с автономными данными следует включить[Удаление с возможностью восстановления](/ru-ru/documentation/articles/mobile-services-using-soft-delete/). В противном случае необходимо вручную удалить записи в локальном хранилище или вызвать `IMobileServiceSyncTable::PurgeAsync()` для очистки локального хранилища.


Следующие шаги показывают, как клиенты Windows Phone 8.1 и Windows Store 8.1, выполняемые одновременно, пытаются создать и устранить конфликт, используя образец.

1. В Visual Studio щелкните правой кнопкой мыши проект Windows Phone 8.1 и выберите **Назначить запускаемым проектом**. Нажмите клавиши **Ctrl+F5** для запуска клиента Windows Phone 8.1 без отладки. После установки клиента Windows Phone 8.1 и запуска в эмуляторе нажмите кнопку **Pull**для синхронизации локального хранилища с текущим состоянием базы данных.
 
    ![][3]
 
   
2. В Visual Studio щелкните правой кнопкой мыши проект среды выполнения Windows 8.1 и щелкните **Назначить запускаемым проектом**, чтобы установить его обратно в запуске проекта. Нажмите клавишу **F5**, чтобы запустить его. После установки клиента Windows Phone 8.1 и запуска в эмуляторе нажмите кнопку **Pull**для синхронизации локального хранилища с текущим состоянием базы данных.

    ![][4]
 
3. На этом этапе оба клиента синхронизируются с базой данных. Код обоих клиентов также использует добавочную синхронизацию таким образом, что будет синхронизироваться только с неполным списком элементов todo. Завершенные элементы списка todo будут игнорироваться. Выберите один из элементов и измените текст одного и того же элемента в обоих клиентах на другое значение. Нажмите кнопку Push**Push** для синхронизации изменений с базой данных на сервере.

    ![][5]

    ![][6]


4. Клиент, push-уведомление которого было выполнено последним, выявляет конфликт и позволяет пользователю решить, какое значение зафиксировать в базе данных. Исключение содержит правильное значение версии, которое используется для разрешения конфликта.

    ![][7]



## Проверка кода для обработки конфликтов синхронизации

Для использования автономных функций мобильных служб необходимо включать столбец версии в локальную базу данных и в объект передачи данных. Это достигается путем обновления в классе "TodoItem" следующего члена:

        [Version]
        public string Version { get; set; }

Столбец `__version` включается в локальную базу данных в методе `OnNavigatedTo()`, если класс `TodoItem` используется для определения локального хранилища.

Для обработки конфликтов автономной синхронизации в коде создайте класс, реализующий интерфейс `IMobileServiceSyncHandler`. Передайте объект данного типа в вызове в `MobileServiceClient.SyncContext.InitializeAsync()`. Это также происходит в методе `OnNavigatedTo()` образца.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

Класс `SyncHandler` в файле **SyncHandler.cs** реализует `IMobileServiceSyncHandler`. Метод `ExecuteTableOperationAsync` вызывается при каждой отправке операции push на сервер. Если возникает исключение типа `MobileServicePreconditionFailedException`, это означает наличие конфликта между локальной и удаленной версиями элемента.

Для разрешения конфликта в пользу локального элемента следует просто повторить операцию. После возникновения конфликта локальная версия элемента будет обновлена в соответствии с версией на сервере, поэтому повторное выполнение операции приведет к замене изменений на сервере на локальные изменения:

    await operation.ExecuteAsync(); 

Для разрешения конфликта в пользу серверного элемента следует просто выйти из метода `ExecuteTableOperationAsync`. Локальная версия объекта будет удалена и заменена значением с сервера.

Чтобы остановить операцию push (но сохранить изменения в очереди), используйте метод `AbortPush()`:

    operation.AbortPush();

При этом текущая операция push будет остановлена, но все ожидающие изменения будут сохранены, в том числе текущая операция, если метод `AbortPush` вызывается из `ExecuteTableOperationAsync`. При следующем вызове метода `PushAsync()` эти изменения будут отправлены на сервер. 

При отмене операции push метод `PushAsync` создаст исключение `MobileServicePushFailedException`, а свойство `PushResult.Status` исключения будет иметь значение `MobileServicePushStatus.CancelledByOperation`. 


<!-- Anchors. -->
[Загрузка проекта приложения для Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Добавление столбца даты выполнения для базы данных]: #add-column
[Обновление базы данных для серверных мобильных служб .NET]: #dotnet-backend  
[Обновление базы данных для мобильных служб JavaScript]: #javascript-backend
[Тестирование приложения с мобильной службой]: #test-app
[Ручное обновление данных в серверной части для создания конфликта]: #handle-conflict
[Дальнейшие действия]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png




<!-- URLs -->
[Пример кода обработки конфликтов]: http://go.microsoft.com/fwlink/?LinkId=394787
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[Приступая к работе с автономными данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite для Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Портал управления Azure]: https://manage.windowsazure.com/
[Обработка конфликтов базы данных]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app
[Образцы репозиториев мобильных служб Github]: http://go.microsoft.com/fwlink/?LinkId=512865
[Образец мобильных служб TODO в автономном режиме]: http://go.microsoft.com/fwlink/?LinkId=512866
