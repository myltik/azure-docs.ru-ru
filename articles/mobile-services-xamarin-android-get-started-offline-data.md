<properties urlDisplayName="Using Offline Data" pageTitle="С помощью автономных данных в мобильных службах (Xamarin Android) | Центр разработчиков мобильных устройств" metaKeywords ="" description="Узнайте, как использовать мобильные службы Azure для кэширования и синхронизации автономных данных в приложении на Xamarin для Android" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Использование автономной синхронизации данных в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

В этом разделе показано, как использовать автономные возможности мобильных служб Azure. Эти функции позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Автономные функции позволяют синхронизировать локальные изменения с мобильной службой после подключения к сети. 

В этом учебнике вы обновите приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными] для поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

>[WACOM.NOTE] Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Windows Azure для хранения и извлечения данных из приложения Магазина Windows. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].
>
> Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>. 

Завершенный проект по данному учебнику доступен [здесь](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android).

В этом учебнике рассматриваются следующие основные действия:

1. [Обновление приложения для поддержки автономных функций]
2. [Тестирование приложения, подключенного к мобильной службе]

Для работы с данным учебником требуется следующее:

* Visual Studio с [расширением Xamarin] **или** [Xamarin Studio] 
* Завершение учебника [Начало работы с мобильными службами] или [Начало работы с данными].
* [Azure Mobile Services SDK версия 1.3.0-beta2][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store версия 1.0.0-beta2][SQLite store nuget]

>[WACOM.NOTE] Приведенные ниже инструкции подразумевают, что вы используете Visual Studio 2012 или более позднюю версию с расширением Xamarin. При использовании Xamarin Studio используйте встроенную поддержку диспетчера пакетов NuGet.

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Мобильные службы Azure с автономной синхронизацией позволяет конечным пользователям взаимодействовать с локальной базой данных в случае, когда сеть недоступна. Для использования этих возможностей в приложении необходимо инициализировать `MobileServiceClient.SyncContext` в локальном хранилище. Затем вы ссылаетесь на таблицу с помощью интерфейса `IMobileServiceSyncTable` .

1. В Visual Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. В обозревателе решений удалите ссылку на **Azure Mobile Services SDK** в разделе **Компоненты**.

2. Установите предварительную версию пакета Mobile Services SQLiteStore, используя следующую команду в консоли диспетчера пакетов. 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    При этом также будут установлены все требуемые зависимые компоненты.
    
3. В узле ссылок удалите обращения к `System.IO`, `System.Runtime` и `System.Threading.Tasks`.

### Внесите изменения в файл ToDoActivity.cs

1. Добавление объявлений

		using Microsoft.WindowsAzure.MobileServices.Sync;
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
		using System.IO;

2. Изменение типа члена `ToDoActivity.toDoTable` с `IMobileServiceTable<>` на `IMobileServiceSyncTable<>`

3. В методе `OnCreate(Bundle)` после строки, инициализирующей член `client`, добавьте следующий код.

	    // existing initializer
	    client = new MobileServiceClient (applicationURL, applicationKey, progressHandler);
	
		// new code to initialize the SQLite store
	    string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "test1.db");
	
	    if (!File.Exists(path))
	    {
	        File.Create(path).Dispose();
	    }

	    var store = new MobileServiceSQLiteStore(path);
	    store.DefineTable<ToDoItem>();
	
	    await client.SyncContext.InitializeAsync(store, new TodoSyncHandler(this));

4. В том же методе измените строку, которая инициализирует `toDoTable`, чтобы использовать метод `GetSyncTable<>` вместо `GetTable<>`.

		toDoTable = client.GetSyncTable <ToDoItem> ();

5. Измените метод `OnRefreshItemsSelected`, чтобы добавить вызовы в `PushAsync`  и `PullAsync`.

		async void OnRefreshItemsSelected ()
		{
		    await client.SyncContext.PushAsync();
			await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    await RefreshItemsFromTableAsync();
		}

