<properties urlDisplayName="Using Offline Data" pageTitle="Использование автономных данных в мобильных службах (магазин Windows) | Центр мобильных разработок" metaKeywords="" description="Узнайте, как использовать мобильные службы Azure для кэширования и синхронизации автономных данных в приложении магазина Windows" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Использование синхронизации автономных данных в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить поддержку автономных функций в универсальное приложение для Магазина Windows с помощью мобильных служб Azure. Эта поддержка позволяет взаимодействовать с локальной базой данных при работе в автономном сценарии. Данные автономные функции позволяют синхронизировать локальные изменения с серверной базой данных после подключения к сети. 
</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">14:36</span></div>
</div>


Во время работы с этим учебником вы обновите универсальное приложение, созданное при выполнении заданий учебника [Приступая к работе с мобильными службами], чтобы добавить поддержку автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.


>[WACOM.NOTE] Этот учебник поможет вам разобраться, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных приложения для Магазина Windows. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].
>
>Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения можно найти на странице <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатное пробное использование Azure</a>. 
>
>Более ранняя версия учебника по Windows Phone 8 для Visual Studio 2012 доступна по этой ссылке: [Учебник по Windows Phone 8 для Visual Studio 2012].


В этом учебнике рассматриваются следующие основные действия:

1. [Обновление приложения для поддержки автономных функций]
2. [Тестирование приложения в автономном сценарии] 
3. [Обновление приложения для повторного подключения мобильной службы]
4. [Тестирование приложения, подключенного к мобильной службе]

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Выполнение заданий учебника [Приступая к работе с мобильными службами].
* [Пакет SDK для мобильных служб Azure версии 1.3.0 (или более поздних версий)][Nuget-пакет SDK для мобильных служб]
* [SQLite Store для мобильных служб Azure версии 1.0.0 (или более поздних версий)][Nuget-пакет SQLiteStore]
* SQLite для Windows 8.1

>[AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. О том, как это сделать, читайте в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатное пробное использование Azure</a>. 

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Автономные функции мобильных служб Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Для использования этих возможностей в приложении необходимо инициализировать MobileServiceClient.SyncContext в локальном хранилище и создать ссылку на таблицу с помощью интерфейса IMobileServiceSyncTable. В этом учебнике в качестве локального хранилища используется SQLite.

>[AZURE.NOTE] Этот раздел можно пропустить, просто загрузив версию проекта, в которой уже содержится поддержка автономного режима, из репозитория Github с примерами проектов для мобильных служб. Версию проекта с включенной поддержкой автономных функций можно найти по этой ссылке: [Пример автономного приложения TodoList].


1. Установите среду выполнения SQLite для Windows 8.1 и Windows Phone 8.1. 

    * **Среда выполнения Windows 8.1:** установите среду выполнения SQLite для Windows 8.1 с помощью этой ссылки: [SQLite для Windows 8.1].
    * **Windows Phone 8.1:** установите среду выполнения SQLite для Windows Phone 8.1 с помощью этой ссылки: [SQLite для Windows Phone 8.1].

    >[AZURE.NOTE] Если вы используете Internet Explorer, после перехода по ссылке для установки SQLite может потребоваться загрузить VSIX-файл в виде ZIP-файла. Сохраните файл в папку на жестком диске с расширением .vsix вместо .zip. Дважды щелкните VSIX-файл в проводнике Windows для запуска установки.

2. В Visual Studio откройте проект, созданный в ходе работы над учебником [Приступая к работе с мобильными службами]. В обозревателе решений щелкните правой кнопкой мыши элемент **Ссылки** для проектов на платформах среды выполнения Windows 8.1 и Windows Phone 8.1 и добавьте ссылку на элемент SQLite, расположенный в разделе **Расширения**. 

    ![][1]
    </br>**Среда выполнения Windows 8.1**

    ![][11]
    </br>**Windows Phone 8.1**

3. Для среды выполнения SQLite требуется указать один из этих типов процессорной архитектуры проекта: **x86**, **x64** или **ARM**. Параметр **Любой процессор** не поддерживается. В обозревателе решений Visual Studio щелкните элемент "Решение" в верхней части, а затем выберите в раскрывающемся списке для процессорной архитектуры один из поддерживаемых параметров, которые требуется протестировать.

    ![][13]

4. Установите NuGet-пакет **WindowsAzure.MobileServices.SQLiteStore** для проектов среды выполнения Windows 8.1 и Windows Phone 8.1.

    * **Windows 8.1:** В обозревателе решений щелкните правой кнопкой мыши проект Windows 8.1 и выберите пункт **Управление пакетами Nuget**, чтобы открыть диспетчер пакетов NuGet. Выполните поиск по запросу **SQLiteStore**, чтобы установить пакет WindowsAzure.MobileServices.SQLiteStore.
    * **Windows Phone 8.1:** Щелкните правой кнопкой мыши проект Windows Phone 8.1 и выберите пункт **Управление пакетами Nuget**, чтобы открыть диспетчер пакетов NuGet. Выполните поиск по запросу **SQLiteStore**, чтобы установить пакет WindowsAzure.MobileServices.SQLiteStore.     

    >[WACOM.NOTE] Если при установке создается ссылка на более раннюю версию SQLite, эту повторяющуюся ссылку можно просто удалить. 

    ![][2]

5. В обозревателе решений Visual Studio откройте файл MainPage.cs в общем проекте. Добавьте в начало файла следующие инструкции using.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. В общем файле Mainpage.cs замените объявление todoTable объявлением типа IMobileServicesSyncTable, которое инициализируется при вызове метода MobileServicesClient.GetSyncTable().

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. В общем файле MainPage.cs обновите обработчик событий OnNavigatedTo, чтобы он инициализировал контекст синхронизации клиента с хранилищем SQLite. Хранилище SQLite создается с таблицей, соответствующей схеме таблицы мобильной службы, и оно должно содержать системное свойство **Version**, которое вы создадите на следующем шаге.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
            await RefreshTodoItems();
        }


