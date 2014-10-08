<properties linkid="develop-mobile-tutorials-get-started-offline-data-wp8" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Windows Phone application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Приступая к работе с синхронизацией автономных данных в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone" class="current">Windows Phone</a>
<a href="/en-us/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/en-us/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/en-us/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

В этом разделе показано, как использовать автономные возможности мобильных служб Azure. Автономные функции мобильных служб Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Автономные функции позволяют синхронизировать локальные изменения с мобильной службой после подключения к сети.

В этом учебнике вы обновите приложение, созданное в учебнике [Приступая к работе с мобильными службами][] или [Приступая к работе с данными][], для поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

> [WACOM.NOTE] Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных из приложения Windows Phone. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами][].

В этом учебнике рассматриваются следующие основные действия:

1.  [Обновление приложения для поддержки автономных функций][]
2.  [Тестирование приложения в автономном сценарии][]
3.  [Обновление приложения для повторного подключения мобильной службы][]
4.  [Тестирование приложения, подключенного к мобильной службе][]

Для работы с данным учебником требуется следующее:

-   Visual Studio 2012
-   [Windows Phone 8 SDK][]
-   Изучение учебника [Приступая к работе с мобильными службами][] или [Приступая к работе с данными][].
-   [Пакет SDK мобильных служб Azure версии 1.3.0-alpha4 (или последующих версий)][]
-   [Azure Mobile Services SQLite Store версии 1.0.0-alpha4 (или последующих версий)][]
-   [SQLite для Windows Phone 8][]

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][].

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Автономные функции мобильных служб Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Для использования этих возможностей в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальное хранилище. Затем укажите ссылку на таблицу с помощью интерфейса `IMobileServiceSyncTable`.

В этом разделе SQLite используется как локальное хранилище для автономных функций.

> [WACOM.NOTE] Этот раздел можно пропустить и просто загрузить версию проекта "Приступая к работе", который уже содержит поддержку автономного режима. Сведения о загрузке проекта с поддержкой автономных функций см. в разделе [Приступая к работе с автономными функциями, пример для Windows Phone][].

1.  Установите SQLite для проектов Windows Phone 8. Этот компонент можно установить по ссылке [SQLite для Windows Phone 8][].

    > [WACOM.NOTE] Если вы используете Internet Explorer, после перехода по ссылке для установки SQLite может потребоваться загрузить VSIX-файл как ZIP-файл. Сохраните файл в папку на жестком диске с расширением .vsix вместо .zip. Дважды щелкните VSIX-файл в проводнике Windows для запуска установки.

2.  В Visual Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами][] или [Приступая к работе с данными][]. В обозревателе решений щелкните правой кнопкой мыши элемент **Ссылки** в узле проекта и добавьте ссылку на **SQLite для Windows Phone** в разделе **Windows Phone**\>**Расширения**.

    ![][]

3.  Для среды выполнения SQLite требуется изменить архитектуру процессора проекта на **x86**, **x64** или **ARM**. Параметр **Любой процессор** не поддерживается. Измените архитектуру процессора на один из поддерживаемых параметров, которые требуется протестировать.

    ![][1]

4.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект клиентского приложения и выберите пункт**Управление пакетами NuGet**, чтобы открыть диспетчер пакетов NuGet. Выполните поиск **SQLiteStore** для установки пакета **WindowsAzure.MobileServices.SQLiteStore**.

    ![][2]

