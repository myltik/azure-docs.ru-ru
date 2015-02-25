<properties pageTitle="Обработка конфликтов с автономными данными в мобильных службах (Магазин Windows) | Центр мобильных разработок" description="Узнайте, как использовать возможности мобильных служб Azure для обработки конфликтов при синхронизации автономных данных в приложении магазина Windows." documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="wesmc"/>


# Обработка конфликтов синхронизации автономных данных в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-offline-conflicts](../includes/mobile-services-selector-offline-conflicts.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показывается, как синхронизировать данные и обрабатывать конфликты при использовании возможностей автономной работы мобильных служб Azure.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">14:36</span></div>
</div>

В этом учебнике будут загружаться решения Windows Universal C# для приложения, которое поддерживает обработку конфликтов автономной синхронизации. Можно интегрировать мобильную службу с приложением и запустить клиенты Магазина Windows 8.1 и Windows Phone 8.1 для формирования конфликта синхронизации и его устранения.

Этот учебник основан на инструкциях и примере приложения предыдущего учебника [Приступая к работе с автономными данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с автономными данными].


В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows] 
2. [Тестирование приложения с помощью мобильной службы]
3. [Ручное обновление данных в серверной части для создания конфликта]

Для этого учебника требуется среда Visual Studio 2013 в Windows 8.1.


## <a name="download-app"></a>Загрузка примера проекта

![][0]

В этом учебнике приведен пример того, как [образец мобильных служб TODO в автономном режиме] обрабатывает конфликты синхронизации между автономным локальным хранилищем и базой данных мобильной службы в Azure.

1. Загрузите ZIP-файл для [репозитория образцов для мобильных служб Github] и извлеките его в рабочий каталог. 

2. Если вы еще не установили SQLite для Windows 8.1 и Windows Phone 8.1, как было указано в учебнике [Приступая к работе с автономными данными], установите обе эти среды выполнения.

3. Откройте в Visual Studio 2013 файл решения *mobile-services-samples\TodoOffline\WindowsUniversal\TodoOffline-Universal.sln*. Нажмите клавишу **F5**, чтобы перестроить и запустить проект. Убедитесь, что пакеты NuGet восстанавливаются, а ссылки заданы правильно.

    >[AZURE.NOTE] Может потребоваться удалить все старые ссылки на среду выполнения SQLite и заменить их обновленными, как упоминалось в учебнике [Приступая к работе с автономными данными].

4. В приложении введите какой-либо текст в поле **Insert a TodoItem** (Вставка TodoItem), а затем нажмите кнопку **Сохранить**, чтобы добавить некоторые элементы todo в локальное хранилище. После этого закройте приложение.

Обратите внимание, что приложение еще не подключено к мобильной службе, поэтому кнопки **Push** и **Pull** будут вызывать исключения.




## <a name="test-app"></a>Тестирование приложения с помощью мобильной службы

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

В реальной ситуации конфликт синхронизации происходит, когда одно приложение отправляет обновления записи в базе данных, а затем другое приложение пытается отправить изменение в ту же запись, используя устаревшую версию поля в этой записи. Как было указано в разделе [Приступая к работе с автономными данными], системное свойство версии необходимо для поддержки функций автономной синхронизации. Эти сведения проверяются при каждом обновлении базы данных. Если экземпляр приложения пытается обновить запись с помощью устаревшей версии, возникает конфликт, который перехватывается в приложении как `MobileServicePreconditionFailedException`. Если приложение не перехватывает `MobileServicePreconditionFailedException`, то в результате возникает `MobileServicePushFailedException`, показывающее число выявленных ошибок синхронизации.

>[AZURE.NOTE] Для поддержки синхронизации удаленных записей при синхронизации автономных данных необходимо включить поддержку [удаления с возможностью восстановления](/ru-ru/documentation/articles/mobile-services-using-soft-delete/). В противном случае придется вручную удалять записи в локальном хранилище или вызвать функцию `IMobileServiceSyncTable::PurgeAsync()` для очистки хранилища.


Следующие шаги показывают, как клиенты Windows Phone 8.1 и Windows Store 8.1, выполняемые одновременно, пытаются создать и устранить конфликт, используя образец.