8. В обозревателе решений Visual Studio разверните в общем проекте элемент **DataModel** и откройте файл TodoItem.cs. Добавьте оператор using для пространства имен MobileServices. Это необходимо, чтобы разграничить атрибут version и системное свойство version.

        using Microsoft.WindowsAzure.MobileServices;

      Обновите класс TodoItem, чтобы он включал системное свойство **Version**, как показано ниже. Для поддержки автономных функций схема таблицы должна включать системное свойство **Version**, как показано ниже.

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




9. Теперь обновите пользовательский интерфейс проектов Windows 8.1 и Windows Phone 8.1, чтобы добавить кнопки для поддержки операций автономной синхронизации между локальной базой данных и базой данных мобильных служб в Azure. 

    * **Windows 8.1:** В обозревателе решений Visual Studio для проекта Windows 8.1 откройте файл MainPage.xaml. Найдите кнопку с именем **ButtonRefresh**. Замените этот элемент такой панелью стека кнопок: 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Download"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8.1:** В обозревателе решений Visual Studio откройте файл MainPage.xaml для проекта Windows Phone 8.1. Найдите кнопку с именем **ButtonRefresh**. Замените этот элемент такой панелью стека кнопок: 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Download"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. В общем файле MainPage.cs добавьте обработчики событий нажатия для кнопок **Push** и **Pull**. Затем сохраните файл. 
        
    Обратите внимание: исключение MobileServicePushFailedException может возникать как при операциях передачи, так и при операциях извлечения. При извлечении это возможно, поскольку в процессе этой операции происходит передача, необходимая для контроля синхронизации всех таблиц и их взаимосвязей. В следующем учебнике [Обработка конфликтов с автономной поддержкой мобильных служб] показано, как обрабатывать такие исключения, связанные с синхронизацией.

    Для поддержки синхронизации удаленных записей при синхронизации автономных данных необходимо включить поддержку [удаления с возможностью восстановления](/ru-ru/documentation/articles/mobile-services-using-soft-delete/). В противном случае придется вручную удалять записи в локальном хранилище или вызвать функцию IMobileServiceSyncTable::PurgeAsync() для очистки хранилища.

    В этом примере мы передаем запрос на вызов метода PullAsync для поддержки добавочной синхронизации. Это удобно в том случае, когда нецелесообразно извлекать всю таблицу при синхронизации. В этом сценарии нас не интересуют изменения текстовых значений элементов списка дел, нужно извлечь только завершенные дела, чтобы вычеркнуть их из списка.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // All items should be synced since other clients might mark an item as complete
                // The first parameter is a query ID that uniquely identifies the query.
                // This is used in incremental sync to get only newer items the next time PullAsync is called
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery());

                await RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Internal Push operation during pull request failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPull.IsEnabled = true; 
        }


        private async void ButtonPush_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Push is in progress
            // and visibly show the action is in progress. 
            ButtonPush.Focus(FocusState.Programmatic);
            ButtonPush.IsEnabled = false;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Push again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPush.IsEnabled = true;
        }

11. Соберите решение и убедитесь, что при этом ни в одном из проектов не возникло ошибок.


## <a name="test-offline-app"></a>Тестирование приложения в автономном сценарии

В этом разделе вы разорвете связь приложения с мобильной службой для имитации автономного сценария. Затем вы добавите некоторые элементы данных, которые будут храниться в локальном хранилище.

Обратите внимание, что в этом разделе приложение не должно быть подключено к мобильной службе. Поэтому кнопки **Push** и **Pull** будут вызывать исключения при их нажатии. В следующем разделе вы подключите это клиентское приложение к мобильной службе еще раз, чтобы проверить операции **Push** и **Pull** для синхронизации хранилища с базой данных мобильной службы.