5.  В обозревателе решений Visual Studio откройте файл MainPage.xaml.cs. Добавьте в начало файла следующие инструкции using.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6.  В файле Mainpage.xaml.cs замените объявление `todoTable` на объявление типа `IMobileServicesSyncTable`, которое инициализируется при вызове метода `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7.  В файле MainPage.xaml.cs обновите класс `TodoItem`, чтобы он включал системное свойство **Версия**, следующим образом.

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

8.  В файле MainPage.xaml.cs обновите обработчик событий `OnNavigatedTo`, чтобы это был метод `async`, инициализирующий контекст синхронизации клиента с хранилищем SQLite. Хранилище SQLite создается с таблицей, соответствующей схеме таблицы мобильной службы, но оно должно содержать системное свойство **Version**, добавленное на предыдущем шаге.

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

9.  В обозревателе решений Visual Studio откройте файл MainPage.xaml. Найдите определение для кнопки **Обновить**. Замените его следующим определением панели стека.

    Этот код добавляет две кнопки с обработчиком события нажатия для операций **Push** и **Pull**. Кнопки располагаются горизонтально, там, где находится кнопка обновления. Сохраните файл.

        <StackPanel  Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2" HorizontalAlignment="Center">
          <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Width="160">Refresh</Button>
          <Button Name="ButtonPush" Click="ButtonPush_Click" Width="160">Push</Button>
          <Button Name="ButtonPull" Click="ButtonPull_Click" Width="160">Pull</Button>
        </StackPanel>

    Также измените текст для блоков текста, чтобы он соответствовал следующему снимку экрана.

    ![][3]

10. В файле MainPage.xaml.cs добавьте обработчики событий нажатия для кнопок **Push** и **Pull** и сохраните файл.

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
                MessageBox.Show("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
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
                MessageBox.Show(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
            }
        }

11. Пока не запускайте приложение. Нажмите клавишу **F7** для повторного построения проекта. Убедитесь, что ошибок нет.

## <a name="test-offline-app"></a>Тестирование приложения в автономном сценарии

В этом разделе вы разорвете связь приложения с мобильной службой для имитации автономного сценария. Затем вы добавите некоторые элементы данных, которые будут храниться в локальном хранилище.

Обратите внимание, что в этом разделе приложение не должно быть подключено к мобильной службе. Поэтому кнопки **Push** и **Pull** будут вызывать исключения при их нажатии. В следующем разделе вы подключите это клиентское приложение к мобильной службе еще раз, чтобы проверить операции **Push** и **Pull** для синхронизации хранилища с базой данных мобильной службы.

1.  В обозревателе решений Visual Studio откройте файл App.xaml.cs. Измените инициализацию **MobileServiceClient** на недопустимый адрес, заменив "**azure-mobile.net**" на "**azure-mobile.xxx**" для вашего URL-адреса. Затем сохраните файл.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2.  В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Введите новый элемент списка дел и нажмите кнопку **Сохранить**. Новые элемент списка дел существует только в локальном хранилище, пока он не будет перемещен в мобильную службу. Клиентское приложение ведет себя так, если бы оно было подключено к мобильной службе, поддерживающей все операции создания, чтения, обновления и удаления (CRUD).

    ![][4]

3.  Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

## <a name="update-online-app"></a>Обновление приложения для повторного подключения мобильной службы

В этом разделе вы повторно подключите приложение к мобильной службе. Эта процедура имитирует переход приложения из автономного состояния в оперативное состояние с мобильной службой.

1.  В обозревателе решений Visual Studio откройте файл App.xaml.cs. Измените инициализацию **MobileServiceClient** обратно на правильный адрес, заменив "**azure-mobile.xxx**" на "**azure-mobile.net**" для вашего URL-адреса. Затем сохраните файл.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

## <a name="test-online-app"></a>Тестирование приложения, подключенного к мобильной службе

В этом разделе вы протестируете операции передачи и извлечения данных для синхронизации локального хранилища с базой данных мобильной службы.

1.  В Visual Studio нажмите клавишу **F5**, чтобы построить и запустить приложение. Обратите внимание, что данные выглядят так же, как в автономном сценарии, хотя приложение теперь подключено к мобильной службе. Это вызвано тем, что это приложение всегда работает с объектом `IMobileServiceSyncTable`, который указывает на локальное хранилище.

    ![][4]

2.  Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы. При использовании серверной части .NET для мобильной службы можно нажать кнопку **Управление** для вашей базы данных в расширении SQL Azure, чтобы выполнить запрос к таблице.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

    ![][5]

3.  В приложении нажмите кнопку **Push**. После этого приложение вызовет методы `MobileServiceClient.SyncContext.PushAsync` и `RefreshTodoItems` для обновления приложения с помощью элементов из локального хранилища. Эта операция приводит к тому, что база данных мобильной службы получает данные из хранилища. Однако локальное хранилище не получает элементы из базы данных мобильной службы.

    Операция push выполняется в `MobileServiceClient.SyncContext`, а не в `IMobileServicesSyncTable`, и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.

    ![][6]

4.  В локальном хранилище приложения есть несколько новых элементов.

    ![][7]

5.  На этот раз нажмите кнопку **Pull** в приложении. Приложение вызывает только методы `IMobileServiceSyncTable.PullAsync()` и `RefreshTodoItems`. Обратите внимание, что все данные из базы данных мобильной службы были извлечены в локальное хранилище и показаны в приложении. Кроме того, обратите внимание, что все данные в локальном хранилище также были переданы в базу данных мобильной службы. Это происходит, потому что **операции извлечения всегда предшествует операция передачи**.

    ![][8]

    ![][9]

## Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс `IMobileServiceSyncTable` и инициализировали `MobileServiceClient.SyncContext` с локальным хранилищем. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Когда мы хотели синхронизировать локальное хранилище с сервером, то использовали методы `IMobileServiceSyncTable.PullAsync` и `MobileServiceClient.SyncContext.PushAsync`.

-   Для отправки изменений на сервер мы вызвали метод `IMobileServiceSyncContext.PushAsync()`. Этот метод является участником `IMobileServicesSyncContext`, а не таблицы синхронизации, поскольку изменения будут переданы во все таблицы.

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CRUD), будут отправлены на сервер.

-   Для извлечения данных из таблицы на сервере в приложение мы вызвали метод `IMobileServiceSyncTable.PullAsync`.

    Операции извлечения всегда предшествует операция передачи.

    Также существуют перегрузки метода **PullAsync()**, позволяющие указать запрос. Обратите внимание, что в предварительной версии автономных функций мобильных служб метод **PullAsync** будет считывать все строки в соответствующей таблице (или запросе), а не будет пытаться прочитать только те строки, которые появились, например, после последней синхронизации. Если строки уже есть в локальной таблице синхронизации, они останутся без изменений.

-   Сведения о загрузке проекта с поддержкой автономных функций см. в разделе [Приступая к работе с автономными функциями, пример для Windows Phone][].

## Дальнейшие действия

-   [Обработка конфликтов с автономной поддержкой мобильных служб][]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [Магазин Windows C\#]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Магазин Windows C#"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /en-us/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /en-us/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /en-us/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [Приступая к работе с мобильными службами]: /en-us/documentation/articles/mobile-services-windows-phone-get-started/
  [Приступая к работе с данными]: /en-us/documentation/articles/mobile-services-windows-phone-get-started-data/
  [Обновление приложения для поддержки автономных функций]: #enable-offline-app
  [Тестирование приложения в автономном сценарии]: #test-offline-app
  [Обновление приложения для повторного подключения мобильной службы]: #update-online-app
  [Тестирование приложения, подключенного к мобильной службе]: #test-online-app
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
  [Пакет SDK мобильных служб Azure версии 1.3.0-alpha4 (или последующих версий)]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-alpha4
  [Azure Mobile Services SQLite Store версии 1.0.0-alpha4 (или последующих версий)]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-alpha4
  [SQLite для Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28
  [Приступая к работе с автономными функциями, пример для Windows Phone]: http://go.microsoft.com/fwlink/?LinkId=397952
  []: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
  [1]: ./media/mobile-services-windows-phone-get-started-offline-data/vs-select-processor-architecture.png
  [2]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitestore-nuget.png
  [3]: ./media/mobile-services-windows-phone-get-started-offline-data/ui-screenshot.png
  [4]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-offline-app-run1.png
  [5]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse.png
  [6]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse2.png
  [7]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run2.png
  [8]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run3.png
  [9]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse3.png
  [Обработка конфликтов с автономной поддержкой мобильных служб]: /en-us/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data/
