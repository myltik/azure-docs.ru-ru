<properties
	pageTitle="Включение автономной синхронизации для мобильного приложения (iOS)"
	description="Использование мобильных приложений службы приложений для кэширования и синхронизации автономных данных в приложении iOS"
	documentationCenter="ios"
	authors="lindydonna"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="donnam"/>

# Включение автономной синхронизации для мобильного приложения iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]

В этом учебнике рассматривается функция автономной синхронизации мобильных приложений для iOS. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением — просматривать, добавлять или изменять данные — даже при отсутствии подключения к сети. Изменения хранятся в локальной базе данных; после подключения устройства к сети эти изменения синхронизируются с удаленным внутренним сервером.

Автономная синхронизация может применяться в следующих случаях:

* Повышение скорости реагирования приложений путем локального кэширования данных сервера в устройстве
* Защита приложений от потери сетевой связности
* Предоставление конечным пользователям возможности создания и изменения данных даже в том случае, когда отсутствует сетевой доступ, поддерживая сценарии с минимальной связностью или при отсутствии связности
* Синхронизация данных между несколькими устройствами и обнаружение конфликтов, когда два устройства изменяют одну и ту же запись

Если вы первый раз работаете с мобильными приложениями, сначала пройдите учебник [Создание приложения iOS].

##<a name="review"></a>Проверка конфигурации сервера проекта (необязательное действие)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## <a name="get-app"></a>Получить пример автономного приложения списка дел

В разделе [Пример репозитория мобильных приложений на GitHub] клонируйте репозиторий и откройте проект [Пример автономного приложения iOS] в Xcode.

### Бета-версия пакета SDK
Чтобы добавить поддержку автономной работы в существующее приложение, получите последнюю версию [бета-версии пакета SDK для iOS](http://aka.ms/gc6fex).

## <a name="review-sync"></a>Обзор кода синхронизации мобильных приложений

Автономная синхронизация мобильных приложений позволяет конечным пользователям взаимодействовать с локальной базой данных, если сеть недоступна. Для использования этих возможностей в приложении необходимо инициализировать контекст синхронизации `MSClient` и указать ссылку на локальное хранилище. Затем укажите ссылку на таблицу с помощью интерфейса `MSSyncTable`.

В этом разделе рассматривается представленный в примере код автономной синхронизации.

1. Обратите внимание, что в **QSTodoService.m** элемент `syncTable` имеет тип `MSSyncTable`. Автономная синхронизация использует этот интерфейс таблицы синхронизации вместо `MSTable`. При использовании таблицы синхронизации все операции направляются в локальное хранилище и синхронизируются только с удаленным внутренним сервером с явными push-уведомлениями и операциями получения.

    Для получения ссылки на таблицу синхронизации используйте метод `syncTableWithName`. Чтобы удалить функциональность автономной синхронизации, используйте `tableWithName`.

2. Прежде чем можно будет выполнить операции с таблицами, необходимо инициализировать локальное хранилище. Это соответствующий код в методе `QSTodoService.init`:

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    Он создает локальное хранилище с использованием интерфейса `MSCoreDataStore`, который предоставляется в пакете SDK для мобильных приложений. Можно также предоставить другое локальное хранилище, реализовав протокол `MSSyncContextDataSource`.

    Первый параметр `initWithDelegate` используется для указания обработчика конфликтов. Поскольку мы передали `nil`, то получим обработчик конфликтов по умолчанию, который завершается с ошибкой всякий раз, когда возникает конфликт.

	<!-- For details on how to implement a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services]. -->

