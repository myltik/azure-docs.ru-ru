<properties
    pageTitle="Включение автономной синхронизации для мобильного приложения Azure (Cordova) | Microsoft Azure"
    description="Использование мобильного приложения службы приложений для кэширования и синхронизации автономных данных в приложении Cordova"
    documentationCenter="cordova"
    authors="mikejo5000"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="08/14/2016"
    ms.author="mikejo"/>

# Включение автономной синхронизации для мобильного приложения Cordova

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

В этом руководстве рассматривается функция автономной синхронизации мобильных приложений Azure для Cordova. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением (просматривать, добавлять или изменять данные) даже при отсутствии подключения к сети. Изменения сохраняются в локальной базе данных; как только устройство возвращается в режим подключения к сети, эти изменения синхронизируются с удаленной службой.

В этом руководстве используется решение быстрого запуска Cordova для мобильных приложений, которые вы создадите, завершив изучение руководства [Создание приложения Apache Cordova]. При работе с этим руководством вы обновите решение быстрого запуска для добавления автономных функций мобильных приложений Azure. Мы также рассмотрим автономный код, применяемый в приложении.

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure]. Дополнительные сведения об использовании API см. в файле [README] подключаемого модуля.

## Добавление функции автономной синхронизации в решение быстрого запуска

В приложение необходимо добавить код автономной синхронизации. Для включения функции автономной синхронизации требуется подключаемый модуль cordova-sqlite-storage, который автоматически добавляется в приложение после включения подключаемого модуля мобильных приложений Azure в проект. В проект быстрого запуска добавлены оба этих подключаемых модуля.

1. В обозревателе решений Visual Studio откройте файл index.js и замените код

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    следующим:

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. Затем замените код

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

	следующим:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Предыдущие дополнения кода позволяют выполнить инициализацию локального хранилища и определить локальную таблицу, соответствующую значениям столбцов, используемых в серверной части Azure. (В этот код не нужно включать все значения столбцов.)

    Ссылку на контекст синхронизации можно получить, вызвав метод **getSyncContext**. Контекст синхронизации помогает сохранить связи между таблицами, отслеживая и отправляя изменения во всех таблицах, произведенные клиентским приложением при вызове метода **push**.

3. Замените текущий URL-адрес приложения URL-адресом вашего мобильного приложения.

4. Теперь замените код

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    следующим:

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Приведенный выше код инициализирует контекст синхронизации, а затем вызывает метод getSyncTable (вместо getTable), чтобы получить ссылку на локальную таблицу.

    Этот код использует локальную базу данных для всех операций создания, чтения, обновления и удаления (CRUD), выполняемых с таблицей.

    В этом примере выполняется простая обработка ошибок, связанных с конфликтами синхронизации. Реальное приложение будет обрабатывать различные ошибки, например ошибки состояния сети, конфликты серверов и другие. Примеры кода можно найти [здесь].

5. Теперь добавьте эту функцию, чтобы выполнить фактическую синхронизацию.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Изменения в серверную часть мобильного приложения можно отправить в любое время. Для этого в объекте **syncContext**, используемом клиентом, необходимо вызвать метод **push**. Например, можно добавить вызов к методу **syncBackend** для обработчика событий нажатия кнопки в приложении (например, кнопки "Синхронизация") или добавить вызов к функции **addItemHandler** для выполнения синхронизации после добавления нового элемента.

##Рекомендации по автономной синхронизации

В этом примере вызов метода **push** объекта **syncContext** выполняется только при запуске приложения в функции обратного вызова для входа. В реальном приложении функцию синхронизации также можно активировать вручную или при изменении состояния сети.

При извлечении из таблицы, для которой есть ожидающие локальные обновления, отслеживаемые по контексту, операция извлечения автоматически активирует отправку предыдущего контекста. При обновлении, добавлении и завершении элементов в данном примере можно опустить явный вызов **push**, так как он может быть избыточным (сначала проверьте текущее состояние функции в файле [README]).

В представленном коде запрашиваются все записи из удаленной таблицы TodoItem, однако их можно также отфильтровать путем передачи идентификатора запроса и запроса в **push**. Дополнительные сведения см. в описании пункта *Добавочная синхронизация* в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## Отключение проверки подлинности (необязательно)

