<properties urlDisplayName="Using Offline Data" pageTitle="Использование автономных данных в мобильных службах (Windows Store) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Использование автономной синхронизации данных в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить поддержку автономного универсального Магазина Windows с помощью мобильных служб Azure. Автономный режим позволяет взаимодействовать с локальной базой данных, когда для приложения действует автономный сценарий. Когда приложение находится в сети с серверной базой данных, можно синхронизировать локальные изменения с помощью автономных функций. 
</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">просмотрите учебник</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">14:36</span></div>
</div>


В этом учебнике будет обновляться проект приложения Universal из учебника [Приступая к работе с мобильными службами] для поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.


>[WACOM.NOTE] Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Windows Azure для хранения и извлечения данных из приложения Магазина Windows. Если ранее вы не работали с мобильными службами, сначала пройдите учебник [Приступая к работе с мобильными службами].
>
>Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>. 
>
>Предыдущая версия учебника Windows Phone 8 для Visual Studio 2012 все еще доступна здесь, в учебнике [Windows Phone 8 для Visual Studio 2012].


В этом учебнике рассматриваются следующие основные действия:

1. [Обновление приложения для поддержки автономных функций]
2. [Тестирование приложения в автономном сценарии] 
3. [Обновление приложения для повторного подключения мобильной службы]
4. [Тестирование приложения, подключенного к мобильной службе]

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Завершение учебника [Приступая к работе с мобильными службами].
* [Пакет SDK для мобильных служб Azure версии 1.3.0-beta2 (или более поздней версии)][Mobile Services SDK Nuget].
* [SQLite Store для мобильных служб Azure версии 1.0.0-beta2 (или более поздней версии).)][SQLite store nuget]
* SQLite для Windows 8.1

>[AZURE.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>. 

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Автономные функции мобильных служб Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Для использования этих возможностей в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальном хранилище. Затем необходимо сослаться на таблицу с помощью интерфейса `IMobileServiceSyncTable`. В этом учебнике мы используем SQLite для локального хранилища.

>[AZURE.NOTE]Можно пропустить этот раздел и просто получить пример проекта, который уже содержит поддержку автономного режима из репозитория образцов Github для мобильных служб. Пример проекта с автономной поддержкой находится здесь [Образец автономного TodoList].


1. Установка среды выполнения SQLite для Windows 8.1 и Windows Phone 8.1. 

    * **Среда выполнения Windows 8.1:** Установка среды выполнения SQLite для Windows 8.1 с помощью этой ссылки [SQLite для Windows 8.1].
    * **Windows Phone 8.1:** Установка среды выполнения SQLite для Windows 8.1 с помощью этой ссылки [SQLite для Windows 8.1].

    >[AZURE.NOTE] Если вы используете Internet Explorer, после перехода по ссылке для установки SQLite может потребоваться загрузить VSIX-файл как ZIP-файл. Сохраните файл в папку на жестком диске с расширением .vsix вместо .zip. Дважды щелкните VSIX-файл в проводнике Windows, чтобы начать установку.

2. Откройте в Visual Studio проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами]. В обозревателе решений щелкните правой кнопкой мыши **Ссылки** для проектов платформ среды выполнения Windows 8.1 и Windows Phone 8.1 и добавьте ссылку на SQLite, который находится в разделе **Расширения**. 

    ![][1]
    </br>**Среда выполнения Windows 8.1**

    ![][11]
    </br>**Windows Phone 8.1**

3.  Для среды выполнения SQLite требуется изменить архитектуру процессора проекта на **x86**, **x64** или **ARM**. Параметр **Любой процессор**  не поддерживается. В обозревателе решений для Visual Studio щелкните "Решение" вверху, а затем измените в поле раскрывающегося списка архитектуры процессора один из поддерживаемых параметров, которые требуется протестировать.

    ![][13]

4. Установите пакет  **WindowsAzure.MobileServices.SQLiteStore** NuGet для среды выполнения Windows 8.1 и проектов Windows Phone 8.1.

    * **Windows 8.1:** В обозревателе решений щелкните правой кнопкой мыши проект Windows 8.1 и щелкните **Управление пакетами NuGet** для запуска диспетчера пакетов NuGet. Выполните поиск **SQLiteStore** , чтобы установить пакет WindowsAzure.MobileServices.SQLiteStore.
    * **Windows Phone 8.1:**Щелкните правой кнопкой мыши проект Windows Phone 8.1 и щелкните **Управление пакетами Nuget**  для запуска диспетчера пакетов NuGet. Выполните поиск **SQLiteStore** , чтобы установить пакет WindowsAzure.MobileServices.SQLiteStore.     

    >[WACOM.NOTE]Если установка создает ссылку на более старую версию SQLite, просто удалите эту повторяющееся ссылку. 

    ![][2]

