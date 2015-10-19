<properties 
	pageTitle="Использование автономных данных в универсальном приложении Windows | Microsoft Azure" 
	description="Узнайте, как использовать мобильные службы Azure для кэширования и синхронизации автономных данных в универсальном приложении Windows." 
	documentationCenter="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="donnam"/>

# Использование автономной синхронизации данных в мобильных службах

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

В этом учебнике показано, как добавить поддержку автономных функций в универсальное приложение для Магазина Windows с помощью мобильных служб Azure. Автономный режим позволяет взаимодействовать с локальной базой данных, когда для приложения действует автономный сценарий. Когда приложение находится в сети с серверной базой данных, можно синхронизировать локальные изменения с помощью автономных функций.

Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

Во время работы с этим учебником вы обновите универсальный проект приложения, созданный при выполнении заданий учебника [Приступая к работе с мобильными службами], чтобы добавить поддержку автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

>[AZURE.NOTE]Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Windows Azure для хранения и извлечения данных из приложения Магазина Windows. Если ранее вы не работали с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].

##Предварительные требования 

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Выполнение заданий учебника [Приступая к работе с мобильными службами].
* [Пакет SDK для мобильных служб Azure версии 1.3.0 или более поздней][Mobile Services SDK Nuget]
* [Хранилище SQLite для мобильных служб Azure версии 1.0.0 или более поздней][SQLite store nuget]
* [SQLite для Windows 8.1](www.sqlite.org/downloads)
* Учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28). 

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Автономные функции мобильных служб Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Для использования этих функций в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальном хранилище. Затем укажите ссылку на таблицу с помощью интерфейса `IMobileServiceSyncTable`. В этом учебнике в качестве локального хранилища используется SQLite.

>[AZURE.NOTE]Можно пропустить этот раздел и просто получить пример проекта, который уже поддерживает автономный режим, из репозитория примеров Github для мобильных служб. Версию проекта с включенной поддержкой автономных функций можно найти по этой ссылке: [Пример автономного приложения TodoList].

1. Установите среду выполнения SQLite для Windows 8.1 и Windows Phone 8.1. 

    * **Среда выполнения Windows 8.1:** установите [SQLite для Windows 8.1].
    * **Windows Phone 8.1:** установите [SQLite для Windows Phone 8.1].

    >[AZURE.NOTE]Если вы используете Internet Explorer, после перехода по ссылке для установки SQLite может потребоваться загрузить VSIX-файл в виде ZIP-файла. Сохраните файл в папку на жестком диске с расширением .vsix вместо .zip. Дважды щелкните VSIX-файл в проводнике Windows для запуска установки.

2. Откройте в Visual Studio проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами]. Установите NuGet-пакет **WindowsAzure.MobileServices.SQLiteStore** для проектов среды выполнения Windows 8.1 и Windows Phone 8.1.

    * **Windows 8.1.** В обозревателе решений щелкните правой кнопкой мыши проект Windows 8.1 и выберите пункт **Управление пакетами Nuget**, чтобы запустить диспетчер пакетов NuGet. Найдите **SQLiteStore**, чтобы установить пакет `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1.** Щелкните правой кнопкой мыши проект Windows Phone 8.1 и выберите пункт **Управление пакетами Nuget**, чтобы запустить диспетчер пакетов NuGet. Найдите **SQLiteStore**, чтобы установить пакет `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Если при установке создается ссылка на более раннюю версию SQLite, эту повторяющуюся ссылку можно просто удалить.

    ![][2]

2. В обозревателе решений щелкните правой кнопкой мыши пункт **Ссылки** для проектов на платформах среды выполнения Windows 8.1 и Windows Phone 8.1. Убедитесь в наличии ссылки на элемент SQLite в разделе **Расширения**.

    ![][1] </br>

    **Среда выполнения Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

3. Для среды выполнения SQLite требуется изменить архитектуру процессора проекта на **x86**, **x64** или **ARM**. Параметр **Любой процессор** не поддерживается. В обозревателе решений щелкните элемент "Решение" в верхней части, а затем выберите в раскрывающемся списке для архитектуры процессора один из поддерживаемых параметров, которые требуется протестировать.

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

    >[AZURE.NOTE]* Чтобы удалить записи из локального хранилища устройства, если они удалены из базы данных мобильной службы, вам следует включить [обратимое удаление]. В противном случае приложение должно периодически вызывать `IMobileServiceSyncTable.PurgeAsync()` для очистки локального хранилища.

    Обратите внимание, что `MobileServicePushFailedException` может возникать как при операциях принудительной передачи, так и при операциях принудительного извлечения. Оно может возникнуть для операции принудительного извлечения, так как внутри нее выполняется принудительная отправка, чтобы гарантировать согласованность всех таблиц, а также всех связей. В следующем учебнике [Обработка конфликтов с автономной поддержкой мобильных служб] показано, как обрабатывать такие исключения, связанные с синхронизацией.