1. В Visual Studio щелкните правой кнопкой мыши проект Windows Phone 8.1 и выберите **Назначить запускаемым проектом**. Нажмите клавиши **CTRL+F5** для запуска клиента Windows Phone 8.1 без отладки. После установки клиента Windows Phone 8.1 и запуска его в эмуляторе нажмите кнопку **Получить** для синхронизации локального хранилища с текущим состоянием базы данных.
 
    ![][3]
 
   
2. В Visual Studio щелкните правой кнопкой мыши проект среды выполнения Windows 8.1 и выберите **Назначить запускаемым проектом**, чтобы снова установить его в качестве запускаемого проекта. Нажмите клавишу **F5**, чтобы запустить его. После установки и запуска клиента Windows Phone 8.1 и нажмите кнопку **Получить** для синхронизации локального хранилища с текущим состоянием базы данных.

    ![][4]
 
3. На этом этапе оба клиента синхронизированы с базой данных. Код обоих клиентов также использует добавочную синхронизацию таким образом, что будет синхронизироваться только с неполным списком элементов todo. Завершенные элементы списка todo будут игнорироваться. Выберите один из элементов и измените текст одного и того же элемента в обоих клиентах на другое значение. Нажмите кнопку **Отправить** для синхронизации изменений с базой данных на сервере.

    ![][5]

    ![][6]


4. Клиент, push-уведомление которого было выполнено последним, выявляет конфликт и позволяет пользователю решить, какое значение зафиксировать в базе данных. Исключение содержит правильное значение версии, которое используется для разрешения конфликта.

    ![][7]



## Проверка кода для обработки конфликтов синхронизации

Для использования автономных функций мобильных служб необходимо включать столбец версии в локальную базу данных и в объект передачи данных. Это достигается путем обновления в классе `TodoItem` следующего члена:

        [Version]
        public string Version { get; set; }

Столбец "__version" включается в локальную базу данных в методе  `OnNavigatedTo()`, если класс `TodoItem` используется для определения локального хранилища.

Для обработки конфликтов автономной синхронизации в коде создайте класс, реализующий интерфейс `IMobileServiceSyncHandler`. Передайте объект данного типа в вызов метода `MobileServiceClient.SyncContext.InitializeAsync()`. Это также происходит в методе `OnNavigatedTo()` образца.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

Класс `SyncHandler` в **SyncHandler.cs** реализует `IMobileServiceSyncHandler`. Метод `ExecuteTableOperationAsync` вызывается при каждой передаче операции принудительной отправки на сервер. Если возникает исключение типа `MobileServicePreconditionFailedException`, это означает, что возник конфликт между локальной и удаленной версиями элемента.

Для разрешения конфликта в пользу локального элемента следует просто повторить операцию. После возникновения конфликта локальная версия элемента будет обновлена в соответствии с версией на сервере, поэтому повторное выполнение операции приведет к замене изменений на сервере на локальные изменения:

    await operation.ExecuteAsync(); 

Для разрешения конфликта в пользу серверного элемента следует просто выйти из метода `ExecuteTableOperationAsync`. Локальная версия объекта будет удалена и заменена значением с сервера.

Чтобы остановить операцию принудительной отправки (но сохранить изменения в очереди), используйте метод `AbortPush()`:

    operation.AbortPush();

При этом текущая операция принудительной отправки будет остановлена, но все ожидающие изменения будут сохранены, в том числе текущая операция, если метод `AbortPush` вызывается из `ExecuteTableOperationAsync`. При следующем вызове `PushAsync()` эти изменения будут отправлены на сервер. 

При отмене операции принудительной отправки метод `PushAsync` вызовет исключение `MobileServicePushFailedException`, а свойство исключения `PushResult.Status` будет иметь значение `MobileServicePushStatus.CancelledByOperation`. 


<!-- Anchors. -->
[Загрузка проекта приложения Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Добавление столбца даты выполнения для базы данных]: #add-column
[Обновление базы данных для серверных мобильных служб .NET]: #dotnet-backend  
[Обновление базы данных для мобильных служб JavaScript]: #javascript-backend
[Тестирование приложения с помощью мобильной службы]: #test-app
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
[Репозиторий образцов для мобильных служб Github]: http://go.microsoft.com/fwlink/?LinkId=512865
[Образец мобильных служб TODO в автономном режиме]: http://go.microsoft.com/fwlink/?LinkId=512866


<!--HONumber=42-->