5. В обозревателе решений для Visual Studio, в общем проекте, откройте файл MainPage.cs. Добавьте в начало файла следующие инструкции using.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. В файле Mainpage.xaml.cs замените объявление `todoTable` на объявление типа `IMobileServicesSyncTable`, которое инициализируется при вызове метода `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. В файле MainPage.xaml.cs обновите обработчик событий `OnNavigatedTo` так, чтобы он инициализировал контекст синхронизации клиента с хранилищем SQLite. Хранилище SQLite создается с таблицей, соответствующей схеме таблицы мобильной службы, но оно должно содержать системное свойство **Version**, которое будет добавлено на следующем шаге.

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


8. В обозревателе решений для Visual Studio, в общем проекте, разверните **DataModel** и откройте TodoItem.cs. Добавьте инструкцию `using` для пространства имен MobileServices. Это необходимо, чтобы разрешить атрибут версии для системного свойства Version.

        using Microsoft.WindowsAzure.MobileServices;

      В файле MainPage.xaml.cs добавьте в класс `TodoItem` системное свойство **Version**, как показано ниже. Схема таблицы должна включать системное свойство **Version**, как показано ниже, для поддержки автономных функций.

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




9. Затем можно обновить пользовательский интерфейс для проектов Windows 8.1 и Windows Phone 8.1, для включения кнопок, которые будут поддерживать операции автономной синхронизации между автономной локальной базы данных и базой данных мобильной службы в Azure. 

    * **Windows 8.1:**В обозревателе решений для Visual Studio в проекте Windows 8.1 откройте файл MainPage.xaml. Найдите кнопку с именем **ButtonRefresh**. Замените этот элемент кнопки следующей панелью стека кнопок. 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8.1:**В обозревателе решений для Visual Studio в проекте Windows 8.1 откройте файл MainPage.xaml. Найдите кнопку с именем **ButtonRefresh**. Замените этот элемент кнопки следующей панелью стека кнопок. 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
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
        
    Обратите внимание, что `MobileServicePushFailedException` для операции принудительной отправки и извлечения. Это может произойти для операции принудительной установки, поэтому убедитесь, что опрашивающая операция внутренне выполняет отправку, чтобы гарантировать синхронизацию всех таблиц, а также всех связей. В следующем учебнике [Обработка конфликтов с автономной поддержкой мобильных служб] показано, как обрабатывать эти связанные исключения синхронизации.

    Для поддержки синхронизации удаленных записей с синхронизацией автономных данных следует включить[Удаление с возможностью восстановления](/ru-ru/documentation/articles/mobile-services-using-soft-delete/). В противном случае необходимо вручную удалить записи в локальном хранилище или вызвать `IMobileServiceSyncTable::PurgeAsync()` для очистки локального хранилища.

    В этом примере мы передаем запрос методу `PullAsync` для поддержки добавочной синхронизации. Это полезно в случаях, когда отсутствует необходимость в расходах на извлечение всей таблицы во время синхронизации. В этом случае проблема заключается не в текстовых изменениях todoitems, нам просто необходимо принудительно извлечь завершенные элементы для отметки их в нашем списке todolist.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // In this example scenario we are demonstrating incremental sync. We only want to 
                // sync the uncomplete todoitems. If another client updated the text or any other 
                // field after completing the item, it doesn't concern us. So we pass a query to the 
                // PullAsync() method to pull uncomplete todoitems instead of the entire table.
                await todoTable.PullAsync(todoTable.Where(todoItem => todoItem.Complete == false));

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

11. Постройте решение и убедитесь, что ошибок построения нет ни в одном из проектов.


## <a name="test-offline-app"></a>Тестирование приложения в автономном сценарии

В этом разделе будет разорвана связь приложения с мобильной службой для имитации автономного сценария. Затем будет добавлено несколько элементов данных, которые будут храниться в локальном хранилище.

Обратите внимание, что в этом разделе приложение не должно быть подключено к мобильной службе. Поэтому кнопки **Push** и **Pull** будут вызывать исключения при их нажатии. В следующем разделе оно будет подключено к мобильной службе еще раз, чтобы проверить операции **Push** и **Pull** для синхронизации хранилища с базой данных мобильной службы.


1. В обозревателе решений в Visual Studio откройте файл App.xaml.cs в общем проекте. Измените инициализацию **MobileServiceClient** на недопустимый адрес, заменив "**azure-mobile.net**" на "**azure-mobile.xxx**" для вашего URL-адреса. Затем сохраните файл.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Введите новые элементы todo и для каждого из них щелкните **Сохранить**. Новые элемент списка дел существует только в локальном хранилище, пока он не будет перемещен в мобильную службу. Клиентское приложение ведет себя так, как если бы оно было подключено к мобильной службе, поддерживающей все операции создания, чтения, обновления и удаления (CRUD).

    ![][4]

3. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения мобильной службы

В этом разделе приложение будет повторно подключено к мобильной службе. Эта процедура имитирует переход приложения из автономного состояния в оперативное состояние с мобильной службой.


1. В обозревателе решений в Visual Studio откройте файл App.xaml.cs в общем проекте. Измените инициализацию **MobileServiceClient** на допустимый адрес, заменив "**azure-mobile.xxx**" на "**azure-mobile.net**" для вашего URL-адреса. Затем сохраните файл.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Тестирование приложения, подключенного к мобильной службе


В этом разделе вы протестируете операции передачи и извлечения данных для синхронизации локального хранилища с базой данных мобильной службы. Можно проверить клиент Windows Store 8.1 или Windows Phone 8.1. На снимках экрана показан клиент Windows Store 8.1. 

1. В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Обратите внимание, что данные выглядят так же, как в автономном сценарии, хотя приложение теперь подключено к мобильной службе. Это вызвано тем, что данное приложение всегда работает с объектом `IMobileServiceSyncTable`, который указывает на локальное хранилище.

    ![][4]

2. Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы. При использовании серверной части .NET для мобильной службы можно нажать кнопку **Управление** для вашей базы данных в расширении SQL Azure, чтобы выполнить запрос к таблице.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

    ![][6]

3. В приложении нажмите кнопку  **Push**. В этом случае приложение вызовет `MobileServiceClient.SyncContext.PushAsync`. Эта операция приводит к тому, что база данных мобильной службы получает данные из хранилища. Однако локальное хранилище не получает элементы из базы данных мобильной службы.

    Операция принудительной отправки выполняется в `MobileServiceClient.SyncContext`, а не в `IMobileServicesSyncTable` и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.

    ![][7]

4. В приложении установите флажок рядом с несколькими элементами для их выполнения в локальном хранилище. 

    ![][8]

5. На этот раз нажмите кнопку **Pull** в приложении. Приложение вызывает `IMobileServiceSyncTable.PullAsync()` и `RefreshTodoItems`.  Обратите внимание, что все данные из базы данных мобильной службы были извлечены в локальное хранилище и показаны в приложении. Кроме того, обратите внимание, что все данные в локальном хранилище также были переданы в базу данных мобильной службы. Это происходит, потому что **операции извлечения всегда предшествует операция передачи**. Это позволяет убедиться, что все таблицы в локальном хранилище, а также связи синхронизированы.
 
    ![][9]

    ![][10] 
  

##Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс `IMobileServiceSyncTable` и инициализировали `MobileServiceClient.SyncContext` с локальным хранилищем. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Когда мы хотели синхронизировать локальное хранилище с сервером, мы использовали методы `IMobileServiceSyncTable.PullAsync` и `MobileServiceClient.SyncContext.PushAsync`.

*   Для отправки изменений на сервер мы вызвали метод `IMobileServiceSyncContext.PushAsync()`. Он является членом объекта `IMobileServicesSyncContext`, а не таблицы синхронизации, поскольку изменения будут переданы во все таблицы:

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CRUD), будут отправлены на сервер.
   
* Для извлечения данных из таблицы на сервере в приложение мы вызвали метод `IMobileServiceSyncTable.PullAsync`.

    Операции извлечения всегда предшествует операция передачи. Это позволяет убедиться, что все таблицы в локальном хранилище, а также связи синхронизированы.

    Кроме того, существуют перегруженные методы `PullAsync()`, позволяющие указать запрос для поддержки добавочной синхронизации. Если запрос не передается, `PullAsync()` будет извлекать все строки в соответствующей таблице (или запросе). Можно передать запрос для фильтрации только изменений, с которыми приложение должно быть синхронизировано.


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
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Обработка конфликтов с автономной поддержкой мобильных служб]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Пример автономного TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[SQLite для Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite для Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Учебник по Windows Phone 8 для Visual Studio 2012]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[Мобильные службы SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[Хранилище SQLite nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