1. В обозревателе решений Visual Studio откройте файл App.xaml.cs в общем проекте. Измените инициализацию **MobileServiceClient** на недопустимый адрес, заменив "**azure-mobile.net**" на "**azure-mobile.xxx**" для вашего URL-адреса. Затем сохраните файл.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Введите новые элементы списка и нажмите **Сохранить** для каждого из них. Новые элемент списка дел существует только в локальном хранилище, пока он не будет перемещен в мобильную службу. Клиентское приложение ведет себя так, если бы оно было подключено к мобильной службе, поддерживающей все операции создания, чтения, обновления и удаления (CRUD).

    ![][4]

3. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения мобильной службы

В этом разделе вы повторно подключите приложение к мобильной службе. Эта процедура имитирует переход приложения из автономного состояния в оперативное состояние с мобильной службой.


1. В обозревателе решений Visual Studio откройте файл App.xaml.cs в общем проекте. Измените инициализацию **MobileServiceClient** на допустимый адрес, заменив "**azure-mobile.xxx**" на "**azure-mobile.net**" для вашего URL-адреса. Затем сохраните файл.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Тестирование приложения, подключенного к мобильной службе


В этом разделе вы протестируете операции передачи и извлечения данных для синхронизации локального хранилища с базой данных мобильной службы. Можно протестировать клиент Магазина Windows 8.1 или клиент Windows Phone 8.1. На снимках экрана ниже показан клиент Магазина Windows 8.1. 

1. В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Обратите внимание, что данные выглядят так же, как в автономном сценарии, хотя приложение теперь подключено к мобильной службе. Это вызвано тем, что приложение всегда работает с объектом IMobileServiceSyncTable, который указывает на локальное хранилище.

    ![][4]

2. Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы. При использовании серверной части .NET для мобильной службы можно нажать кнопку **Управление** для вашей базы данных в расширении SQL Azure, чтобы выполнить запрос к таблице.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

    ![][6]

3. В приложении нажмите кнопку **Push**. После этого приложение вызовет метод MobileServiceClient.SyncContext.PushAsync. Эта операция приводит к тому, что база данных мобильной службы получает данные из хранилища. Однако локальное хранилище не получает элементы из базы данных мобильной службы.

    Операция передачи выполняется в MobileServiceClient.SyncContext, а не в IMobileServicesSyncTable, и передает изменения всех таблиц, связанных с данным контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.

€

4. В приложении установите флажки рядом с некоторыми элементами, отметив их в локальном хранилище как выполненные. 

    ![][8]

5. На этот раз нажмите в приложении кнопку **Pull**. Приложение вызывает методы IMobileServiceSyncTable.PullAsync() и RefreshTodoItems.  Обратите внимание, что все данные из базы данных мобильной службы были извлечены в локальное хранилище и показаны в приложении. Кроме того, обратите внимание, что все данные в локальном хранилище также были переданы в базу данных мобильной службы. Это происходит, потому что **операции извлечения всегда предшествует операция передачи**. Это позволяет убедиться, что все таблицы в локальном хранилище синхронизированы, как и связи между ними.
 
    ![][9]

    ![][10] 
  

##Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс IMobileServiceSyncTable и инициализировали MobileServiceClient.SyncContext с локальным хранилищем. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Чтобы синхронизировать локальное хранилище с сервером, мы использовали методы IMobileServiceSyncTable.PullAsync и MobileServiceClient.SyncContext.PushAsync.

*  Для отправки изменений на сервер мы вызвали IMobileServiceSyncContext.PushAsync(). Этот метод - член объекта IMobileServicesSyncContext, а не таблицы синхронизации, поскольку изменения будут передаваться во все таблицы:

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CRUD), будут отправлены на сервер.
   
* Для извлечения данных из таблицы на сервере в приложение мы вызвали метод IMobileServiceSyncTable.PullAsync.

    Операции извлечения всегда предшествует операция передачи. Это позволяет убедиться, что все таблицы в локальном хранилище синхронизированы, как и связи между ними.

    Также существуют перегрузки метода PullAsync(), позволяющие указать запрос для добавочной синхронизации. Если запрос не передается, PullAsync() извлекает все строки в соответствующей таблице (или запросе). Можно передать запрос для фильтрации только тех изменений, которые нужно синхронизировать вашему приложению.


## Дальнейшие действия

* [Обработка конфликтов с автономной поддержкой мобильных служб]

<!-- Anchors. -->
[Обновление приложения для поддержки автономных функций]: #enable-offline-app
[Тестирование приложения в автономном сценарии]: #test-offline-app
[Обновление приложения для повторного подключения мобильной службы]: #update-online-app
[Тестирование приложения, подключенного к мобильной службе]: #test-online-app
[Дальнейшие действия]: #next-steps

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
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Обработка конфликтов с автономной поддержкой мобильных служб]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Пример автономного приложения TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[SQLite для Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite для Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Учебник по Windows Phone 8 в Visual Studio 2012]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[Nuget-пакет SDK для мобильных служб]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[Nuget-пакет SQLiteStore]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0

<!--HONumber=35.2-->