Если проверка подлинности перед тестированием функции автономной синхронизации не настроена, закомментируйте функцию обратного вызова для входа, но код внутри этой функции оставьте раскомментированным.

После комментирования строк входа код должен выглядеть следующим образом.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Теперь синхронизация приложения с серверной частью Azure будет выполняться при запуске приложения, а не при входе.

## Запуск клиентского приложения

Теперь, когда автономная синхронизация включена, можно запустить клиентское приложение по крайней мере по одному разу на каждой платформе, чтобы заполнить базу данных локального хранилища. Далее вы сымитируете сценарий автономного режима и измените данные в локальном хранилище, пока приложение будет находиться в автономном режиме.

## Тестирование режима синхронизации (необязательно)

В этом разделе вы измените клиентский проект, чтобы смоделировать сценарий автономного режима, используя недействительный URL-адрес приложения для серверной части. При добавлении или изменении элементов данных эти изменения будут сохраняться в локальном хранилище, но не будут синхронизироваться с серверным хранилищем данных, пока не будет восстановлено подключение.

1. В обозревателе решений откройте файл проекта index.js и измените значение URL-адреса приложения таким образом, чтобы оно указывало на недопустимый URL-адрес, например, как показано ниже.

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. В файле index.html замените элемент `<meta>` поставщика облачных решений тем же самым недопустимым URL-адресом.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Создайте и запустите клиентское приложение. Обратите внимание, что исключение регистрируется в журнале консоли при каждой попытке приложения выполнить синхронизацию с серверной частью после входа. Любые добавляемые новые элементы существуют только в локальном хранилище, пока не будут извлечены в мобильную серверную часть. Клиентское приложение ведет себя так, как если бы оно было подключено к внутреннему серверу, поддерживающему все операции создания, чтения, обновления и удаления (CRUD).

4. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

5. (Необязательно.) С помощью Visual Studio просмотрите таблицу базы данных SQL Azure, чтобы увидеть, что данные в серверной базе данных не изменились.

	В Visual Studio в откройте **обозреватель сервера**. Перейдите к своей базе данных в **Azure** > **Базы данных SQL**. Щелкните правой кнопкой мыши базу данных и выберите пункт **Открыть в обозревателе объектов SQL Server**. Теперь можно перейти к таблице базы данных SQL и ее содержимому.


## Тестирование повторного подключения к мобильной серверной части (необязательно)

В этом разделе вы повторно подключите приложение к мобильному внутреннему серверу, имитирующему приложение, подключающееся к сети. При входе данные будут синхронизированы с мобильной серверной частью.

1. Снова откройте файл index.js и измените значение URL-адреса приложения таким образом, чтобы оно указывало на правильный URL-адрес.

2. Снова откройте файл index.html и измените URL-адрес приложения в элементе `<meta>` поставщика облачных решений.

3. Повторно выполните сборку и запустите клиентское приложение. После входа приложение пытается выполнить синхронизацию с серверной частью мобильного приложения. Убедитесь, что в журнале консоли отладки нет исключений.

4. (Необязательно.) Просмотрите обновленные данные, используя обозреватель объектов SQL Server или инструмент REST, например Fiddler. Обратите внимание, что данные синхронизированы между базой данных в серверной части и локальным хранилищем.

    Обратите внимание, данные синхронизированы между базой данных и локальным хранилищем, и они содержат элементы, которые вы добавили, пока ваше приложение было вне сети.

## Дополнительные ресурсы

* [Автономная синхронизация данных в мобильных приложениях Azure]

* [Облачное покрытие: автономная синхронизация в мобильных службах Azure] \(примечание: видео рассказывает о мобильных службах, однако точно так же автономная синхронизация работает в мобильных приложениях Azure)

* [Набор средств Visual Studio для Apache Cordova]

## Дальнейшие действия

* Просмотрите образцы более сложных функций автономной синхронизации, таких как устранение конфликтов, в [репозитории GitHub].
* Просмотрите сведения об API функции автономной синхронизации в файле [README].

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Создание приложения Apache Cordova]: app-service-mobile-cordova-get-started.md
[README]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[здесь]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[репозитории GitHub]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Автономная синхронизация данных в мобильных приложениях Azure]: app-service-mobile-offline-data-sync.md
[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Набор средств Visual Studio для Apache Cordova]: https://www.visualstudio.com/ru-RU/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0824_2016-->
