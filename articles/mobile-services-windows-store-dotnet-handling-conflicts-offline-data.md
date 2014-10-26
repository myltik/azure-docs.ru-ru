<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Обработка конфликтов синхронизации автономных данных в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Магазин Windows C#" class="current">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показывается, как синхронизировать данные и обрабатывать конфликты при использовании возможностей автономной работы мобильных служб Azure.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">14:36:00</span></div>

</div>

В этом учебнике вам предстоит загрузить приложение, которое поддерживает автономные и оперативные данные, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра и обновления базы данных в ходе выполнения приложения.

Этот учебник основан на инструкциях и примере приложения предыдущего учебника [Приступая к работе с автономными данными][Приступая к работе с автономными данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с автономными данными][Приступая к работе с автономными данными].

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения для Магазина Windows][Загрузка проекта приложения для Магазина Windows]
2.  [Добавление столбца даты выполнения для базы данных][Добавление столбца даты выполнения для базы данных]
  * [Обновление базы данных для серверных мобильных служб .NET][Обновление базы данных для серверных мобильных служб .NET] 
  * [Обновление базы данных для мобильных служб JavaScript][Обновление базы данных для мобильных служб JavaScript]  
3.  [Тестирование приложения с мобильной службой][Тестирование приложения с мобильной службой]
4.  [Ручное обновление данных в серверной части для создания конфликта][Ручное обновление данных в серверной части для создания конфликта]

Для этого учебника требуется среда Visual Studio 2013 в Windows 8.1.

## <a name="download-app"></a>Загрузка примера проекта

![][]

Этот учебник построен на [примере кода обработки конфликтов][примере кода обработки конфликтов], который является проектом приложения для Магазина Windows в Visual Studio 2013. Пользовательский интерфейс этого приложения аналогичен приложению в учебнике [Приступая к работе с автономными данными][Приступая к работе с автономными данными] за исключением того, что для каждого элемента TodoItem имеется новый столбец даты.

1.  Скачайте [пример кода обработки конфликтов][примере кода обработки конфликтов] на C#.

2.  Установите [SQLite для Windows Phone 8.1][SQLite для Windows Phone 8.1], если этот компонент еще не установлен.

3.  В Visual Studio 2013 откройте загруженный проект. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4.  В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить** Вы также можете изменить дату выполнения добавляемых элементов списка дел.

Обратите внимание, что приложение еще не подключено к мобильной службе, поэтому кнопки **Push** и **Pull** будут вызывать исключения.

## <a name="add-column"></a>Добавление столбца в модель данных

В этом разделе вы обновите базу данных, чтобы добавить таблицу TodoItem со столбцом даты выполнения в мобильную службу. Приложение позволяет изменить дату выполнения для элемента во время выполнения, чтобы создавать конфликты синхронизации в следующем разделе данного учебника.

Класс `TodoItem` в примере задан в файле MainPage.xaml.cs. Обратите внимание, что класс содержит следующий атрибут, который используется для операций синхронизации в этой таблице.

        [DataTable("TodoWithDate")]

Добавьте эту таблицу в базу данных.

### <a name="dotnet-backend"></a>Обновление базы данных для серверных мобильных служб .NET

Если вы используете серверную часть .NET для мобильной службы, выполните следующие действия для обновления схемы базы данных.

1.  Откройте проект серверной мобильной службы .NET в Visual Studio.
2.  В обозревателе решений в Visual Studio в проекте службы разверните папку **Модели** и откройте файл ToDoItem.cs. Добавьте свойство `DueDate` как показано далее.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }

