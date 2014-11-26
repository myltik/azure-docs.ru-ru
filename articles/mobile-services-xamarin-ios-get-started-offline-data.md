<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="donnam,wesmc" editor="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam,wesmc" />

# Приступая к работе с синхронизацией автономных данных в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

В этом разделе показано, как использовать автономные возможности мобильных служб Azure. Эти функции позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Автономные функции позволяют синхронизировать локальные изменения с мобильной службой после подключения к сети.

В этом учебнике вы обновите приложение, созданное в учебнике [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], для поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

> [WACOM.NOTE] Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных из приложения Магазина Windows. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].
>
> Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

В этом учебнике рассматриваются следующие основные действия:

1.  [Обновление приложения для поддержки автономных функций][Обновление приложения для поддержки автономных функций]
2.  [Тестирование приложения, подключенного к мобильной службе][Тестирование приложения, подключенного к мобильной службе]

Для работы с данным учебником требуется следующее:

-   XCode 4.5 и iOS 6.0 (или более поздние версии)
-   Visual Studio с [расширением Xamarin][расширением Xamarin] **или** [Xamarin Studio][Xamarin Studio] в OS X
-   Выполнение заданий учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными]
-   [Azure Mobile Services SDK версия 1.3.0-alpha3 (или позднее)][Azure Mobile Services SDK версия 1.3.0-alpha3 (или позднее)]
-   [Azure Mobile Services SQLite Store версия 1.0.0-alpha2 (или позднее)][Azure Mobile Services SQLite Store версия 1.0.0-alpha2 (или позднее)]

> [WACOM.NOTE] Указанные ниже инструкции подразумевают, что вы используете Visual Studio 2012 или выше с расширением Xamarin. Если Xamarin Studio используется в OS X, большая часть инструкций остается без изменения, однако следует также установить [дополнение NuGet для Xamarin][дополнение NuGet для Xamarin], чтобы можно было легко добавлять в проект пакеты Mobile Services NuGet предварительной версии.

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Автономные функции мобильных служб Azure позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Для использования этих возможностей в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальное хранилище. Затем укажите ссылку на таблицу с помощью интерфейса `IMobileServiceSyncTable`.

1.  В Visual Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными]. В обозревателе решений удалите ссылку на **Azure Mobile Services SDK** в разделе **Компоненты**.

2.  Установите предварительную версию пакета Mobile Services SQLiteStore, используя следующую команду в консоли диспетчера пакетов.

        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    При этом также будут установлены все требуемые зависимые компоненты.

3.  В узле ссылок удалите ссылки на `System.IO`, `System.Runtime` и `System.Threading.Tasks`.

### Внесите изменения в файл QSTodoService.cs

Измените класс `QSTodoService`, включив использование автономных функций Mobile Services для локального хранилища SQLite.

1.  Добавьте в начало файла следующие инструкции using.

        using Microsoft.WindowsAzure.MobileServices; 
        using Microsoft.WindowsAzure.MobileServices.Sync; 
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2.  Измените тип члена `todoTable` с `IMobileServiceTable` на `IMobileServicesSyncTable`

        IMobileServiceSyncTable<ToDoItem> todoTable; 

3.  В конструкторе `QSTodoService` измените инициализатор для `todoTable`:

        todoTable = client.GetSyncTable <ToDoItem> ();

4.  В конструкторе `QSTodoService` добавьте вызов `SQLitePCL.CurrentPlatform.Init()` как второй строки кода:

        QSTodoService ()
        {
            CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

            // Initialize the Mobile Service client with your URL and key
            client = new MobileServiceClient (applicationURL, applicationKey, this);

            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetSyncTable <ToDoItem> ();
        }

5.  В классе `QSTodoService` определите новый метод `InitializeAsync`:

        public async Task InitializeStoreAsync()
        {
            string path = "syncstore.db";
            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();
            await client.SyncContext.InitializeAsync(store);
        }

