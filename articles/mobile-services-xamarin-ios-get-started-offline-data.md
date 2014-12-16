<properties urlDisplayName="Using Offline Data" pageTitle="Использование автономных данных в мобильных службах (Xamarin iOS) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Использование автономной синхронизации данных в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

В этом разделе показано, как использовать автономные возможности мобильных служб Azure. Эти функции позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Автономные функции позволяют синхронизировать локальные изменения с мобильной службой после подключения к сети. 

В этом учебнике вы обновите приложение, созданное в учебнике  [Начало работы с мобильными службами] или [Начало работы с данными], для добавления поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

>[WACOM.NOTE] Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Windows Azure для хранения и извлечения данных из приложения Магазина Windows. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].
>
>Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>. 

В этом учебнике рассматриваются следующие основные действия:

1. [Обновление приложения для поддержки автономных функций]
2. [Тестирование приложения, подключенного к мобильной службе]

Для работы с данным учебником требуется следующее:

* XCode 4.5 и iOS 6.0 (или более поздние версии) 
* Visual Studio с [расширением Xamarin ]** или **[ Xamarin Studio] на OS X
* Завершение учебника [Начало работы с мобильными службами] или [Начало работы с данными].
* [Пакет SDK для мобильных служб Azure версии 1.3.0-beta2 (или более поздней версии).][Mobile Services SDK Nuget]
* [SQLite Store для мобильных служб Azure версии 1.0.0-beta2 (или более поздней версии).][SQLite store nuget]

>[WACOM.NOTE] Приведенные ниже инструкции подразумевают, что вы используете Visual Studio 2012 или более позднюю версию с расширением Xamarin. При использовании Xamarin Studio в OS X, используйте встроенный диспетчер пакетов NuGet.

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Мобильные службы Azure с автономной синхронизацией позволяет конечным пользователям взаимодействовать с локальной базой данных в случае, когда сеть недоступна. Для использования этих возможностей в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальном хранилище. Затем нужно обратиться к таблице с помощью интерфейса `IMobileServiceSyncTable`.

Завершенный проект по данному учебнику доступен [здесь](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS).

1. В Visual Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. В обозревателе решений удалите ссылку на **Azure Mobile Services SDK** в разделе **Компоненты**.

2. Установите предварительную версию пакета Mobile Services SQLiteStore, используя следующую команду в консоли диспетчера пакетов. 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    При этом также будут установлены все требуемые зависимые компоненты.
    
3. В узле ссылок удалите обращения к `System.IO`, `System.Runtime` и `System.Threading.Tasks`.

### Внесите изменения в файл QSTodoService.cs 

Измените класс `QSTodoService`, включив использование автономных функций Mobile Services для локального хранилища SQLite.

1. Добавьте в начало файла следующие инструкции using.

		using Microsoft.WindowsAzure.MobileServices; 
		using Microsoft.WindowsAzure.MobileServices.Sync; 
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2. Измените тип члена `todoTable` с `IMobileServiceTable` на `IMobileServicesSyncTable`

		IMobileServiceSyncTable<ToDoItem> todoTable; 

3. В конструкторе для `QSTodoService` измените инициализатор для `todoTable`:

        todoTable = client.GetSyncTable <ToDoItem> ();

4. В конструкторе для `QSTodoService` добавьте вызов в `SQLitePCL.CurrentPlatform.Init()` в виде второй строки кода:

		QSTodoService ()
		{
			CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

			// Initialize the Mobile Service client with your URL and key
			client = new MobileServiceClient (applicationURL, applicationKey, this);

			// Create an MSTable instance to allow us to work with the TodoItem table
			todoTable = client.GetSyncTable <ToDoItem> ();
		}
 
5. В классе `QSTodoService` определите новый метод `InitializeAsync`:
 
		public async Task InitializeStoreAsync()
		{
		    string path = "syncstore.db";
		    var store = new MobileServiceSQLiteStore(path);
		    store.DefineTable<ToDoItem>();
		    await client.SyncContext.InitializeAsync(store);
		}

6. В классе `QSTodoService` определите новый метод `SyncAsync`:
 
		public async Task SyncAsync()
		{
		    try
		    {
		        await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    }
		    catch (MobileServiceInvalidOperationException e)
		    {
		        Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
		    }
		}

### Измените файл ToDoItem.cs 

1. Добавьте инструкцию "using": 

        using Microsoft.WindowsAzure.MobileServices; 


2. Добавьте следующие члены в класс `ToDoItem`.
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

### Внесите изменения в файл QSTodoListViewController.cs 

