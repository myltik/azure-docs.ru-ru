<properties
    pageTitle="Включение автономной синхронизации для мобильного приложения Azure (Xamarin iOS)"
    description="Использование мобильного приложения службы приложений для кэширования и синхронизации автономных данных в приложении Xamarin iOS"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="08/11/2015"
    ms.author="wesmc"/>

# Включение автономной синхронизации для мобильного приложения Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

## Обзор

В этом учебнике представлена функция автономной синхронизации мобильных приложений Azure для Xamarin.iOS. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением — просматривать, добавлять или изменять данные — даже при отсутствии подключения к сети. Изменения сохраняются в локальной базе данных; как только устройство возвращается в режим подключения к сети, эти изменения синхронизируются с удаленной службой.

Из этого учебника вы узнаете, как обновить проект приложения Xamarin.iOS, описанного в учебнике [Создание приложения Xamarin iOS], чтобы обеспечить поддержку автономных функций мобильных приложений Azure.

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## Требования

Для работы с данным учебником требуется следующее:

* Visual Studio 2013
* [Расширение Xamarin] Visual Studio **или** [Xamarin Studio] на OS X
* Завершение учебника [Создание приложения Xamarin iOS]. В текущем учебнике используется завершенное приложение, описанное в этом учебнике.

##<a name="review"></a>Проверка конфигурации серверного проекта (необязательно)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## Просмотр кода синхронизации клиента

Клиентский проект Xamarin, скачанный при изучении учебника [Создание приложения Xamarin iOS], уже содержит код, который поддерживает автономную синхронизацию с использованием локальной базы данных SQLite. Вот краткое содержание материала, уже включенного в код учебника. Общие сведения об этой функции см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

* Прежде чем можно будет выполнить операции с таблицами, необходимо инициализировать локальное хранилище. Инициализация базы данных локального хранилища происходит, когда `QSTodoListViewController.ViewDidLoad()` выполняет `QSTodoService.InitializeStoreAsync()`. При этом создается новая локальная база данных SQLite с помощью класса `MobileServiceSQLiteStore`, предоставленного клиентским пакетом SDK для мобильных приложений Azure. 

	Метод `DefineTable` создает в локальном хранилище таблицу, соответствующую полям в указанном типе, в данном случае это `ToDoItem`. Тип необязательно должен включать в себя все столбцы, которые находятся в удаленной базе данных. Можно хранить и подмножество столбцов.

		// QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* Элемент `todoTable` в `QSTodoService` имеет тип `IMobileServiceSyncTable`, а не `IMobileServiceTable`. В данном случае все операции создания, чтения, обновления и удаления (CRUD) с таблицей будут направлены в базу данных локального хранилища.
 
	Можно решить, когда эти изменения будут передаваться в серверную часть мобильного приложения Azure, вызвав `IMobileServiceSyncContext.PushAsync()` с использованием контекста синхронизации для клиентского подключения. Контекст синхронизации помогает сохранить связи между таблицами, отслеживания и отправляя изменения во всех таблицах, измененных клиентским приложением при вызове `PushAsync`.

	Представленный код вызывает метод `QSTodoService.SyncAsync()` для синхронизации при каждом обновлении списка элементов Todoitem или добавлении либо завершении элемента Todoitem. Поэтому он выполняет синхронизацию после каждого локального изменения, выполняя отправку по контексту синхронизации и извлечение по таблице синхронизации. Но важно понимать, что при извлечении из таблицы, для которой есть ожидающие локальные обновления, отслеживаемые по контексту, операция извлечения сначала автоматически инициирует отправку контекста. Поэтому в таких случаях (при обновлении, добавлении и завершении элементов) можно опустить явный вызов `PushAsync`. Он является избыточным.

    В представленном запрашиваются все записи из удаленной таблицы `TodoItem`, однако их можно также отфильтровать путем передачи идентификатора запроса и запроса в `PushAsync`. Дополнительные сведения см. в подразделе *Добавочная синхронизация* раздела [Автономная синхронизация данных в мобильных приложениях Azure].

	<!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
