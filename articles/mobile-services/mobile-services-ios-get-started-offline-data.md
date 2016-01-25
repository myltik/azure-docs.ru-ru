<properties
	pageTitle="Приступая к работе с синхронизацией автономных данных в мобильных службах (iOS) | Microsoft Azure"
	description="Узнайте, как использовать мобильные службы Azure для кэширования и синхронизации автономных данных в приложении iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="krisragh;donnam"/>

# Приступая к работе с синхронизацией автономных данных в мобильных службах

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

Автономная синхронизация позволяет вам взаимодействовать с мобильным приложением даже при отсутствии подключения к сети. В этом учебнике описано, как приложение может автоматически сохранять изменения в локальной базе данных, работающей в автономном режиме, и синхронизировать изменения всякий раз, когда она возвращается в режим подключения к сети.

Автономная синхронизация имеет несколько преимуществ:

* повышение скорости реагирования приложений путем локального кэширования данных сервера на устройстве;
* защита приложений от потери сетевой связности;
* поддержка создания и изменения данных даже при минимальных возможностях подключения или в случае их отсутствия;
* синхронизация данных на нескольких устройствах;
* обнаружение конфликтов при изменении одной записи двумя устройствами.

> [AZURE.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить [бесплатные мобильные службы, которые можно использовать и после окончания пробного периода](http://azure.microsoft.com/pricing/details/mobile-services/). Дополнительные сведения см. на странице [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 target="\_blank").

Этот учебник создан на основе [учебника по быстрому запуску мобильных служб], который необходимо изучить в первую очередь. Сначала рассмотрим код, связанный с автономной синхронизации, который уже доступен в быстром запуске.

## <a name="review-sync"></a>Обзор кода синхронизации мобильных служб

Мобильные службы Azure с автономной синхронизацией позволяет конечным пользователям взаимодействовать с локальной базой данных в случае, когда сеть недоступна. Для использования этих возможностей в приложении необходимо инициализировать контекст синхронизации `MSClient` и указать ссылку на локальное хранилище. Затем укажите ссылку на таблицу с помощью интерфейса `MSSyncTable`.

* Обратите внимание, что в **QSTodoService.m** элемент `syncTable` имеет тип `MSSyncTable`. Он используется при автономной синхронизации вместо `MSTable`. Если используется таблица синхронизации, все операции направляются в локальное хранилище и синхронизируются с удаленной службой только по явным операциям принудительной отправки и принудительного получения.

```
		@property (nonatomic, strong)   MSSyncTable *syncTable;
```

Чтобы получить ссылку на таблицу синхронизации, используйте метод `syncTableWithName`. Чтобы удалить функцию автономной синхронизации, воспользуйтесь методом `tableWithName`.

* В **QSTodoService.m** локальное хранилище инициализируется в `QSTodoService.init` перед выполнением операций с таблицей:

```
		MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
		self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

При этом создается локальное хранилище с использованием интерфейса `MSCoreDataStore`. Можно предоставить другую реализацию локального хранилища, внедрив протокол `MSSyncContextDataSource`.

Первый параметр `initWithDelegate` указывает на обработчик конфликтов. Однако так как мы передали `nil`, используется обработчик конфликтов по умолчанию, который завершается с ошибкой всякий раз, когда возникает конфликт. Сведения о реализации пользовательского обработчика конфликтов см. в [статье об обработке конфликтов с автономной поддержкой мобильных служб].

* В **QSTodoService.m** метод `syncData` сначала передает новые изменения, а затем вызывает `pullData` для получения данных из удаленной службы. В `syncData` мы сначала вызываем `pushWithCompletion` в контексте синхронизации. Этот метод является участником `MSSyncContext`, а не самой таблицы синхронизации, поэтому изменения будут переданы во все таблицы. На сервер будут отправлены только те записи, которые каким-либо образом локально изменены (с помощью операций создания, обновления или удаления). В конце `syncData` мы вызываем вспомогательный метод `pullData`.

В этом примере операция принудительной отправки не является обязательной. Если в контексте синхронизации для таблицы, осуществляющей операцию принудительной отправки, имеются ожидающие изменения, операция принудительного получения всегда предварительно выполняет операцию принудительной отправки. Однако при наличии нескольких таблиц синхронизации необходимо явно вызывать операцию принудительной отправки, чтобы обеспечить согласованность таблиц.

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }

```

* После этого в **QSTodoService.m** метод `pullData` получает новые данные, удовлетворяющие запросу. Метод `pullData` вызывает `MSSyncTable.pullWithQuery` для извлечения удаленных данных и их сохранения в локальной базе данных. Кроме того, метод `pullWithQuery` позволяет указать запрос для фильтрации извлекаемых записей. В этом примере запрос просто извлекает все записи в удаленной таблице `TodoItem`.

Второй параметр для `pullWithQuery` — это идентификатор запроса для _добавочной синхронизации_. Добавочная синхронизация извлекает только те записи, которые изменены с момента последней синхронизации, используя для этого метку времени `UpdatedAt` записи (в локальном хранилище она называется `ms_updatedAt`). Идентификатор запроса — это описательная строка, уникальная для каждого логического запроса в приложении. Чтобы явно отказаться от добавочной синхронизации, передайте `nil` в качестве идентификатора запроса. Это неэффективно, так как при каждой операции принудительного получения будут извлекаться все записи.

```
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
```


>[AZURE.NOTE]Чтобы удалить записи из локального хранилища устройства, если они удалены из базы данных мобильной службы, включите [обратимое удаление]. В противном случае приложение должно периодически вызывать `MSSyncTable.purgeWithQuery` для очистки локального хранилища.


* В **QSTodoService.m** методы `addItem` и `completeItem` вызывают `syncData` после изменения данных. В **QSTodoListViewController.m** метод `refresh` также вызывает `syncData`, чтобы при каждом обновлении и запуске в пользовательском интерфейсе отображались последние данные (`init` вызывает `refresh`).

Так как приложение вызывает `syncData` при каждом изменении данных, приложение предполагает, что вы находитесь в сети, когда вносите изменения в приложение.

## <a name="review-core-data"></a>Обзор модели Core Data

При использовании автономного хранилища Core Data необходимо определить конкретные таблицы и поля в модели данных. Пример приложения уже содержит модель данных с подходящим форматом. В этом разделе мы рассмотрим эти таблицы и их использование.

- Откройте **QSDataModel.xcdatamodeld**. Определено четыре таблицы — три используются пакетом SDK, а одна предназначена для самих элементов списка задач.

      * MS\_TableOperations: для отслеживания элементов, которые необходимо синхронизировать с сервером.
      * MS\_TableOperationErrors: для отслеживания ошибок, возникающих при автономной синхронизации.
      * MS\_TableConfig: для отслеживания времени последнего обновления с помощью последней операции синхронизации для всех операций принудительного получения.
      * TodoItem: для хранения элементов списка задач. Системные столбцы **ms\_createdAt**, **ms\_updatedAt** и **ms\_version** представляют собой необязательные системные свойства.

>[AZURE.NOTE]Пакет SDK для мобильных служб резервирует имена столбцов, начинающиеся с "**`ms_`**". Не используйте этот префикс где-либо, кроме системных столбцов. В противном случае при использовании удаленной службы имена таких столбцов будут изменены.

- При использовании функции автономной синхронизации необходимо определить системные таблицы, как показано ниже.

    ### Системные таблицы

    #### MS\_TableOperations

    | Атрибут | Тип |
    |-------------- |   ------    |
    | идентификатор (обязательно) | Integer 64 |
    | itemId | Строка |
    | properties | Двоичные данные |
    | таблица | Строка |
    | tableKind | Integer 16 |

    #### MS\_TableOperationErrors

    | Атрибут | Тип |
    |-------------- | ----------  |
    | идентификатор (обязательно) | Строковый |
    | operationId | Integer 64 |
    | properties | Двоичные данные |
    | tableKind | Integer 16 |

    #### MS\_TableConfig


    | Атрибут | Тип |
    |-------------- | ----------  |
    | идентификатор (обязательно) | Строковый |
    | key | Строка |
    | keyType | Integer 64 |
    | таблица | Строка |
    | значение | Строка |

    ### Таблица данных

    #### TodoItem

    | Атрибут | Тип | Примечание. |
    |-------------- |  ------ | -------------------------------------------------------|
    | идентификатор (обязательно) | Строковый | первичный ключ в удаленном хранилище (обязательно) |
    | complete | Логический | поле элемента todo |
    | text | Строка | поле элемента todo |
    | ms\_createdAt | Дата | (необязательно) сопоставляется с системным свойством \_\_createdAt | | ms\_updatedAt | Дата | (необязательно) сопоставляется с системным свойством \_\_updatedAt | | ms\_version | Строка | (необязательно) используется для обнаружения конфликтов, сопоставляется с \_\_version |



## <a name="setup-sync"></a>Изменение режима синхронизации приложения

В этом разделе вы измените приложение, чтобы оно не синхронизировалось при запуске и при вставке или обновлении элементов, а синхронизировалось только при выполнении жеста обновления.

* В **QSTodoListViewController.m** измените метод `viewDidLoad`, чтобы удалить вызов `[self refresh]` в конце метода. Теперь данные не будут синхронизироваться с сервером при запуске приложения, а будут храниться в локальном хранилище.

* В **QSTodoService.m** измените `addItem`, чтобы запретить синхронизацию после вставки элемента. Удалите блок `self syncData` и замените его следующим:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* Аналогично, в **QSTodoService.m** удалите блок для `self syncData` из `completeItem` и замените его следующим:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>Тестирование приложения

В этом разделе вы отключите функцию Wi-Fi в имитаторе, чтобы создать сценарий автономной работы. При добавлении элементов данных они будут находиться в локальном хранилище Core Data, но не будут синхронизированы с мобильной службой.

1. Отключите подключение к Интернету на компьютере Mac. Если выключить в симуляторе iOS только Wi-Fi, симулятор может использовать подключение хост-компьютера, поэтому отключить Интернет необходимо на самом компьютере Mac. Эта процедура имитирует сценарий автономной работы.

2. Добавьте несколько элементов списка дел или завершите некоторые элементы. Выполните выход из имитатора (или принудительно закройте приложения) и перезапустите его. Убедитесь, что изменения были сохранены. Обратите внимание, что элементы данных все еще отображаются, так как они хранятся в локальном хранилище Core Data.

3. Просмотрите содержимое удаленной таблицы TodoItem. Убедитесь, что новые элементы _не_ синхронизированы с сервером.

   - Для серверной части JavaScript перейдите на [классический портал Azure](http://manage.windowsazure.com) и откройте вкладку "Данные", чтобы просмотреть содержимое таблицы `TodoItem`.
   - Для серверной части .NET просмотрите содержимое таблицы либо с помощью средства SQL, например SQL Server Management Studio, или с помощью клиента REST, например Fiddler или Postman.

4. Включите функцию Wi-Fi в имитаторе iOS. Затем сделайте жест обновления, потянув список элементов вниз. Появится индикатор хода выполнения и текст "Синхронизация...".

5. Снова просмотрите данные таблиц TodoItem. Должны отображаться как новые, так и измененные TodoItem.

## Сводка

Для поддержки автономных функций мобильных служб вы использовали интерфейс `MSSyncTable` и инициализировали `MSClient.syncContext` в локальном хранилище. В этом случае локальным хранилищем была база данных Core Data.

При использовании локального хранилища Core Data необходимо определить несколько таблиц с помощью [правильных системных свойств][Review the Core Data model]. Обычные операции для мобильных служб работают так, как будто приложение по-прежнему подключено, но все операции выполняются в локальном хранилище.

Для синхронизации локального хранилища с сервером вы использовали методы `MSSyncTable.pullWithQuery` и `MSClient.syncContext.pushWithCompletion`:

		* To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.

		* To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## Дальнейшие действия

* [Обработка конфликтов с автономной поддержкой мобильных служб]

* [Использование обратимого удаления в мобильных службах][Soft Delete]

## Дополнительные ресурсы

* [Облачное покрытие: автономная синхронизация в мобильных службах Azure]

* [Azure Friday: приложения с поддержкой автономной работы в мобильных службах Azure]. (Примечание. Демонстрации предназначены для операционной системы Windows, однако обсуждение функций справедливо для всех платформ.)

<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Обработка конфликтов с автономной поддержкой мобильных служб]: mobile-services-ios-handling-conflicts-offline-data.md
[статье об обработке конфликтов с автономной поддержкой мобильных служб]: mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[обратимое удаление]: mobile-services-using-soft-delete.md

[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: приложения с поддержкой автономной работы в мобильных службах Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[учебника по быстрому запуску мобильных служб]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_0114_2016-->