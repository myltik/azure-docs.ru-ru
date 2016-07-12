<properties
	pageTitle="Включение автономной синхронизации для приложения универсальной платформы Windows (UWP) с помощью мобильных приложений| Служба приложений Azure"
	description="Узнайте, как использовать мобильное приложение Azure для кэширования и синхронизации автономных данных в приложении универсальной платформы Windows (UWP)."
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
	ms.date="05/14/2016"
	ms.author="wesmc"/>

# Включение автономной синхронизации для приложения для Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Обзор

В этом учебнике показано, как добавить поддержку автономных функций в приложение универсальной платформы Windows (UWP) с помощью серверной части мобильного приложения Azure. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением (просматривать, добавлять или изменять данные) даже при отсутствии подключения к сети. Изменения хранятся в локальной базе данных; после подключения устройства к сети эти изменения синхронизируются с удаленным внутренним сервером.

Из этого учебника вы узнаете, как обновить проект приложения UWP, описанного в учебнике [Создание приложения для Windows], чтобы обеспечить поддержку автономных функций мобильных приложений Azure. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакеты расширений доступа к данным. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## Требования

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Выполнение заданий на странице [Создание приложения для Windows][create a windows app].
* [Хранилище SQLite для мобильных служб Azure][sqlite store nuget]
* [SQLite для разработки универсальной платформы Windows](http://www.sqlite.org/downloads)

## Обновление клиентского приложения для поддержки автономных функций

Автономные функции мобильных приложений Azure позволяют взаимодействовать с локальной базой данных в случае автономной работы. Для использования этих функций в приложении необходимо инициализировать [SyncContext][synccontext] в локальном хранилище. Затем необходимо сослаться на таблицу с помощью интерфейса [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite используется как локальное хранилище на устройстве.

1. Установите [среду выполнения SQLite для универсальной платформы Windows для универсальных приложений](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. В Visual Studio откройте диспетчер пакетов NuGet для проекта приложения UWP, выполненного в учебнике [Создание приложения Windows], а затем найдите и установите пакет NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.

4. В обозревателе решений щелкните правой кнопкой мыши **Ссылки** > **Добавить ссылку** > **Универсальные приложения Windows** > **Расширения**, а затем включите **SQLite для универсальной платформы Windows** и **среду выполнения Visual C++ 2015 для приложений универсальной платформы Windows**.

    ![Добавить ссылку на SQLite UWP][1]

5. Откройте файл MainPage.xaml.cs и раскомментируйте в начале файла следующие инструкции `using`:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  
        using Microsoft.WindowsAzure.MobileServices.Sync;         

6. Закомментируйте строку кода, которая инициализирует `todoTable` как **IMobileServiceTable**, а затем раскомментируйте строку кода, которая инициализирует `todoTable` как **IMobileServiceSyncTable**:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); 

7. В области `Offline sync` файла MainPage.xaml.cs раскомментируйте следующие методы:

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

	В `SyncAsync` операция принудительной отправки запускается из [SyncContext][synccontext], после чего следует добавочная синхронизация. Контекст синхронизации отслеживает изменения, внесенные клиентом во все таблицы. Подробнее об этом поведении см. в разделе [Автономная синхронизация данных в мобильных приложениях Azure].

8. В обработчике событий `OnNavigatedTo` раскомментируйте вызов `InitLocalStoreAsync`. Если вы уже выполнили инструкции из [руководства по проверке подлинности](app-service-mobile-windows-store-dotnet-get-started-users.md), сейчас это необходимо сделать в методе `AuthenticateAsync`.

9. Раскомментируйте вызовы к [PushAsync] в методах `InsertTodoItem` и `UpdateCheckedTodoItem`, а затем раскомментируйте вызов к `SyncAsync` в методе `ButtonRefresh_Click`.

10. В методе `SyncAsync` добавьте следующие обработчики исключений:

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); 
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
                  "you can try your pull again when connected with your backend.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

	В автономном режиме метод [PullAsync][PullAsync] может вернуть результат [MobileServicePushFailedException][MobileServicePushFailedException], при этом свойство [PushResult.Status][Status] будет иметь значение [CancelledByNetworkError][CancelledByNetworkError]. Это происходит, когда неявная отправка пытается отправить ожидающие обновления до извлечения, и потому завершается ошибкой. Дополнительные сведения см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

11. В Visual Studio нажмите клавишу **F5**, чтобы выполнить сборку и запустить клиентское приложение. Приложение работает так же, как и до включения автономной синхронизации. Тем не менее, локальная база данных теперь заполняется данными, которые можно использовать в автономном сценарии. Затем можно создать автономный сценарий и использовать локально хранимые данные для запуска приложения.

## <a name="update-sync"></a>Обновление приложения для отключения от серверной части

В этом разделе вы разорвете связь с серверной частью мобильного приложения для имитации автономного режима. При добавлении элементов данных обработчик исключений сообщает, что приложение работает в автономном режиме. В этом состоянии новые элементы добавляются в локальное хранилище и будут синхронизированы с серверной частью мобильного приложения при следующей отправке данных в подключенном состоянии.

1. Измените файл App.xaml.cs в общем проекте. Закомментируйте инициализацию **MobileServiceClient** и добавьте следующие строки, которые используют недопустимый URL-адрес мобильного приложения:

         public static MobileServiceClient MobileService =
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	Если в приложении используется проверка подлинности, единый вход завершится ошибкой. Чтобы продемонстрировать поведение в автономном режиме, отключите на устройстве Wi-Fi и сотовую связь и включите авиарежим.

2. Нажмите клавишу **F5**, чтобы выполнить сборку и запустить приложение. Обратите внимание, синхронизацию не удалось выполнить при обновлении во время запуска приложения.
 
3. Введите новые элементы. Учтите, что при каждом нажатии кнопки **Сохранить** отправка завершается сбоем с состоянием [CancelledByNetworkError]. Однако новые элементы списка существуют в локальном хранилище, пока они не будут перемещены в серверную часть мобильного приложения.

	В рабочем приложении при подавлении этих исключений клиентское приложение ведет себя так, как если бы оно по-прежнему было подключено к серверной части мобильного приложения.

4. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

5. В Visual Studio в откройте **обозреватель сервера**. Перейдите к своей базе данных, выбрав **Azure** > **Базы данных SQL**. Щелкните правой кнопкой мыши базу данных и выберите пункт **Открыть в обозревателе объектов SQL Server**. Теперь можно перейти к таблице базы данных SQL и ее содержимому. Убедитесь, что данные в серверной базе данных не изменились.

6. (Необязательно.) Воспользуйтесь инструментом REST, например Fiddler или Postman, чтобы выполнить запрос к мобильной серверной части с помощью запроса GET вида `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения к серверной части мобильного приложения

В этом разделе приложение будет повторно подключено к серверной части мобильного приложения. Эта процедура имитирует переход приложения из автономного состояния в оперативное состояние для серверной части мобильного приложения. При первом запуске приложения обработчик событий `OnNavigatedTo` вызовет `InitLocalStoreAsync`. Это, в свою очередь, вызовет `SyncAsync` для синхронизации локального хранилища с серверной базой данных. Поэтому приложение будет пытаться выполнить синхронизацию при запуске.

1. Откройте файл App.xaml.cs в общем проекте и раскомментируйте предыдущую инициализацию `MobileServiceClient`, чтобы использовать правильный URL-адрес мобильного приложения.

2. Нажмите клавишу **F5**, чтобы перестроить и запустить приложение. Приложение синхронизирует локальные изменения с серверной частью мобильного приложения Azure, применяя операции отправки и извлечения, сразу же после выполнения обработчика событий `OnNavigatedTo`.

3. (Необязательно.) Просмотрите обновленные данные, используя обозреватель объектов SQL Server или инструмент REST, например Fiddler. Обратите внимание, что данные синхронизированы между базой данных серверной части мобильного приложения Azure и локальным хранилищем.

4. В приложении установите флажки рядом с некоторыми элементами, отметив их в локальном хранилище как выполненные.

  `UpdateCheckedTodoItem` вызывает `SyncAsync` для синхронизации каждого выполненного элемента с серверной частью мобильного приложения. `SyncAsync` вызывает отправку и извлечение. Однако следует отметить, что **всякий раз, когда выполняется извлечение из таблицы, в которую клиент внес изменения, сначала всегда автоматически будет выполняться отправка для контекста синхронизации клиента**. Это позволяет обеспечить согласованность всех таблиц в локальном хранилище, а также связей между ними. Поэтому в данном случае мы можем удалить вызов `PushAsync`, так как он автоматически выполняется при выполнении извлечения. Если не знать об этом, такое поведение может привести к непредвиденной отправке данных. Подробнее об этом поведении см. в разделе [Автономная синхронизация данных в мобильных приложениях Azure].


##Сводные данные API

Для поддержки автономных функций мобильных служб мы использовали интерфейс [IMobileServiceSyncTable] и инициализировали [MobileServiceClient.SyncContext][synccontext] с использованием локальной базы данных SQLite. В автономном режиме обычные операции CRUD для мобильных приложений работают так, как если бы приложение по-прежнему было подключено, но операции выполнялись в локальном хранилище. Для синхронизации локального хранилища с сервером используются следующие методы.

*  **[PushAsync]** Так как этот метод является членом объекта [IMobileServicesSyncContext], изменения во всех таблицах будут отправляться в серверную часть приложения. Только записи с локальными изменениями будут отправляться на сервер.

* **[PullAsync]** Извлечение запускается из объекта [IMobileServiceSyncTable]. При наличии отслеживаемых изменений в таблице выполняется неявная отправка, чтобы убедиться в том, что все таблицы в локальном хранилище и их взаимосвязи остаются согласованными. Параметр *pushOtherTables* определяет, будет ли выполнена для других таблиц в контексте неявная отправка. Параметр *query* использует [IMobileServiceTableQuery&lt;U&gt;][IMobileServiceTableQuery] или строку запроса OData для фильтрации возвращаемых данных. Параметр *queryId* используется для определения добавочной синхронизации. Дополнительные сведения см. в статье [Автономная синхронизация данных в мобильных приложениях Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** Приложение должно периодически вызывать этот метод, чтобы удалить устаревшие данные из локального хранилища. Используйте параметр *force*, чтобы удалить все изменения, которые не были синхронизированы.

Дополнительные сведения об этих понятиях см. в статье [Автономная синхронизация данных в мобильных приложениях Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## Подробнее

В следующих разделах приводятся дополнительные сведения о функции автономной синхронизации мобильных приложений:

* [Автономная синхронизация данных в мобильных приложениях Azure]
* [Облачное покрытие: автономная синхронизация в мобильных службах Azure]. (Обратите внимание: видео посвящено мобильным службам, но автономная синхронизация работает в мобильных приложениях Azure точно так же.)
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
[Автономная синхронизация данных в мобильных приложениях Azure]: app-service-mobile-offline-data-sync.md
[create a windows app]: app-service-mobile-windows-store-dotnet-get-started.md
[Создание приложения Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[Создание приложения для Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: приложения с поддержкой автономного режима в мобильных службах Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0629_2016-->