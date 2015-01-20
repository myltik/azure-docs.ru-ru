<properties urlDisplayName="Using Offline Data" pageTitle="Использование автономных данных в мобильных службах (Xamarin Android) | Центр мобильных разработок" metaKeywords="" description="Узнайте, как использовать мобильные службы Azure для кэширования и синхронизации автономных данных в приложении на Xamarin для Android" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Использование синхронизации автономных данных в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

В этом разделе показано, как использовать автономные возможности мобильных служб Azure. Эти функции позволяют взаимодействовать с локальной базой данных при работе с мобильной службой в автономном сценарии. Автономные функции позволяют синхронизировать локальные изменения с мобильной службой после подключения к сети. 

В этом учебнике вы обновите приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными], для поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

>[WACOM.NOTE] Этот учебник поможет вам разобраться, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных приложения для Магазина Windows. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].
>
> Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения можно найти на странице <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатное пробное использование Azure</a>. 

Готовый проект, выполненный по этому учебнику, можно найти [здесь](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android).

В этом учебнике рассматриваются следующие основные действия:

1. [Обновление приложения для поддержки автономных функций]
2. [Тестирование приложения, подключенного к мобильной службе]

Для работы с данным учебником требуется следующее:

* Visual Studio с [расширением Xamarin] **или** [Xamarin Studio] 
* Выполнение заданий учебника [Приступая к работе с мобильными службами] или [Приступая к работе с данными]
* [Пакет SDK для мобильных служб Azure версии 1.3.0][Mobile Services SDK Nuget]
* [Хранилище SQLite для мобильных служб Azure версии 1.0.0][SQLite store nuget]

>[WACOM.NOTE] Дальнейшие инструкции предполагают, что вы используете Visual Studio 2012 или более поздних версий с расширением Xamarin. При использовании Xamarin Studio воспользуйтесь встроенной поддержкой диспетчера пакетов NuGet.

## <a name="enable-offline-app"></a>Обновление приложения для поддержки автономных функций

Автономная синхронизация мобильных служб Azure позволяет взаимодействовать с локальной базой данных без доступа к сети. Чтобы использовать эти возможности в приложении, необходимо инициализировать MobileServiceClient.SyncContext в локальное хранилище и создать ссылку на таблицу с помощью интерфейса IMobileServiceSyncTable.

1. В Visual Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. В обозревателе решений удалите ссылку на **пакет SDK для мобильных служб Azure** в разделе **Компоненты**.

2. Установите предварительную версию пакета Mobile Services SQLiteStore, используя следующую команду в консоли диспетчера пакетов. 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    При этом также будут установлены все требуемые зависимые компоненты.
    
3. В узле ссылок удалите ссылки на элементы System.IO, System.Runtime и System.Threading.Tasks.

### Внесите изменения в файл ToDoActivity.cs

1. Добавление объявлений

		using Microsoft.WindowsAzure.MobileServices.Sync;
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
		using System.IO;

2. Измените тип члена ToDoActivity.toDoTable с IMobileServiceTable<> на IMobileServiceSyncTable<>.

3. В методе OnCreate(Bundle) после строки, инициализирующей члена client, добавьте такой код:

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

4. В том же методе измените строку, инициализирующую toDoTable, чтобы вместо метода GetTable<> использовался GetSyncTable<>:

		toDoTable = client.GetSyncTable <ToDoItem> ();

5. Измените метод OnRefreshItemsSelected, добавив вызовы PushAsync и PullAsync:

		async void OnRefreshItemsSelected ()
		{
		    await client.SyncContext.PushAsync();
			await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    await RefreshItemsFromTableAsync();
		}

### Измените файл ToDoItem.cs 

1. Добавьте выражение "using": 

        using Microsoft.WindowsAzure.MobileServices; 


2. Добавьте в класс ToDoItem такие члены:
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

## <a name="test-online-app"></a>Тестирование приложения 