### Измените файл ToDoItem.cs 

1. Добавьте инструкцию "using". 

        using Microsoft.WindowsAzure.MobileServices; 


2. Добавьте следующие компоненты в класс ToDoItem.
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

## <a name="test-online-app"></a>Тестирование приложения 

В этом разделе вы протестируете метод `SyncAsync`, выполняющий синхронизацию локального хранилища с базой данных мобильной службы.

1. В Visual Studio нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, используемом по умолчанию для этого проекта.

2. Обратите внимание, что список элементов в приложении пуст. В результате изменений, внесенных в код в предыдущем разделе, приложение больше не считывает элементы из мобильной службы. Вместо этого происходит считывание из локального хранилища. 

3. Добавьте элементы в список дел.

    ![][1]


4. Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы. При использовании серверной части .NET для мобильной службы можно нажать кнопку **Управление** для вашей базы данных в расширении SQL Azure, чтобы выполнить запрос к таблице.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

5. В приложении нажмите кнопку **Обновить**. После этого приложение вызовет методы `MobileServiceClient.SyncContext.PushAsync` и `IMobileServiceSyncTable.PullAsync()`, затем `RefreshTodoItems`, чтобы обновить приложение элементами из локального хранилища. 

    В результате этой push-операции база данных мобильной службы получает данные из хранилища. Она выполняется в контексте `MobileServiceClient.SyncContext`, а не в `IMobileServicesSyncTable`, и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.
    
    Операция извлечения, напротив, приводит к получению записей только из той таблицы, которая была указана. Если для данной таблицы в контексте синхронизации имеются операции, ожидающие в очереди, то операция PushAsync будет вызвана пакетом SDK для мобильных служб.
        
    ![][3] 



    ![][2]


  

##Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс `IMobileServiceSyncTable` и инициализировали `MobileServiceClient.SyncContext` с локальным хранилищем. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Когда мы хотели синхронизировать локальное хранилище с сервером, мы использовали методы `IMobileServiceSyncTable.PullAsync` и `MobileServiceClient.SyncContext.PushAsync`.

*   Для отправки изменений на сервер мы вызвали метод `IMobileServiceSyncContext.PushAsync()`. Он является членом объекта `IMobileServicesSyncContext`, а не таблицы синхронизации, поскольку изменения будут переданы во все таблицы:

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер.
   
* Для извлечения данных из таблицы на сервере в приложение мы вызвали метод `IMobileServiceSyncTable.PullAsync`.

    Операции извлечения всегда предшествует операция передачи.  

    В этом примере используется перегруженная версия **PullAsync()**, которая позволяет указать ключ запроса и сам запрос. Запрос используется для добавочной синхронизации. Пакет SDK мобильных служб отслеживает последнюю обновленную отметку времени после каждой операции pull, выполненной успешно. При следующей операции pull будут возвращены только новые записи. Если ключ запроса не указан, для таблицы синхронизации будет выполнена полная синхронизация.

## Дальнейшие действия

Полную версию этого учебника можно взять из нашего репозитория примеров [GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android).

<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Использование клиентского компонента Xamarin для мобильных служб Azure]

<!-- Anchors. -->
[Обновление приложения для поддержки автономных функций]: #enable-offline-app
[Тестирование приложения, подключенного к мобильной службе]: #test-online-app
[Дальнейшие действия]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png
[2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png



<!-- URLs. -->
[Обработка конфликтов с автономной поддержкой мобильных служб]: /ru-ru/documentation/articles/mobile-services-xamarin-android-handling-conflicts-offline-data/ 
[Приступая к работе с данными]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started/
[Использование клиентского компонента Xamarin для мобильных служб Azure]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[Мобильные службы SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[Хранилище SQLite nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Модуль Xamarin]: http://xamarin.com/visual-studio
[Надстройки NuGet для Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