Измените `QSTodoListViewController` для вызова нового метода `SyncAsync` при выполнении пользователем жеста обновления.
 
1. Добавьте вызов `InitializeStoreAsync` в `ViewDidLoad()` после инициализации `todoService`:

		public override async void ViewDidLoad ()
		{
		    base.ViewDidLoad ();
		
		    todoService = QSTodoService.DefaultService;
			await todoService.InitializeStoreAsync();
			
			...    // the rest of the code in the method is unchanged
		}

2. Измените метод `AddRefreshControl` для вызова `SyncAsync` перед вызовом `RefreshAsync`:


		RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
			await RefreshAsync();
		}; 

<!-- 
DM: commenting this out because this tutorial doesn't show OC conflict handling
### Edit ToDoItem.cs 

Modify the strongly-type data class to add a version field

1. In the top of the file, add the using statement: 

        using Microsoft.WindowsAzure.MobileServices; 

2. Add the following members to the class `ToDoItem`:
 
		[Version]
		public string Version { get; set; }
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

-->

## <a name="test-online-app"></a>Тестирование приложения 

В этом разделе вы протестируете метод `SyncAsync`, выполняющий синхронизацию локального хранилища с базой данных мобильной службы.

1. В Visual Studio нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для этого проекта.

2. Обратите внимание, что список элементов в приложении пуст. В результате изменений, внесенных в код в предыдущем разделе, приложение больше не считывает элементы из мобильной службы. Вместо этого происходит считывание из локального хранилища. 

3. Добавьте элементы в список дел.

    ![][1]


4. Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы. При использовании серверной части .NET для мобильной службы можно нажать кнопку **Управление** для вашей базы данных в расширении SQL Azure, чтобы выполнить запрос к таблице.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

5. Находясь в приложении, сделайте жест обновления, потянув список элементов вниз. При этом приложение вызовет `MobileServiceClient.SyncContext.PushAsync` и `IMobileServiceSyncTable.PullAsync(), затем `RefreshTodoItems` для актуализации приложения элементами из локального хранилища. 

    Эта операция приводит к тому, что база данных мобильной службы получает данные из хранилища. Она выполняется в контексте `MobileServiceClient.SyncContext`, а не в `IMobileServicesSyncTable`, и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.
    
    Операция извлечения, напротив, приводит к получению записей только из той таблицы, которая была указана. Если для данной таблицы в контексте синхронизации имеются операции, ожидающие в очереди, то операция `PushAsync` будет явно вызвана Mobile Services SDK.
        
    ![][3] 



    ![][2]


  

##Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс `IMobileServiceSyncTable` и инициализировали `MobileServiceClient.SyncContext` с локальным хранилищем. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Когда мы хотели синхронизировать локальное хранилище с сервером, мы использовали методы `IMobileServiceSyncTable.PullAsync` и `MobileServiceClient.SyncContext.PushAsync`.

*   Для отправки изменений на сервер мы вызвали метод `IMobileServiceSyncContext.PushAsync()`. Он является членом объекта `IMobileServicesSyncContext`, а не таблицы синхронизации, поскольку он будет передавать изменения во все таблицы:

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер.
   
* Для извлечения данных из таблицы на сервере в приложение мы вызвали метод `IMobileServiceSyncTable.PullAsync`.

    Операции извлечения всегда предшествует операция передачи.  

    В этом примере используется перегруженная версия **PullAsync()**, которая позволяет указать ключ запроса и сам запрос. Запрос используется для добавочной синхронизации. Пакет SDK мобильных служб отслеживает последнюю обновленную отметку времени после каждой операции pull, выполненной успешно. При следующей операции pull будут возвращены только новые записи. Если ключ запроса не указан, для таблицы синхронизации будет выполнена полная синхронизация.

## Дальнейшие действия

Полную версию этого учебника можно взять из нашего [репозитория примеров GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS).


<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Использование клиентского компонента Xamarin для мобильных служб Azure]

<!-- Anchors. -->
[Обновление приложения для поддержки автономных функций]: #enable-offline-app
[Тестирование приложения, подключенного к мобильной службе]: #test-online-app
[Дальнейшие действия]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
[2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png



<!-- URLs. -->
[Обработка конфликтов с автономной поддержкой мобильных служб]: /ru-ru/documentation/articles/mobile-services-xamarin-ios-handling-conflicts-offline-data/ 
[Приступая к работе с данными]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
[Использование клиентского компонента Xamarin для мобильных служб Azure]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[Мобильные службы SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[Хранилище SQLite nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Модуль Xamarin]: http://xamarin.com/visual-studio
[Надстройки NuGet для Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
