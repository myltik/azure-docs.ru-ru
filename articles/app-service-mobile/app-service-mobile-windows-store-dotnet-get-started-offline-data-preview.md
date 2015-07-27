<properties
	pageTitle="Использование автономных данных в мобильном приложении Azure (Магазин Windows) | Центр мобильных разработок"
	description="Дополнительные сведения об использовании мобильного приложения Azure для кэширования и синхронизации автономных данных в приложении Магазина Windows"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# Использование синхронизации автономных данных в мобильных приложениях Azure

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]


В этом учебнике показано, как добавить поддержку автономных функций в универсальное приложение Магазина Windows с помощью мобильных приложений Azure. Поддержка автономных функций позволяет взаимодействовать с локальной базой данных при работе приложения в автономном режиме. Данные автономные функции позволяют синхронизировать локальные изменения с серверной базой данных после подключения к сети.



Из этого учебника вы узнаете, как обновить проект универсального приложения, описанного в учебнике на странице [Создание приложения для Windows], чтобы обеспечить поддержку автономных функций мобильных приложений Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

В этом учебнике рассматриваются следующие основные действия:

1. [Обновление приложения для поддержки автономных функций]
2. [Обновление режима синхронизации приложения]
3. [Обновление приложения для повторного подключения внутренней службы мобильных приложений]

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Выполнение заданий на странице [Создание приложения для Windows][create a windows app].
* [Пакет SDK для мобильных служб Azure версии 2.0.0 или более поздней][azure mobile app sdk nuget]
* [Хранилище SQLite для мобильных служб Azure версии 1.0.2 или более поздней][sqlite store nuget]
* [SQLite для Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>.

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Автономные функции мобильных приложений Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Для использования этих функций в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальном хранилище. Затем укажите ссылку на таблицу с помощью интерфейса `IMobileServiceSyncTable`. В этом учебнике в качестве локального хранилища используется SQLite.

1. Установите среду выполнения SQLite для Windows 8.1 и Windows Phone 8.1.

    * **Среда выполнения Windows 8.1:** установите [SQLite для Windows 8.1].
    * **Windows Phone 8.1:** установите [SQLite для Windows Phone 8.1].

    >[AZURE.NOTE]Если вы используете Internet Explorer, после перехода по ссылке для установки SQLite может потребоваться загрузить VSIX-файл в виде ZIP-файла. Сохраните файл в папку на жестком диске с расширением .vsix вместо .zip. Дважды щелкните VSIX-файл в проводнике Windows для запуска установки.

2. В Visual Studio откройте проект, созданный в учебнике на странице [Создание приложения для Windows]. Установите NuGet-пакет **WindowsAzure.MobileServices.SQLiteStore** для проектов среды выполнения Windows 8.1 и Windows Phone 8.1.

    * **Windows 8.1.** В обозревателе решений щелкните правой кнопкой мыши проект Windows 8.1 и выберите пункт **Управление пакетами Nuget**, чтобы запустить диспетчер пакетов NuGet. Найдите **SQLiteStore**, чтобы установить пакет `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1.** Щелкните правой кнопкой мыши проект Windows Phone 8.1 и выберите пункт **Управление пакетами Nuget**, чтобы запустить диспетчер пакетов NuGet. Найдите **SQLiteStore**, чтобы установить пакет `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Если при установке создается ссылка на более раннюю версию SQLite, эту повторяющуюся ссылку можно просто удалить.

3. В обозревателе решений щелкните правой кнопкой мыши пункт **Ссылки** для проектов на платформах среды выполнения Windows 8.1 и Windows Phone 8.1. Убедитесь в наличии ссылки на элемент SQLite в разделе **Расширения**.

    ![][1] </br>

    **Среда выполнения Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

4. Для среды выполнения SQLite требуется изменить архитектуру процессора проекта на **x86**, **x64** или **ARM**. Параметр **Любой процессор** не поддерживается. В обозревателе решений щелкните элемент "Решение" в верхней части, а затем выберите в раскрывающемся списке для архитектуры процессора один из поддерживаемых параметров, которые требуется протестировать.

    ![][13]

5. В обозревателе решений в общем проекте откройте файл MainPage.cs. Раскомментируйте следующий код с помощью операторов в начале файла:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. В файле MainPage.cs закомментируйте определение `todoTable` и раскомментируйте одну из следующих строк, в которой выполняется вызов `MobileServicesClient.GetSyncTable()`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. В области файла MainPage.cs, помеченной как `Offline sync`, раскомментируйте методы `InitLocalStoreAsync` и `SyncAsync`. Метод `InitLocalStoreAsync` инициализирует контекст синхронизации клиента с хранилищем SQLite.

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8. В обработчике событий `OnNavigatedTo` раскомментируйте вызов `InitLocalStoreAsync`:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. Раскомментируйте 3 вызова `SyncAsync` в методах `InsertTodoItem`, `UpdateCheckedTodoItem` и `ButtonRefresh_Click`:

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. Добавьте обработчики исключений в метод `SyncAsync`:

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " +
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
        }

    В этом примере мы вернем из удаленной таблицы `todoTable` все записи, которые можно также отфильтровать путем передачи запроса. Первый параметр для `PullAsync` — идентификатор запроса, используемый для добавочной синхронизации, в рамках которой используется метка времени `UpdatedAt`, чтобы получить только записи, измененные с момента последней синхронизации. Идентификатор запроса должен быть описательной строкой, уникальной для каждого логического запроса в приложении. Чтобы явно отказаться от добавочной синхронизации, передайте `null` в качестве идентификатора запроса. В этом случае команда получает все записи по каждой операции принудительного извлечения, которая потенциально неэффективна.

    Обратите внимание, что `MobileServicePushFailedException` может возникать как при операциях принудительной передачи, так и при операциях принудительного извлечения. Оно может возникнуть для операции принудительного извлечения, так как внутри нее выполняется принудительная отправка, чтобы гарантировать согласованность всех таблиц, а также всех связей.

