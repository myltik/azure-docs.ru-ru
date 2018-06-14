---
title: Включение автономной синхронизации для мобильного приложения Azure (Cordova) | Документация Майкрософт
description: Использование мобильного приложения службы приложений для кэширования и синхронизации автономных данных в приложении Cordova
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: c12328a441a8cc438fa3e974863cc8adf8651b50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593720"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Включение автономной синхронизации для мобильного приложения Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

В этом руководстве рассматривается функция автономной синхронизации мобильных приложений Azure для Cordova. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением &mdash;просматривать, добавлять или изменять данные &mdash; даже при отсутствии подключения к сети. Изменения сохраняются в локальной базе данных.  Как только устройство возвращается в режим подключения к сети, эти изменения синхронизируются с удаленной службой.

В этом руководстве используется решение быстрого запуска Cordova для мобильных приложений, которые вы создадите, завершив изучение руководства [Создание приложения Apache Cordova]. При работе с этим руководством вы обновите решение быстрого запуска для добавления автономных функций мобильных приложений Azure.  Мы также рассмотрим автономный код, применяемый в приложении.

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure]. Сведения об использовании API см. в [документации по API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Добавление функции автономной синхронизации в решение быстрого запуска
В приложение необходимо добавить код автономной синхронизации. Для включения функции автономной синхронизации требуется подключаемый модуль cordova-sqlite-storage, который автоматически добавляется в приложение после включения подключаемого модуля мобильных приложений Azure в проект. В проект быстрого запуска добавлены оба этих подключаемых модуля.

1. В обозревателе решений Visual Studio откройте файл index.js и замените код

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    следующим:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Затем замените код

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    следующим:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Предыдущие дополнения кода позволяют выполнить инициализацию локального хранилища и определить локальную таблицу, соответствующую значениям столбцов, используемых в серверной части Azure. (В этот код не нужно включать все значения столбцов.)  Поле `version` обслуживается серверной частью мобильной службы и используется для разрешения конфликтов.

    Ссылку на контекст синхронизации можно получить, вызвав метод **getSyncContext**. Контекст синхронизации помогает сохранить связи между таблицами, отслеживая и отправляя изменения во всех таблицах, измененных клиентским приложением при вызове `.push()` .

3. Замените текущий URL-адрес приложения URL-адресом вашего мобильного приложения.

4. Теперь замените код

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    следующим:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
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

    В этом примере выполняется простая обработка ошибок, связанных с конфликтами синхронизации. Реальное приложение будет обрабатывать различные ошибки, например ошибки состояния сети, конфликты серверов и другие. Примеры кода можно найти [этом примере].

5. Теперь добавьте эту функцию, чтобы выполнить фактическую синхронизацию.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Изменения в серверную часть мобильного приложения можно отправить в любое время, вызвав **syncContext.push()**. Например, можно вызвать **syncBackend** в обработчике событий кнопки, привязанной к кнопке синхронизации.

## <a name="offline-sync-considerations"></a>Рекомендации по автономной синхронизации

В этом примере вызов метода **push** объекта **syncContext** выполняется только при запуске приложения в функции обратного вызова для входа.  В реальном приложении функцию синхронизации также можно активировать вручную или при изменении состояния сети.

При извлечении из таблицы, для которой есть ожидающие локальные обновления, отслеживаемые по контексту, операция извлечения автоматически активирует отправку. При обновлении, добавлении и завершении элементов в данном примере можно опустить явный вызов **push**, так как он может быть избыточен.

В представленном коде запрашиваются все записи из удаленной таблицы TodoItem, однако их можно также отфильтровать путем передачи идентификатора запроса и запроса в **push**. Дополнительные сведения см. в разделе *Добавочная синхронизация* статьи [Автономная синхронизация данных в мобильных приложениях Azure].

## <a name="optional-disable-authentication"></a>Отключение проверки подлинности (необязательно)

Если вы не хотите настраивать аутентификацию перед тестированием функции автономной синхронизации, то закомментируйте функцию обратного вызова для входа, но код внутри этой функции оставьте раскомментированным.  После комментирования строк входа в систему код выглядит следующим образом.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Теперь при запуске приложение синхронизируется с серверной частью Azure.

## <a name="run-the-client-app"></a>Запуск клиентского приложения
Теперь, когда автономная синхронизация включена, можно запустить клиентское приложение по крайней мере по одному разу на каждой платформе, чтобы заполнить базу данных локального хранилища. Далее вы сымитируете сценарий автономного режима и измените данные в локальном хранилище, пока приложение будет находиться в автономном режиме.

## <a name="optional-test-the-sync-behavior"></a>Тестирование режима синхронизации (необязательно)
В этом разделе вы измените клиентский проект, чтобы смоделировать сценарий автономного режима, используя недействительный URL-адрес приложения для серверной части. При добавлении или изменении элементов данных эти изменения будут сохраняться в локальном хранилище, но не будут синхронизироваться с серверным хранилищем данных, пока не будет восстановлено подключение.

1. В обозревателе решений откройте файл проекта index.js и измените значение URL-адреса приложения таким образом, чтобы оно указывало на недопустимый URL-адрес, например, как показано в коде ниже.

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. В файле index.html замените элемент `<meta>` поставщика облачных решений тем же самым недопустимым URL-адресом.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Создайте и запустите клиентское приложение. Обратите внимание, что исключение регистрируется в журнале консоли при каждой попытке приложения выполнить синхронизацию с серверной частью после входа. Добавляемые новые элементы существуют только в локальном хранилище, пока не будут извлечены в серверную часть мобильной службы. Клиентское приложение ведет себя так, как если бы оно было подключено к серверной части.

4. Закройте приложение и перезапустите его, чтобы убедиться, что новые элементы сохранены в локальном хранилище.

5. (Необязательно.) С помощью Visual Studio просмотрите таблицу базы данных SQL Azure, чтобы увидеть, что данные в серверной базе данных не изменились.

    В Visual Studio в откройте **обозреватель сервера**. Перейдите к своей базе данных в **Azure**->**Базы данных SQL**. Щелкните правой кнопкой мыши базу данных и выберите пункт **Открыть в обозревателе объектов SQL Server**. Теперь можно перейти к таблице базы данных SQL и ее содержимому.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>Тестирование повторного подключения к мобильной серверной части (необязательно)

В этом разделе вы повторно подключите приложение к серверной части мобильной службы, имитирующей приложение, подключающееся к сети. При входе данные синхронизируются с серверной частью мобильной службы.

1. Снова откройте файл index.js и восстановите URL-адрес приложения.
2. Снова откройте файл index.html и измените URL-адрес приложения в элементе `<meta>` поставщика облачных решений.
3. Повторно выполните сборку и запустите клиентское приложение. После входа приложение пытается выполнить синхронизацию с серверной частью мобильного приложения. Убедитесь, что в журнале консоли отладки нет исключений.
4. (Необязательно.) Просмотрите обновленные данные, используя обозреватель объектов SQL Server или инструмент REST, например Fiddler. Обратите внимание, что данные синхронизированы между базой данных в серверной части и локальным хранилищем.

    Обратите внимание, данные синхронизированы между базой данных и локальным хранилищем, и они содержат элементы, которые вы добавили, пока ваше приложение было вне сети.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Автономная синхронизация данных в мобильных приложениях Azure]
* [Инструменты Visual Studio для Apache Cordova]

## <a name="next-steps"></a>Дополнительная информация
* Ознакомьтесь с более сложными функциями автономной синхронизации, такими как устранение конфликтов, в [этом примере].
* Ознакомьтесь со справочной информацией об API автономной синхронизации в [документации по API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Создание приложения Apache Cordova]: app-service-mobile-cordova-get-started.md
[этом примере]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Автономная синхронизация данных в мобильных приложениях Azure]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Инструменты Visual Studio для Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
