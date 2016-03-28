<properties
	pageTitle="Включение автономной синхронизации для мобильного приложения Azure (Windows 8.1) | Microsoft Azure"
	description="Дополнительные сведения об использовании мобильного приложения Azure для кэширования и синхронизации автономных данных в приложении Магазина Windows"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="wesmc"/>

# Включение автономной синхронизации для приложения для Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Обзор

В этом учебнике показано, как добавить поддержку автономных функций в приложение Магазина Windows 8.1 или Магазина Windows Phone с помощью серверной части мобильного приложения Azure. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением — просматривать, добавлять или изменять данные — даже при отсутствии подключения к сети. Изменения хранятся в локальной базе данных; после подключения устройства к сети эти изменения синхронизируются с удаленным внутренним сервером.

Из этого учебника вы узнаете, как обновить проект приложения для Windows 8.1, описанного в учебнике [Создание приложения для Windows], чтобы обеспечить поддержку автономных функций мобильных приложений Azure. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для доступа к данным. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## Требования

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Выполнение заданий на странице [Создание приложения для Windows][create a windows app].
* [Хранилище SQLite для мобильных служб Azure][sqlite store nuget]
* [SQLite для Windows 8.1](http://www.sqlite.org/downloads)

## Обновление клиентского приложения для поддержки автономных функций

Автономные функции мобильных приложений Azure позволяют взаимодействовать с локальной базой данных в случае автономной работы. Для использования этих функций в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальном хранилище. Затем укажите ссылку на таблицу с помощью интерфейса `IMobileServiceSyncTable`. В этом учебнике в качестве локального хранилища используется SQLite.

1. Установите среду выполнения SQLite для Windows 8.1 и Windows Phone 8.1.

    * **Среда выполнения Windows 8.1:** установите [SQLite для Windows 8.1].
    * **Windows Phone 8.1:** установите [SQLite для Windows Phone 8.1].

    >[AZURE.NOTE] Эти инструкции также подойдут для проектов Windows 10 UAP, но тогда следует установить [SQLite для Windows 10].

2. В Visual Studio откройте проект, созданный в учебнике на странице [Создание приложения для Windows]. Установите пакет NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** для проектов среды выполнения Windows 8.1 и Windows Phone 8.1. Добавьте ссылку на NuGet в проекты для Магазина Windows 8.1 и Windows Phone 8.1.

    >[AZURE.NOTE] Если при установке создается дополнительная ссылка на другую версию SQLite, которая у вас не установлена, будет сообщено об ошибке компиляции. Следует устранить эту ошибку, удалив повторяющийся элемент из узла **Ссылки** в своих проектах.

3. В обозревателе решений щелкните правой кнопкой мыши пункт **Ссылки** для проектов на платформах среды выполнения Windows 8.1 и Windows Phone 8.1. Убедитесь в наличии ссылки на элемент SQLite в разделе **Расширения**.

    ![][1] </br>

    **Среда выполнения Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite — это собственная библиотека, для которой требуется выбрать архитектуру платформы, например **x86**, **x64** или **ARM**. Параметр **Любой процессор** не поддерживается. В обозревателе решений щелкните элемент "Решение" в верхней части, а затем выберите в раскрывающемся списке для архитектуры процессора один из поддерживаемых параметров, которые требуется протестировать.

    ![][13]

5. В обозревателе решений в общем проекте откройте файл MainPage.cs. Раскомментируйте следующий код с помощью операторов в начале файла:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. В файле MainPage.cs закомментируйте строку кода, которая инициализирует `todoTable` как `IMobileServiceTable`. Раскомментируйте строку кода, которая инициализирует `todoTable` как `IMobileServiceSyncTable`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. В области файла MainPage.cs, помеченной как `Offline sync`, раскомментируйте методы `InitLocalStoreAsync` и `SyncAsync`. Метод `InitLocalStoreAsync` инициализирует контекст синхронизации клиента с хранилищем SQLite. В Visual Studio можно выбрать все закомментированные строки и использовать клавиши **Ctrl**+**K**+**U**, чтобы раскомментировать их.

	Обратите внимание, что в `SyncAsync` операция отправки выполняется для `MobileServiceClient.SyncContext`, а не `IMobileServicesSyncTable`. Это вызвано тем, что контекст отслеживает изменения, внесенные клиентом, для всех таблиц. Подробнее об этом поведении см. в разделе [Автономная синхронизация данных в мобильных приложениях Azure].

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

10. Добавьте обработчики исключений в метод `SyncAsync`. В автономном режиме `MobileServicePushFailedException` будет порождено с `PushResult.Status == CancelledByNetworkError`.

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
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
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

    В этом примере `PullAsync` мы извлекаем из удаленной таблицы `todoTable` все записи, которые можно также отфильтровать, передав запрос. Первый параметр для `PullAsync` — идентификатор запроса, используемый для добавочной синхронизации, в рамках которой используется метка времени `UpdatedAt`, чтобы получить только записи, измененные с момента последней синхронизации. Идентификатор запроса должен быть описательной строкой, уникальной для каждого логического запроса в вашем клиентском приложении. Чтобы явно отказаться от добавочной синхронизации, передайте `null` в качестве идентификатора запроса. В этом случае команда получает все записи по каждой операции принудительного извлечения, которая потенциально неэффективна.

    Обратите внимание, что `MobileServicePushFailedException` может возникать как при операциях принудительной передачи, так и при операциях принудительного извлечения. Оно может возникнуть для операции принудительного извлечения, так как внутри нее выполняется принудительная отправка, чтобы гарантировать согласованность всех таблиц, а также всех связей.

11. В Visual Studio нажмите клавишу **F5**, чтобы выполнить сборку и запустить клиентское приложение. Приложение работает в том же режиме, как и до изменений автономной синхронизации, поскольку оно выполняет операцию синхронизации для операций вставки, изменения и обновления. Тем не менее, оно заполнит локальную базу данных, которую можно использовать в автономном режиме. В следующем разделе мы создадим ситуацию с автономным режимом, раз локальная база данных уже заполнена.

## <a name="update-sync"></a>Обновление режима синхронизации клиентского приложения

В этом разделе вы измените клиентское приложение для имитации автономного режима, разорвав подключение к серверной части мобильного приложения Azure. При добавлении элементов данных обработчик исключений сообщит вам о том, что приложение работает в автономном режиме с `PushResult.Status == CancelledByNetworkError`. Добавленные элементы будут храниться в локальном хранилище, но не будут синхронизированы с серверной частью мобильного приложения, пока не будет восстановлено подключение к сети, а данные — успешно отправлены в серверную часть мобильного приложения Azure.

1. Измените файл App.xaml.cs в общем проекте. Закомментируйте инициализацию **MobileServiceClient** и добавьте следующие строки, которые используют недопустимый URL-адрес мобильного приложения:

         public static MobileServiceClient MobileService =
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	Если в приложении используется проверка подлинности, единый вход завершится ошибкой. Чтобы продемонстрировать поведение в автономном режиме, отключите на устройстве Wi-Fi и сотовую связь и включите авиарежим.

2. Нажмите клавишу **F5**, чтобы выполнить сборку и запустить приложение. Обратите внимание, синхронизацию не удалось выполнить при обновлении во время запуска приложения.
3. Введите новые элементы todo. Нажмите кнопку **Сохранить** для каждого из них. Сбой отправки происходит для каждого с `PushResult.Status=CancelledByNetworkError`. Новый элемент мероприятия существует только в локальном хранилище, пока он не будет перемещен в серверную часть мобильного приложения.

	Можно подавить отображение диалогового окна исключения для `PushResult.Status=CancelledByNetworkError`. Тогда клиентское приложение будет вести себя так, будто оно подключено к серверной части мобильного приложения, обеспечивающей прозрачное выполнение всех операций создания, чтения, обновления и удаления (CRUD).

4. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

5. В Visual Studio в откройте **обозреватель сервера**. Перейдите к своей базе данных, выбрав **Azure** > **Базы данных SQL**. Щелкните правой кнопкой мыши базу данных и выберите пункт **Открыть в обозревателе объектов SQL Server**. Теперь можно перейти к таблице базы данных SQL и ее содержимому. Убедитесь, что данные в серверной базе данных не изменились.

6. (Необязательно.) Воспользуйтесь инструментом REST, например Fiddler или Postman, чтобы выполнить запрос к мобильной серверной части с помощью запроса GET вида `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения к серверной части мобильного приложения

В этом разделе приложение будет повторно подключено к серверной части мобильного приложения. Эта процедура имитирует переход приложения из автономного состояния в оперативное состояние для серверной части мобильного приложения. При первом запуске приложения обработчик событий `OnNavigatedTo` вызовет `InitLocalStoreAsync`. Это, в свою очередь, вызовет `SyncAsync` для синхронизации локального хранилища с серверной базой данных. Поэтому приложение будет пытаться выполнить синхронизацию при запуске.

1. Откройте файл App.xaml.cs в общем проекте. Отмените преобразование в комментарий предыдущей инициализации `MobileServiceClient`, чтобы использовать правильный URL-адрес мобильного приложения и URL-адрес шлюза.

2. Нажмите клавишу **F5**, чтобы перестроить и запустить приложение. Приложение синхронизирует локальные изменения с серверной частью мобильного приложения Azure, применяя операции отправки и извлечения, сразу же после выполнения обработчика событий `OnNavigatedTo`.

3. (Необязательно.) Просмотрите обновленные данные, используя обозреватель объектов SQL Server или инструмент REST, например Fiddler. Обратите внимание, что данные синхронизированы между базой данных серверной части мобильного приложения Azure и локальным хранилищем.

4. В приложении установите флажки рядом с некоторыми элементами, отметив их в локальном хранилище как выполненные.

  `UpdateCheckedTodoItem` вызывает `SyncAsync` для полной синхронизации каждого элемента с серверной частью мобильного приложения. `SyncAsync` вызывает отправку и извлечение. Однако следует отметить, что **всякий раз, когда выполняется извлечение из таблицы, в которую клиент внес изменения, сначала всегда автоматически будет выполняться отправка для контекста синхронизации клиента**. Это позволяет обеспечить согласованность всех таблиц в локальном хранилище, а также связей между ними. Поэтому в данном случае мы можем удалить вызов `PushAsync`, так как он автоматически выполняется при выполнении извлечения. Если не знать об этом, такое поведение может привести к непредвиденной отправке данных. Подробнее об этом поведении см. в разделе [Автономная синхронизация данных в мобильных приложениях Azure].


##Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс `IMobileServiceSyncTable` и инициализировали `MobileServiceClient.SyncContext` в локальном хранилище. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Когда мы хотели синхронизировать локальное хранилище с сервером, то использовали методы `IMobileServiceSyncTable.PullAsync` и `MobileServiceClient.SyncContext.PushAsync`.

*  Для отправки изменений на сервер мы вызывали метод `IMobileServiceSyncContext.PushAsync()`. Этот метод является членом `IMobileServicesSyncContext`, а не таблицы синхронизации, поскольку он будет передавать изменения во все таблицы.

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер.

* Для извлечения данных из таблицы на сервере в приложение мы вызывали метод `IMobileServiceSyncTable.PullAsync`.

    Операции извлечения всегда предшествует отправка контекста синхронизации, если в контексте синхронизации клиента отслеживались изменения в этой таблице. Это позволяет обеспечить согласованность всех таблиц в локальном хранилище, а также связей между ними.

    Также существуют перегрузки `PullAsync()`, которые позволяют указать запрос, чтобы отфильтровать данные, хранящиеся на клиенте. Если запрос не передается, `PullAsync()` извлекает все строки в соответствующей таблице (или запросе). Можно передать запрос для фильтрации только изменений, с которыми приложение должно быть синхронизировано.

* Чтобы включить добавочную синхронизацию, передайте идентификатор запроса в `PullAsync()`. Идентификатор запроса используется для хранения метки времени последнего обновления из результатов последней операции принудительной отправки. Идентификатор запроса должен быть описательной строкой, уникальной для каждого логического запроса в приложении. Если запрос содержит параметр, то аналогичное значение параметра может быть частью идентификатора запроса.

    Например, при фильтрации по userid имя уникальный идентификатор запроса может быть следующим:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Если вы хотите явно отказаться от добавочной синхронизации, передайте `null` в качестве идентификатора запроса. В этом случае при каждом вызове `PullAsync` будут извлекаться все записи, что может снижать уровень производительности.

* В противном случае приложению следует периодически вызывать `IMobileServiceSyncTable.PurgeAsync()` для очистки локального хранилища.


## Дополнительные ресурсы

* [Автономная синхронизация данных в мобильных приложениях Azure]

* [Облачное покрытие: автономная синхронизация в мобильных службах Azure] (примечание: видео рассказывает о мобильных службах, однако точно так же автономная синхронизация работает в мобильных службах Azure)

* [Azure Friday: приложения с поддержкой автономного режима в мобильных службах Azure]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Автономная синхронизация данных в мобильных приложениях Azure]: ../app-service-mobile-offline-data-sync.md
[create a windows app]: ../app-service-mobile-windows-store-dotnet-get-started.md
[Создание приложения для Windows]: ../app-service-mobile-windows-store-dotnet-get-started.md
[SQLite для Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite для Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite для Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921

[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/

[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: приложения с поддержкой автономного режима в мобильных службах Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0316_2016-->