11. В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Приложение работает в том же режиме, как и до изменений автономной синхронизации, поскольку оно выполняет операцию синхронизации для операций вставки, изменения и обновления.

## <a name="update-sync"></a>Обновление режима синхронизации приложения

В этом разделе вы измените приложение, чтобы оно не синхронизировалось при операциях вставки и изменения, а синхронизировалось только при нажатии кнопки **Обновить**. Затем вы разорвете связь приложения с внутренним сервером мобильного приложения для имитации автономного сценария. При добавлении элементов данных они будут находиться в локальном хранилище, но не будут синхронизированы с серверной частью мобильного приложения.

1. Откройте файл App.xaml.cs в общем проекте. Измените методы `InsertTodoItem` и `UpdateCheckedTodoItem`, чтобы закомментировать вызовы `SyncAsync`.

2. Измените файл App.xaml.cs в общем проекте. Закомментируйте инициализацию **MobileServiceClient** и добавьте следующие строки, которые используют недопустимый URL-адрес мобильного приложения:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3. В `InitLocalStoreAsync()` закомментируйте вызов `SyncAsync()`, чтобы приложение не выполняло синхронизацию при запуске.

4. Нажмите клавишу **F5**, чтобы выполнить сборку и запустить приложение. Введите новые элементы todo. Нажмите кнопку **Сохранить** для каждого из них. Новый элемент мероприятия существует только в локальном хранилище, пока он не будет перемещен в серверную часть мобильного приложения. Клиентское приложение ведет себя так, если бы оно было подключено к серверной части мобильного приложения, поддерживающей все операции создания, чтения, обновления и удаления (CRUD).

5. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения к серверной части мобильного приложения

В этом разделе приложение будет повторно подключено к серверной части мобильного приложения. Эта процедура имитирует переход приложения из автономного состояния в оперативное состояние для серверной части мобильного приложения. При нажатии кнопки "Обновить" данные будут синхронизированы с серверной частью мобильного приложения.

1. Откройте файл App.xaml.cs в общем проекте. Снимите комментарии с предыдущей инициализации `MobileServiceClient`, чтобы вернуть правильный URL-адрес серверной части мобильного приложения, URL-адрес шлюза и ключ приложения.

