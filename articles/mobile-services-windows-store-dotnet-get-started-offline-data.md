
<properties linkid="develop-mobile-tutorials-get-started-offline-data-dotnet" urlDisplayName="Приступая к работе с автономными данными" pageTitle="Приступая к работе с автономными данными в мобильных службах (Магазин Windows) | Центр разработчиков для мобильных служб" metaKeywords="" description="Узнайте, как начать применять автономные данные в приложении Магазина Windows" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Приступая к работе с автономными данными в мобильных службах" authors="wesmc" />

# Приступая к работе с автономными данными в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Магазин Windows C#" class="current">Магазин Windows C#</a>
</div>

В этом разделе показано, как использовать автономные возможности мобильных служб Azure. Автономные функции мобильных служб Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Автономные функции позволяют синхронизировать локальные изменения с мобильной службой после подключения к сети. 

В этом учебнике вы обновите приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными], для поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Windows Azure для просмотра изменений, внесенных в данные после запуска приложения.


>[WACOM.NOTE] Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Windows Azure для хранения и извлечения данных из приложения Магазина Windows. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].

В этом учебнике рассматриваются следующие основные действия:

1. [Обновление приложения для поддержки автономных функций]
2. [Тестирование приложения в автономном сценарии] 
3. [Обновление приложения для повторного подключения мобильной службы]
4. [Тестирование приложения, подключенного к мобильной службе]

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Завершение учебника [Приступая к работе с мобильными службами] или [Приступая к работе с данными].
* Пакет SDK NuGet для мобильных служб Windows Azure версии 1.3.0-alpha
* Пакет SQLite Store NuGet для мобильных служб Windows Azure версии 0.1.0-alpha 
* SQLite для Windows 8.1

>[WACOM.NOTE] Для работы с этим учебником необходимо использовать учетную запись Windows Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Подробные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатное использование Windows Azure</a>. 

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Автономные функции мобильных служб Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Для использования этих возможностей в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальном хранилище. Затем вы ссылаетесь на таблицу с помощью интерфейса `IMobileServiceSyncTable`.

В этом разделе SQLite используется как локальное хранилище для автономных функций.

>[WACOM.NOTE] Этот раздел можно пропустить и просто загрузить версию проекта "Приступая к работе", который уже содержит поддержку автономного режима.  Сведения о загрузке проекта с поддержкой автономных функций см. в разделе [Приступая к работе с автономными функциями, пример].


1. Установите SQLite. Компонент можно установить непосредственно по ссылке [SQLite для Windows 8.1].

    >[WACOM.NOTE] Если вы используете Internet Explorer, после перехода по ссылке для установки SQLite может потребоваться загрузить VSIX-файл как ZIP-файл. Сохраните файл в папку на жестком диске с расширением .vsix вместо .zip. Дважды щелкните VSIX-файл в проводнике Windows для запуска установки.

2. В Visual Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. Добавьте ссылку на **расширение Windows** в **среду выполнения SQLite для Windows (Windows 8.1)**. 

    ![][1]

    >[WACOM.NOTE] Для среды выполнения SQLite требуется изменить архитектуру процессора проекта на **x86**, **x64** или **ARM**. Параметр **Любой процессор** не поддерживается.

3. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект клиентского приложения и выберите пункт **Управление пакетами NuGet**, чтобы открыть диспетчер пакетов NuGet. Выполните поиск **SQLiteStore** для установки пакета **WindowsAzure.MobileServices.SQLiteStore**.

    ![][2]

4. В обозревателе решений Visual Studio откройте файл MainPage.xaml.cs. Добавьте в начало файла следующие инструкции using.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