3.  В обозревателе решений в Visual Studio разверните папку **App\_Start** и откройте файл WebApiConfig.cs.

    В файле WebApiConfig.cs обратите внимание, что класс инициализатора базы данных по умолчанию является производным от класса `DropCreateDatabaseIfModelChanges`. Это означает, что любое изменение модели приведет в таблице к его удалению и повторному созданию в соответствии с новой моделью. Таким образом данные в таблице будут потеряны, а таблица будет повторно заполнена. Измените метод Seed инициализатора базы данных так, чтобы инициализация `Seed()` функционировала следующим образом для инициализации нового столбца DueDate. Сохраните файл WebApiConfig.cs.

    > [WACOM.NOTE] Если используется инициализатор базы данных по умолчанию, Entity Framework будет удалять и заново создавать базу данных всякий раз при обнаружении изменения модели данных в определении модели Code First. Чтобы сделать это изменение модели данных и сохранить существующие данные в базе данных, необходимо использовать Code First Migrations. Дополнительные сведения см. в разделе [Как использовать Code First Migrations для обновления модели данных][Как использовать Code First Migrations для обновления модели данных].

        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  В обозревателе решений в Visual Studio разверните папку **Контроллеры** и откройте файл ToDoItemController.cs. Переименуйте класс `TodoItemController` в `TodoWithDateController`. Это приведет к изменению конечной точки REST для табличных операций.

        public class TodoWithDateController : TableController<TodoItem>

5.  В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект мобильной службы для .NET и выберите пункт**Опубликовать** для публикации изменений.

### <a name="javascript-backend"></a>Обновление базы данных для серверных мобильных служб JavaScript

Для мобильных служб, использующих серверную часть JavaScript, вы добавите новую таблицу с именем **TodoWithDate**. Для добавления таблицы **TodoWithDate** для мобильных служб, использующих серверную часть JavaScript, выполните следующие действия.

1.  Выполните вход на [портал управления Azure][портал управления Azure].

2.  Перейдите на вкладку **Данные** мобильной службы.

3.  Щелкните **Создать** в нижней части страницы, а затем создайте новую таблицу с именем **TodoWithDate**.

## <a name="test-app"></a>Тестирование приложения с мобильной службой

Теперь пришло время проверить работу приложения с мобильными службами.

1.  На портале управления Azure найдите ключ приложения мобильной службы, нажав кнопку **Управление ключами** в панели команд на вкладке **Панель мониторинга**. Скопируйте **ключ приложения**.

2.  В обозревателе решений в Visual Studio откройте файл App.xaml.cs в проекте примера клиентского приложения. Добавьте в инициализацию **MobileServiceClient** URL-адрес вашей мобильной службы и ключ приложения:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3.  В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение.

4.  Как и ранее, введите текст в текстовом поле и нажмите кнопку **Сохранить**. При этом данные сохраняются в локальной таблице синхронизации, но не на сервере.

    ![][]

5.  Для просмотра текущего состояния базы данных войдите на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы** и выберите вашу мобильную службу.

  * Если используется серверная часть JavaScript для мобильной службы, перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoWithDate**. Нажмите кнопку **Обзор** чтобы убедиться, что таблица по-прежнему пуста, так как мы не передали изменения из приложения на сервер.

        ![][1]

  * Если используется серверная часть .NET для мобильной службы, перейдите на вкладку **Настройка**, а затем щелкните базу данных SQL. Нажмите кнопку **Управление** в нижней части экрана, чтобы войти на портал управления SQL Azure для просмотра базы данных путем запуска запроса SQL, подобного следующему запросу.

            SELECT * FROM todolist.todowithdate
        ![][2]

1.  Вернитесь в приложение и нажмите кнопку **Отправить**.

2.  На портале управления щелкните кнопку **Обновись** в таблице **TodoItem**. Теперь вы увидите данные, введенные в приложении.

    ![][1]

## <a name="handle-conflict"></a>Обновление данных в серверной части для создания конфликта

В реальной ситуации конфликт синхронизации происходит, когда одно приложение отправляет обновления записи в базе данных, а затем другое приложение пытается отправить изменение в ту же запись, используя устаревшую версию поля в этой записи. Если экземпляр приложения пытается обновить ту же запись без извлечения обновленной записи, возникает конфликт, который будет зафиксирован как исключение `MobileServicePreconditionFailedException` в приложении.

Чтобы развернуть приложение на другом компьютере для запуска двух экземпляров приложения и создания конфликта, необходимо выполнить инструкции по развертыванию в учебнике [Обработка конфликтов базы данных][Обработка конфликтов базы данных].