В этом разделе вы протестируете метод SyncAsync, выполняющий синхронизацию локального хранилища с базой данных мобильной службы.

1. В Visual Studio нажмите кнопку **Запуск**, чтобы создать проект и запустить приложение в эмуляторе iPhone, который используется по умолчанию для этого проекта.

2. Обратите внимание, что список элементов в приложении пуст. В результате изменений, внесенных в код в предыдущем разделе, приложение больше не считывает элементы из мобильной службы. Вместо этого происходит считывание из локального хранилища. 

3. Добавьте элементы в список дел.

    ![][1]


4. Войдите на портал управления Microsoft Azure и посмотрите на базу данных мобильной службы. Если ваша служба использует серверную часть JavaScript для мобильных служб, вы можете просмотреть данные на вкладке **Данные** мобильной службы. При использовании серверной части .NET для мобильной службы можно нажать кнопку **Управление** для вашей базы данных в расширении SQL Azure, чтобы выполнить запрос к таблице.

    Обратите внимание, что данные не синхронизированы между базой данных и локальным хранилищем.

5. В приложении нажмите кнопку **Обновить**. После этого приложение вызовет методы MobileServiceClient.SyncContext.PushAsync и IMobileServiceSyncTable.PullAsync(), а затем выполнит код RefreshTodoItems для обновления приложения элементами из локального хранилища. 

    Эта операция приводит к тому, что база данных мобильной службы получает данные из хранилища. Она выполняется в MobileServiceClient.SyncContext, а не в IMobileServicesSyncTable, и передает изменения всех таблиц, связанных с этим контекстом синхронизации. Это позволяет реализовать сценарии, где существуют связи между таблицами.
    
    Операция извлечения, напротив, приводит к получению записей только из той таблицы, которая была указана. Если для данной таблицы в контексте синхронизации имеются операции, ожидающие в очереди, операция PushAsync будет по умолчанию вызвана пакетом SDK для мобильных служб.
        
    ![][3] 



    ![][2]


  

##Сводка

Для поддержки автономных функций мобильных служб мы использовали интерфейс IMobileServiceSyncTable и инициализировали MobileServiceClient.SyncContext с локальным хранилищем. В этом случае локальным хранилищем была база данных SQLite.

Обычные операции CRUD для мобильных служб работают так, как если бы приложение по-прежнему подключено, но все операции выполняются с локальным хранилищем.

Для синхронизации локального хранилища с сервером использовались методы IMobileServiceSyncTable.PullAsync и MobileServiceClient.SyncContext.PushAsync.

*  Для отправки изменений на сервер мы вызвали метод IMobileServiceSyncContext.PushAsync(). Это член объекта IMobileServicesSyncContext, а не таблицы синхронизации, поскольку изменения будут переданы во все таблицы.

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер.
   
* Для извлечения данных из таблицы на сервере в приложение мы вызвали метод IMobileServiceSyncTable.PullAsync.

    Операции извлечения всегда предшествует операция передачи.  

    Методу **PullAsync()** требуется идентификатор запроса и запрос. Идентификатор запроса используется для добавочной синхронизации. Необходимо использовать отдельный идентификатор для каждого запроса в вашем приложении. Пакет SDK для мобильных служб отслеживает метки времени последнего обновления после каждой успешной операции извлечения. При каждой последующей операции извлекаются только записи, появившиеся со времени предыдущей. Если в качестве идентификатора запроса указано значение нуль, для таблицы синхронизации будет выполняться полная синхронизация.


## Дальнейшие действия

Пример проекта, выполненного по этому учебнику, можно скачать из нашего [репозитория примеров на GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android).

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

[Nuget-пакет SDK для мобильных служб]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[Nuget-пакет SQLite]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
[Xamarin Studio]: http://xamarin.com/download
[Расширение Xamarin]: http://xamarin.com/visual-studio
[Надстройка NuGet для Xamarin]: https://github.com/mrward/monodevelop-nuget-addin

<!--HONumber=35.2-->