5. В файле Mainpage.xaml.cs замените объявление `todoTable` на объявление типа `IMobileServicesSyncTable`, которое инициализируется при вызове метода `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

6. В файле MainPage.xaml.cs добавьте в класс `TodoItem` системное свойство **Version**, как показано ниже.

        public class TodoItem
        {
          public string Id { get; set; }
          [JsonProperty(PropertyName = "text")]
          public string Text { get; set; }
          [JsonProperty(PropertyName = "complete")]
          public bool Complete { get; set; }
          [Version]
          public string Version { get; set; }
        }


7. В файле MainPage.xaml.cs измените обработчик событий `OnNavigatedTo` так, чтобы он инициализировал контекст синхронизации клиента с хранилищем SQLite. Хранилище SQLite создается с таблицей, соответствующей схеме таблицы мобильной службы, но оно должно содержать системное свойство **Version**, добавленное на предыдущем шаге.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync12.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
            }
            RefreshTodoItems();
        }

8. В обозревателе решений Visual Studio откройте файл MainPage.xaml. Найдите элемент Grid, содержащий элемент StackPanel с именем **Query and Update Data**. Добавьте следующий код пользовательского интерфейса, который заменяет элементы из определений строк до открывающего тега элемента управления ListView. 

    Этот код добавляет определения строк и столбцов в таблицу для размещения элементов. Он также добавляет две кнопки с обработчиком события нажатия для операций **Push** и **Pull**. Кнопки расположены над элементом `ListView` с именем ListItems. Сохраните файл.

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <StackPanel Grid.Row="0" Grid.ColumnSpan="2">
            <local:QuickStartTask Number="2" Title="Query, Update, and Synchronize Data" 
              Description="Use the Pull and Push buttons to synchronize the local store with the server" />
        </StackPanel>
        <Button Grid.Row="1" Grid.Column="0" Margin="5,5,0,0" Name="ButtonPush"
            Click="ButtonPush_Click" Width="80" Height="34">⬆ Push</Button>
        <Button Grid.Row="1" Grid.Column="1" Margin="5,5,0,0"  Name="ButtonPull" 
            Click="ButtonPull_Click" Width="80" Height="34">⬇ Pull</Button>
        <ListView Name="ListItems" SelectionMode="None" Margin="0,10,0,0" Grid.ColumnSpan="2" Grid.Row="2">
        


9. В файле MainPage.xaml.cs добавьте обработчики событий нажатия для кнопок **Push** и **Pull** и сохраните файл.

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync();
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageDialog d = new MessageDialog("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
                await d.ShowAsync();
            }
        }
        private async void ButtonPush_Click(object sender, RoutedEventArgs e)
        {
            string errorString = null;
            try
            {
                await App.MobileService.SyncContext.PushAsync();
                RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count() + ", message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message;
            }
            if (errorString != null) {
                MessageDialog d = new MessageDialog(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
                await d.ShowAsync();
            }
        }

10. Пока не запускайте приложение. Нажмите клавишу **F7** для повторного построения проекта. Убедитесь, что ошибок нет.


## <a name="test-offline-app"></a>Тестирование приложения в автономном сценарии

В этом разделе вы разорвете связь приложения с мобильной службой для имитации автономного сценария. Затем вы добавите некоторые элементы данных, которые будут храниться в локальном хранилище.

Обратите внимание, что в этом разделе приложение не должно быть подключено к мобильной службе. Поэтому кнопки **Push** и **Pull** будут вызывать исключения при их нажатии. В следующем разделе вы подключите это клиентское приложение к мобильной службе еще раз, чтобы проверить операции **Push** и **Pull** для синхронизации хранилища с базой данных мобильной службы.


1. В обозревателе решений Visual Studio откройте файл App.xaml.cs. Измените инициализацию **MobileServiceClient** на недопустимый адрес, заменив "**azure-mobile.net**" на "**azure-mobile.xxx**" для вашего URL-адреса. Затем сохраните файл.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Введите новый элемент списка дел и нажмите кнопку **Сохранить**. Новые элемент списка дел существует только в локальном хранилище, пока он не будет перемещен в мобильную службу. Клиентское приложение ведет себя так, если бы оно было подключено к мобильной службе, поддерживающей все операции создания, чтения, обновления и удаления (CRUD).

    ![][4]

3. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения мобильной службы

В этом разделе вы повторно подключите приложение к мобильной службе. Эта процедура имитирует переход приложения из автономного состояния в оперативное состояние с мобильной службой.


1. В обозревателе решений Visual Studio откройте файл App.xaml.cs. Измените инициализацию **MobileServiceClient** на допустимый адрес, заменив "**azure-mobile.xxx**" на "**azure-mobile.net**" для вашего URL-адреса. Затем сохраните файл.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Тестирование приложения, подключенного к мобильной службе


В этом разделе вы протестируете операции передачи и извлечения данных для синхронизации локального хранилища с базой данных мобильной службы.

1. В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Обратите внимание, что данные выглядят так же, как в автономном сценарии, хотя приложение теперь подключено к мобильной службе. Это вызвано тем, что это приложение всегда работает с объектом `IMobileServiceSyncTable`, который указывает на локальное хранилище.

    ![][5]

2. Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы. При использовании серверной части .NET для мобильной службы можно нажать кнопку **Управление** для вашей базы данных в расширении SQL Azure, чтобы выполнить запрос к таблице.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

    ![][6]

3. В приложении нажмите кнопку **Push**. После этого приложение вызовет методы `MobileServiceClient.SyncContext.PushAsync` и `RefreshTodoItems` для обновления приложения элементами из локального хранилища. Эта операция приводит к тому, что база данных мобильной службы получает данные из хранилища. Однако локальное хранилище не получает элементы из базы данных мобильной службы.

    Операция push выполняется в `MobileServiceClient.SyncContext`, а не в `IMobileServicesSyncTable`, и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.

    ![][7]

4. В локальном хранилище приложения есть несколько новых элементов.

    ![][8]

5. На этот раз нажмите кнопку **Pull** в приложении. Приложение вызывает только методы `IMobileServiceSyncTable.PullAsync()` и `RefreshTodoItems`.  Обратите внимание, что все данные из базы данных мобильной службы были извлечены в локальное хранилище и показаны в приложении. Кроме того, обратите внимание, что все данные в локальном хранилище также были переданы в базу данных мобильной службы. Это происходит, потому что **операции извлечения всегда предшествует операция передачи**.    
 
    ![][9]

    ![][10] 
  

##Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс `IMobileServiceSyncTable` и инициализировали `MobileServiceClient.SyncContext` с локальным хранилищем. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Когда мы хотели синхронизировать локальное хранилище с сервером, мы использовали методы `IMobileServiceSyncTable.PullAsync` и `MobileServiceClient.SyncContext.PushAsync`.

* Для отправки изменений на сервер мы вызвали метод `IMobileServiceSyncContext.PushAsync()`. Он является членом объекта `IMobileServicesSyncContext`, а не таблицы синхронизации, поскольку изменения будут переданы во все таблицы:

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CRUD), будут отправлены на сервер.
   
* Для извлечения данных из таблицы на сервере в приложение мы вызвали метод `IMobileServiceSyncTable.PullAsync`.

    Операции извлечения всегда предшествует операция передачи.  

    Также существуют перегрузки метода **PullAsync()**, позволяющие указать запрос. Обратите внимание, что в предварительной версии автономных функций мобильных служб метод **PullAsync** будет считывать все строки в соответствующей таблице (или запросе), а не будет пытаться прочитать только те строки, которые появились, например, после последней синхронизации. Если строки уже есть в локальной таблице синхронизации, они останутся без изменений.


## Дальнейшие действия

* [Обработка конфликтов с автономной поддержкой мобильных служб]

<!-- Anchors. -->
[Обновление приложения для поддержки автономных функций]: #enable-offline-app
[Тестирование приложения в автономном сценарии]: #test-offline-app
[Обновление приложения для повторного подключения мобильной службы]: #update-online-app
[Тестирование приложения, подключенного к мобильной службе]: #test-online-app
[Дальнейшие действия]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png


<!-- URLs. -->
[Обработка конфликтов с автономной поддержкой мобильных служб]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Приступая к работе с автономными функциями, пример]: http://go.microsoft.com/fwlink/?LinkId=394777
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[SQLite для Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776