11. В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Приложение работает в том же режиме, как и до изменений автономной синхронизации, поскольку оно выполняет операцию синхронизации для операций вставки, изменения и обновления.

## <a name="update-sync"></a>Обновление режима синхронизации приложения

В этом разделе вы измените приложение, чтобы оно не синхронизировалось при операциях вставки и изменения, а синхронизировалось только при нажатии кнопки **Обновить**. Затем вы разорвете связь приложения с мобильной службой для имитации автономного сценария. При добавлении элементов данных они будут находиться в локальном хранилище, но не будут синхронизированы с мобильной службой.

1. Откройте файл MainPage.cs в общем проекте. Измените методы `InsertTodoItem` и `UpdateCheckedTodoItem`, чтобы закомментировать вызовы `SyncAsync`.

2. Измените файл App.xaml.cs в общем проекте. Закомментируйте инициализацию **MobileServiceClient** и добавьте следующие строки, которые используют недопустимый URL-адрес мобильной службы:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3. В `InitLocalStoreAsync()` закомментируйте вызов `SyncAsync()`, чтобы приложение не выполняло синхронизацию при запуске.

4. Нажмите клавишу **F5**, чтобы выполнить сборку и запустить приложение. Введите новые элементы todo. Нажмите кнопку **Сохранить** для каждого из них. Новые элемент списка дел существует только в локальном хранилище, пока он не будет перемещен в мобильную службу. Клиентское приложение ведет себя так, если бы оно было подключено к мобильной службе, поддерживающей все операции создания, чтения, обновления и удаления (CRUD).

5. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения мобильной службы

В этом разделе вы повторно подключите приложение к мобильной службе. Эта процедура имитирует переход приложения из автономного состояния в оперативное состояние с мобильной службой. При нажатии кнопки "Обновить" данные будут синхронизированы с вашей мобильной службой.

1. Откройте файл App.xaml.cs в общем проекте. Раскомментируйте предыдущую инициализацию `MobileServiceClient`, чтобы вернуть правильный URL-адрес и ключ приложения мобильной службы.

2. Нажмите клавишу **F5**, чтобы перестроить и запустить приложение. Обратите внимание, что данные выглядят так же, как в автономном сценарии, хотя приложение теперь подключено к мобильной службе. Это вызвано тем, что это приложение всегда работает с объектом `IMobileServiceSyncTable`, который указывает на локальное хранилище.

3. Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы.

    Если вы используете серверную часть .NET для мобильной службы, в Visual Studio выполните команду **Обозреватель сервера** -> **Azure** -> **Базы данных SQL**. Щелкните правой кнопкой мыши базу данных и выберите пункт **Открыть в обозревателе объектов SQL Server**.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

    ![][6]

4. В приложении нажмите кнопку **Обновить**. После этого приложение вызовет `PushAsync` и `PullAsync`. Эта операция отправляет локальные элементы хранения в мобильную службу, а затем извлекает из мобильной службы новые данные.

    Операция принудительной передачи выполняется в `MobileServiceClient.SyncContext`, а не в `IMobileServicesSyncTable`, и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.

    ![][7]

5. В приложении установите флажки рядом с некоторыми элементами, отметив их в локальном хранилище как выполненные.

    ![][8]

6. Нажмите кнопку **Обновить** еще раз, чтобы выполнить вызов `SyncAsync`. `SyncAsync` вызывает операции как принудительной отправки, так и принудительной передачи, но в этом случае мы могли бы удалить вызов `PushAsync`. Это происходит, потому что **операции извлечения всегда предшествует операция передачи**. Это позволяет обеспечить согласованность всех таблиц в локальном хранилище, а также связей между ними.

    ![][10]
  

##Сводка

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Дальнейшие действия

* [Обработка конфликтов с автономной поддержкой мобильных служб]

* [Использование обратимого удаления в мобильных службах][Soft Delete]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your mobile service]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Обработка конфликтов с автономной поддержкой мобильных служб]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md
[Пример автономного приложения TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Getting Started]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Приступая к работе с мобильными службами]: ../mobile-services-windows-store-get-started.md
[SQLite для Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite для Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Soft Delete]: mobile-services-using-soft-delete.md
[обратимое удаление]: mobile-services-using-soft-delete.md


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
 

<!---HONumber=Oct15_HO2-->