-->	// QSTodoService.cs

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## Запуск клиентского приложения

Запустите клиентское приложение по крайней мере один раз, чтобы заполнить базу данных локального хранилища. В следующем разделе вы будете имитировать сценарий автономного режима и изменять данные в локальном хранилище, когда приложение находится в автономном режиме.


## Обновление режима синхронизации клиентского приложения

В этом разделе вы измените клиентский проект, чтобы смоделировать сценарий автономного режима, используя недействительный URL-адрес приложения для серверной части. При добавлении или изменении элементов данных эти изменения будут сохраняться в локальном хранилище, но не будут синхронизироваться с серверным хранилищем данных, пока не будет восстановлено подключение.

1. В верхней части `QSTodoService.cs` измените инициализацию `applicationURL` и `gatewayURL`, указав недействительные URL-адреса:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; 
        const string gatewayURL = @"https://your-gateway.azurewebsites.xxx";


2. Добавьте дополнительный `catch` для класса `Exception` в `QSTodoService.SyncAsync` для записи сообщения об исключении в консоль.

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
        }

3. Выполните сборку и запустите клиентское приложение. Добавьте несколько новых элементов списка дел и обратите внимание на исключение, добавляемое в журнал в консоли при каждой попытке синхронизации с серверной частью мобильного приложения. Эти новые элементы существуют только в локальном хранилище, пока не будут принудительно переданы на мобильный внутренний сервер. Клиентское приложение ведет себя так, как если бы оно было подключено к внутреннему серверу, поддерживающему все операции создания, чтения, обновления и удаления (CRUD).

4. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

5. (Необязательно.) С помощью Visual Studio просмотрите таблицу базы данных SQL Azure, чтобы увидеть, что данные в серверной базе данных не изменились.

   В Visual Studio в откройте **обозреватель сервера**. Перейдите к своей базе данных в **Azure**->**Базы данных SQL**. Щелкните правой кнопкой мыши базу данных и выберите пункт **Открыть в обозревателе объектов SQL Server**. Теперь можно перейти к таблице базы данных SQL и ее содержимому.

6. (Необязательно.) Воспользуйтесь инструментом REST, например Fiddler или Postman, чтобы выполнить запрос к мобильной серверной части с помощью запроса GET вида `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`. 

## Обновление клиентского приложения для повторного подключения мобильной серверной части

В этом разделе вы повторно подключите приложение к мобильному внутреннему серверу, имитирующему приложение, подключающееся к сети. При выполнении жеста обновления данные будут синхронизированы с мобильным внутренним сервером.

1. Откройте `QSTodoService.cs`. Исправьте `applicationURL` и `gatewayURL`, чтобы указать правильные URL-адреса.

2. Повторно выполните сборку и запустите клиентское приложение. После запуска приложение пытается выполнить синхронизацию с серверной частью мобильного приложения Azure. Убедитесь в отсутствии исключений в журнале консоли отладки.

3. (Необязательно.) Просмотрите обновленные данные, используя обозреватель объектов SQL Server или инструмент REST, например Fiddler. Обратите внимание, что данные синхронизированы между базой данных серверной части мобильного приложения Azure и локальным хранилищем.

    Обратите внимание, данные синхронизированы между базой данных и локальным хранилищем, и они содержат элементы, которые вы добавили, пока ваше приложение было вне сети.

## Дополнительные ресурсы

* [Автономная синхронизация данных в мобильных приложениях Azure]

* [Облачное покрытие: автономная синхронизация в мобильных службах Azure] (примечание: видео рассказывает о мобильных службах, однако, точно так же автономная синхронизация работает в мобильных приложениях Azure)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Создание приложения Xamarin iOS]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[Автономная синхронизация данных в мобильных приложениях Azure]: ../app-service-mobile-offline-data-sync-preview.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Расширение Xamarin]: http://xamarin.com/visual-studio
 
[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=August15_HO8-->