2. Нажмите клавишу **F5**, чтобы перестроить и запустить приложение. Обратите внимание, что данные выглядят так же, как в автономном сценарии, хотя приложение теперь подключено к серверной части мобильного приложения. Это вызвано тем, что это приложение всегда работает с объектом `IMobileServiceSyncTable`, который указывает на локальное хранилище.

3. Войдите на портал управления Azure и проверьте базу данных мобильного приложения.

    В Visual Studio последовательно выберите пункты **Обозреватель серверов** -> **Azure** -> **Базы данных SQL**. Щелкните правой кнопкой мыши базу данных и выберите **Открыть в обозревателе объектов SQL Server**.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

4. В приложении нажмите кнопку **Обновить**. После этого приложение вызовет `PushAsync` и `PullAsync`. Эта операция отправляет локальные элементы хранения в мобильную службу, а затем извлекает из мобильной службы новые данные.

    Операция принудительной передачи выполняется в `MobileServiceClient.SyncContext`, а не в `IMobileServicesSyncTable`, и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.

5. В приложении установите флажки рядом с некоторыми элементами, отметив их в локальном хранилище как выполненные.

6. Нажмите кнопку **Обновить** еще раз, чтобы выполнить вызов `SyncAsync`. `SyncAsync` вызывает операции как принудительной отправки, так и принудительной передачи, но в этом случае мы могли бы удалить вызов `PushAsync`. Это происходит, потому что **операции извлечения всегда предшествует операция передачи**. Таким образом все таблицы в локальном хранилище и связи между ними остаются всегда согласованными.


##Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс `IMobileServiceSyncTable` и инициализировали `MobileServiceClient.SyncContext` в локальном хранилище. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Когда мы хотели синхронизировать локальное хранилище с сервером, то использовали методы `IMobileServiceSyncTable.PullAsync` и `MobileServiceClient.SyncContext.PushAsync`.

*  Для отправки изменений на сервер мы вызывали метод `IMobileServiceSyncContext.PushAsync()`. Этот метод является членом `IMobileServicesSyncContext`, а не таблицы синхронизации, поскольку он будет передавать изменения во все таблицы.

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер.

* Для извлечения данных из таблицы на сервере в приложение мы вызывали метод `IMobileServiceSyncTable.PullAsync`.

    Операции извлечения всегда предшествует операция передачи. Это позволяет обеспечить согласованность всех таблиц в локальном хранилище, а также связей между ними.

    Также существуют перегрузки `PullAsync()`, которые позволяют указать запрос, чтобы отфильтровать данные, хранящиеся на клиенте. Если запрос не передается, `PullAsync()` извлекает все строки в соответствующей таблице (или запросе). Можно передать запрос для фильтрации только изменений, с которыми приложение должно быть синхронизировано.

* Чтобы включить добавочную синхронизацию, передайте идентификатор запроса в `PullAsync()`. Идентификатор запроса используется для хранения метки времени последнего обновления из результатов последней операции принудительной отправки. Идентификатор запроса должен быть описательной строкой, уникальной для каждого логического запроса в приложении. Если запрос содержит параметр, то аналогичное значение параметра должно быть частью идентификатора запроса.

    Например, при фильтрации по userid он должен быть частью идентификатора запроса:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Если вы хотите явно отказаться от добавочной синхронизации, передайте `null` в качестве идентификатора запроса. В этом случае при каждом вызове `PullAsync` будут извлекаться все записи, что может снижать уровень производительности.

* В противном случае приложению следует периодически вызывать `IMobileServiceSyncTable.PurgeAsync()` для очистки локального хранилища.


<!-- Anchors. -->
[Обновление приложения для поддержки автономных функций]: #enable-offline-app
[Обновление режима синхронизации приложения]: #update-sync
[Обновление приложения для повторного подключения внутренней службы мобильных приложений]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[todolist offline sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[create a windows app]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[Создание приложения для Windows]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[SQLite для Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite для Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2
 

<!---HONumber=July15_HO3-->