3. Методы `pullData` и `syncData` выполняют операцию фактической синхронизации: `syncData` сначала передает новые изменения, а затем вызывает `pullData` для получения данных из удаленного внутреннего сервера.

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    В свою очередь, метод `pullData` получает новые данные, удовлетворяющие запросу:

        -(void)pullData:(QSCompletionBlock)completion
        {
            MSQuery *query = [self.syncTable query];

            // Pulls data from the remote server into the local table.
            // We're pulling all items and filtering in the view
            // query ID is used for incremental sync
            [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                // Let the caller know that we have finished
                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

    В `syncData` мы сначала вызываем `pushWithCompletion` в контексте синхронизации. Этот метод является членом `MSSyncContext` (а не самой таблицы синхронизации), поэтому изменения будут переданы во все таблицы. Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер. Затем вызывается вспомогательный метод `pullData`, который вызывает метод `MSSyncTable.pullWithQuery` для извлечения удаленных данных и их сохранения в локальной базе данных.

    Обратите внимание, что в этом примере операция принудительной отправки не является обязательной. Если в контексте синхронизации для таблице, осуществляющей операцию принудительной отправки, имеются ожидающие изменения, то операция принудительного получения всегда предварительно выполняет операцию принудительной отправки. Однако при наличии нескольких таблиц синхронизации, рекомендуется явно вызывать операцию принудительной отправки, чтобы обеспечить согласованность для связанных таблиц.

    Метод `pullWithQuery` позволяет указать запрос для фильтрации извлекаемых записей. В этом примере запрос просто извлекает все записи в удаленной таблице `TodoItem`.

    Второй параметр для `pullWithQuery` — это идентификатор запроса, который используется для *добавочной синхронизации*. Добавочная синхронизация извлекает только те записи, которые были изменены с момента последней синхронизации, используя для этого метку времени `UpdatedAt` записи (в локальном хранилище она называется `ms_updatedAt`). Идентификатор запроса должен быть описательной строкой, уникальной для каждого логического запроса в приложении. Чтобы явно отказаться от добавочной синхронизации, передайте `nil` в качестве идентификатора запроса. Помните, что это может привести к снижению производительности, поскольку при каждой операции принудительного получения будут извлекаться все записи.

	<!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to purge the local store.
 -->

5. В классе `QSTodoService` метод `syncData` вызывается после операций, которые изменяют данные, — `addItem` и `completeItem`. Он также вызывается из `QSTodoListViewController.refresh`, поэтому пользователь получает последние данные при каждом выполнении жеста обновления. Приложение также выполняет синхронизацию при запуске, поскольку `QSTodoListViewController.init` вызывает `refresh`.

    Поскольку `syncData` вызывается при каждом изменении данных, это приложение предполагает, что пользователь находится в сети каждый раз, когда вносит изменения в данные. В другом разделе мы обновим приложение, чтобы пользователи могли изменять данные, даже работая в автономном режиме.

## <a name="review-core-data"></a>Обзор модели Core Data

При использовании автономного хранилища Core Data необходимо определить конкретные таблицы и поля в модели данных. Пример приложения уже содержит модель данных с подходящим форматом. В этом разделе мы рассмотрим эти таблицы и их использование.

- Откройте **QSDataModel.xcdatamodeld**. Определено четыре таблицы — три используются пакетом SDK и одна предназначена для самих элементов списка дел.
      * MS\_TableOperations: для отслеживания элементов, которые должны быть синхронизированы с сервером.
      * MS\_TableOperationErrors: для отслеживания ошибок, возникающих во время автономной синхронизации.
      * MS\_TableConfig: для отслеживания времени последнего обновления у последней операции синхронизации для всех операций Pull.
      * TodoItem: для хранения элементов списка дел. Системные столбцы **ms\_createdAt**, **ms\_updatedAt** и **ms\_version** представляют собой необязательные системные свойства.

>[AZURE.NOTE]Пакет SDK для мобильных приложений резервирует имена столбцов, начинающиеся с «**`ms_`**». Не следует использовать этот префикс где-либо, кроме системных столбцов, в противном случае при использовании удаленного внутреннего сервера имена таких столбцов будут изменены.

- При использовании функции автономной синхронизации необходимо определить системные таблицы, как показано ниже.

    ### Системные таблицы

    **MS\_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | Атрибут | Тип |
    |----------- |   ------    |
    | id | Integer 64 |
    | itemId | Строка |
    | properties | Двоичные данные |
    | таблица | Строка |
    | tableKind | Integer 16 |

    <br>**MS\_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | Атрибут | Тип |
    |----------- |   ------    |
    | id | Строка |
    | operationId | Integer 64 |
    | properties | Двоичные данные |
    | tableKind | Integer 16 |

    <br>**MS\_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | Атрибут | Тип |
    |----------- |   ------    |
    | id | Integer 64 |
    | key | Строка |
    | keyType | Integer 64 |
    | таблица | Строка |
    | значение | Строка |

    ### Таблица данных

    ![][defining-core-data-todoitem-entity]

    **TodoItem**


    | Атрибут | Тип | Примечание. |
    |-----------   |  ------ | -------------------------------------------------------|
    | id | Строка | первичный ключ в удаленном хранилище |
    | complete | Логический | поле элемента todo |
    | text | Строка | поле элемента todo |
    | ms\_createdAt | Дата | (необязательно) сопоставляется с системным свойством createdAt | | ms\_updatedAt | Дата | (необязательно) сопоставляется с системным свойством updatedAt | | ms\_version | Строка | (необязательно) используется для обнаружения конфликтов, сопоставляется с версией |


## <a name="setup-sync"></a>Изменение режима синхронизации приложения

В этом разделе вы измените приложение, чтобы оно не синхронизировалось при запуске и при вставке или изменении элементов, а синхронизировалось только при выполнении жеста обновления.

1. В **QSTodoListViewController.m** измените метод **viewDidLoad**, чтобы удалить вызов `[self refresh]` в конце метода. Теперь данные не будут синхронизироваться с сервером при запуске приложения, а будут находиться в локальном хранилище.

2. В **QSTodoService.m** измените определение `addItem`, чтобы запретить синхронизацию после вставки элемента. Удалите блок `self syncData` и замените его следующим:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Измените определение `completeItem`, как указано выше; удалите блок для `self syncData` и замените его следующим:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>Тестирование приложения


В этом разделе вы отключите функцию Wi-Fi в имитаторе, чтобы создать сценарий автономной работы. При добавлении элементов данных они будут находиться в локальном хранилище Core Data, но не будут синхронизированы с мобильным внутренним сервером.

1. Отключите функцию Wi-Fi в имитаторе iOS.

2. Добавьте несколько элементов списка дел или завершите некоторые элементы. Выполните выход из имитатора (или принудительно закройте приложения) и перезапустите его. Убедитесь, что изменения были сохранены.

3. Просмотрите содержимое удаленной таблицы TodoItem:
   - Для серверной части JavaScript перейдите на портал управления и откройте вкладку "Данные", чтобы просмотреть содержимое таблицы `TodoItem`.
   - Для серверной части .NET просмотрите содержимое таблицы либо с помощью средства SQL, например SQL Server Management Studio, или с помощью клиента REST, например Fiddler или Postman.

    Убедитесь, что новые элементы *не* были синхронизированы с сервером.

4. Включите Wi-Fi в симуляторе iOS, а затем сделайте жест обновления, потянув список элементов вниз. Появится индикатор хода выполнения и текст "Синхронизация...".

5. Снова просмотрите данные таблиц TodoItem. Должны отображаться как новые, так и измененные TodoItem.

## Сводка

Для поддержки функции автономной синхронизации мы использовали интерфейс `MSSyncTable` и инициализировали `MSClient.syncContext` в локальном хранилище. В этом случае локальным хранилищем была база данных Core Data.

При использовании локального хранилища Core Data необходимо определить несколько таблиц с помощью [правильных системных свойств](#review-core-data).

Обычные операции CRUD для мобильных приложений работают так, как если бы приложение по-прежнему было подключено, но все операции выполнялись в локальном хранилище.

Когда мы хотели синхронизировать локальное хранилище с сервером, то использовали методы `MSSyncTable.pullWithQuery` и `MSClient.syncContext.pushWithCompletion`.

*  Для отправки изменений на сервер мы вызывали метод `pushWithCompletion`. Этот метод является членом `MSSyncContext`, а не таблицы синхронизации, поскольку он будет передавать изменения во все таблицы.

    Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер.

* Для извлечения данных из таблицы на сервере в приложение мы вызывали метод `MSSyncTable.pullWithQuery`.

    Операции извлечения всегда предшествует операция передачи. Это позволяет обеспечить согласованность всех таблиц в локальном хранилище, а также связей между ними.

    Обратите внимание, что можно использовать `pullWithQuery` для фильтрации данных, хранящихся на клиенте, для этого достаточно изменить параметр `query`.

* Чтобы включить добавочную синхронизацию, передайте идентификатор запроса в `pullWithQuery`. Идентификатор запроса используется для хранения метки времени последнего обновления из результатов последней операции принудительной отправки. Идентификатор запроса должен быть описательной строкой, уникальной для каждого логического запроса в приложении. Если запрос содержит параметр, то аналогичное значение параметра должно быть частью идентификатора запроса.

    Если вы хотите явно отказаться от добавочной синхронизации, передайте `nil` в качестве идентификатора запроса. В этом случае при каждом вызове `pullWithQuery` будут извлекаться все записи, что может снижать уровень производительности.

<!-- * To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to remove records from the local database, in case they have been deleted in the remote service.
 -->

## Дополнительные ресурсы

* [Облачное покрытие: автономная синхронизация в мобильных службах Azure]

* [Azure Friday: приложения с поддержкой автономной работы в мобильных службах Azure]. (Примечание. Демонстрации предназначены для операционной системы Windows, однако обсуждение функций справедливо для всех платформ.)

<!-- URLs. -->

[Создание приложения iOS]: ../app-service-mobile-dotnet-backend-ios-get-started.md

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: ../mobile-services-ios-how-to-use-client-library.md
[Пример автономного приложения iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Пример репозитория мобильных приложений на GitHub]: https://github.com/Azure/mobile-services-samples

[Get started with Mobile Services]: ../mobile-services-ios-get-started.md
[Get started with data]: ../mobile-services-ios-get-started-data.md
[Handling conflicts with offline support for Mobile Services]: ../mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: ../mobile-services-using-soft-delete.md

[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: приложения с поддержкой автономной работы в мобильных службах Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
 

<!---HONumber=August15_HO6-->