6.  В классе `QSTodoService` определите новый метод `SyncAsync`:

        public async Task SyncAsync()
        {
            try
            {
                await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync();
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

### Измените файл ToDoItem.cs

-   Добавьте выражение "using":

        using Microsoft.WindowsAzure.MobileServices; 

-   Добавьте в класс `ToDoItem` следующие методы.

        [Version]
        public string Version { get; set; }


        public override string ToString()
        {
            return "Text: " + Text + "\nComplete: " + Complete + "\n";
        }

### Внесите изменения в файл QSTodoListViewController.cs

Внесите изменения в `QSTodoListViewController`, чтобы реализовать вызов нового метода `SyncAsync`, когда пользователь делает жест обновления.

1.  Добавьте вызов `InitializeStoreAsync` в `ViewDidLoad()` после инициализации `todoService`:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();

            ...    // the rest of the code in the method is unchanged
        }

2.  Измените метод `AddRefreshControl` так, чтобы `SyncAsync` вызывался до вызова `RefreshAsync`.

        RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
            await RefreshAsync();
        }; 

<!--  DM: commenting this out because this tutorial doesn't show OC conflict handling ### Edit ToDoItem.cs   Modify the strongly-type data class to add a version field  1. In the top of the file, add the using statement:           using Microsoft.WindowsAzure.MobileServices;   2. Add the following members to the class `ToDoItem`:           [Version]         public string Version { get; set; }                  public override string ToString()         {             return "Text: " + Text + "\nComplete: " + Complete + "\n";         }  -->

## <a name="test-online-app"></a> Тестирование приложения

В этом разделе вы протестируете метод `SyncAsync`, выполняющий синхронизацию локального хранилища с базой данных мобильной службы.

1.  В Visual Studio нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для этого проекта.

2.  Обратите внимание, что список элементов в приложении пуст. В результате изменений, внесенных в код в предыдущем разделе, приложение больше не считывает элементы из мобильной службы. Вместо этого происходит считывание из локального хранилища.

3.  Добавьте элементы в список дел.

    ![][0]

4.  Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы. При использовании серверной части .NET для мобильной службы можно нажать кнопку **Управление** для вашей базы данных в расширении SQL Azure, чтобы выполнить запрос к таблице.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

5.  Находясь в приложении, сделайте жест обновления, потянув вниз список элементов. После этого приложение вызовет методы `MobileServiceClient.SyncContext.PushAsync` и `IMobileServiceSyncTable.PullAsync()`, после чего будет выполнен код `RefreshTodoItems` для обновления приложения элементами из локального хранилища.

    Эта операция приводит к тому, что база данных мобильной службы получает данные из хранилища. Она выполняется в `MobileServiceClient.SyncContext`, а не в `IMobileServicesSyncTable`, и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.

    Операция извлечения, напротив, приводит к получению записей только из той таблицы, которая была указана. Если для данной таблицы в контексте синхронизации имеются операции, ожидающие в очереди, операция `PushAsync` будет по умолчанию вызвана пакетом Mobile Services SDK.

    ![][1]

    ![][2]

## Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс `IMobileServiceSyncTable` и инициализировали `MobileServiceClient.SyncContext` с локальным хранилищем. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Когда мы хотели синхронизировать локальное хранилище с сервером, то использовали методы `IMobileServiceSyncTable.PullAsync` и `MobileServiceClient.SyncContext.PushAsync`.

-   Для отправки изменений на сервер мы вызвали метод `IMobileServiceSyncContext.PushAsync()`. Этот метод является участником `IMobileServicesSyncContext`, а не таблицы синхронизации, поскольку изменения будут переданы во все таблицы.

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер.

-   Для извлечения данных из таблицы на сервере в приложение мы вызвали метод `IMobileServiceSyncTable.PullAsync`.

    Операции извлечения всегда предшествует операция передачи.

    Также существуют перегрузки метода **PullAsync()**, позволяющие указать запрос. Обратите внимание, что в предварительной версии автономных функций мобильных служб метод **PullAsync** будет считывать все строки в соответствующей таблице (или запросе), а не будет пытаться прочитать только те строки, которые появились, например, после последней синхронизации. Если строки уже есть в локальной таблице синхронизации, они останутся без изменений.

## Дальнейшие действия

<!--* [Handling conflicts with offline support for Mobile Services] -->

-   [Использование клиентского компонента Xamarin для мобильных служб Azure][Использование клиентского компонента Xamarin для мобильных служб Azure]


<!-- Images -->


  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28
  [Обновление приложения для поддержки автономных функций]: #enable-offline-app
  [Тестирование приложения, подключенного к мобильной службе]: #test-online-app
  [расширением Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [дополнение NuGet для Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
  [0]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
  [1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png
  [2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
  [Использование клиентского компонента Xamarin для мобильных служб Azure]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/