Ниже показано, как обновить базу данных в Visual Studio, чтобы вызвать конфликт.

1.  В Visual Studio запустите обозреватель сервера и подключитесь к учетной записи Azure. Разверните узел **Базы данных SQL** для учетной записи Azure.

    ![][2]

2.  Щелкните базу данных SQL правой кнопкой мыши и выберите пункт **Открыть в обозревателе объектов SQL Server**.
3.  В обозревателе объектов SQL Server разверните базу данных и узел **Таблицы**. Щелкните правой кнопкой мыши таблицу **TodoWithDate** и выберите **Просмотреть данные**.

4.  Задайте в поле **complete** для одного из элементов значение True.

    ![][3]

5.  Вернитесь в приложение Todo и измените тот же элемент, который вы изменили в базе данных.

    ![][4]

6.  Нажмите кнопку **Отправить**. Появится диалоговое окно с вопросом о том, как разрешить конфликт. Выберите один из способов разрешения конфликта.

    ![][5]

## Проверка кода для обработки конфликтов синхронизации

Чтобы настроить автономный компонент для обнаружения конфликтов, необходимо добавить столбец версии в локальную базу данных и в объект передачи данных. Класс `TodoItem` имеет следующий элемент:

        [Version]
        public string Version { get; set; }

Столбец `__version` также указан в локальной базе данных в методе `OnNavigatedTo()`.

Для обработки конфликтов автономной синхронизации в коде создайте класс, реализующий интерфейс `IMobileServiceSyncHandler`. Передайте объект данного типа в вызове метода `InitializeAsync`:

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

Класс `SyncHandler` в файле **MainPage.xaml.cs** реализует `IMobileServiceSyncHandler`. Метод `ExecuteTableOperationAsync` вызывается при каждой отправке операции push на сервер. Если возникает исключение типа `MobileServicePreconditionFailedException`, это означает, что возник конфликт между локальной и удаленной версиями элемента.

Для разрешения конфликта в пользу локального элемента следует просто повторить операцию. После возникновения конфликта локальная версия элемента будет обновлена в соответствии с версией на сервере, поэтому повторное выполнение операции приведет к замене изменений на сервере на локальные изменения:

    await operation.ExecuteAsync(); 

Для разрешения конфликта в пользу серверного элемента следует просто выйти из метода `ExecuteTableOperationAsync`. Локальная версия объекта будет удалена и заменена значением с сервера.

Чтобы остановить операцию push (но сохранить изменения в очереди), используйте метод `AbortPush()`:

    operation.AbortPush();

При этом текущая операция push будет остановлена, но все ожидающие изменения будут сохранены, в том числе текущая операция, если метод `AbortPush` вызывается из `ExecuteTableOperationAsync`. При следующем вызове `PushAsync()` эти изменения будут отправлены на сервер.

При отмене операции push метод `PushAsync` вызовет исключение `MobileServicePushFailedException`, а свойство исключения `PushResult.Status` будет иметь значение `MobileServicePushStatus.CancelledByOperation`.

<!-- Anchors. Images  URLs -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data "Магазин Windows C#"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data "Windows Phone"
  [Просмотр учебника]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services
  [Приступая к работе с автономными данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
  [Загрузка проекта приложения для Магазина Windows]: #download-app
  [Добавление столбца даты выполнения для базы данных]: #add-column
  [Обновление базы данных для серверных мобильных служб .NET]: #dotnet-backend
  [Обновление базы данных для мобильных служб JavaScript]: #javascript-backend
  [Тестирование приложения с мобильной службой]: #test-app
  [Ручное обновление данных в серверной части для создания конфликта]: #handle-conflict
  []: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
  [примере кода обработки конфликтов]: http://go.microsoft.com/fwlink/?LinkId=394787
  [SQLite для Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
  [Как использовать Code First Migrations для обновления модели данных]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
  [портал управления Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
  [Обработка конфликтов базы данных]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app
  [2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png
  [3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png
  [4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png
  [5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png
