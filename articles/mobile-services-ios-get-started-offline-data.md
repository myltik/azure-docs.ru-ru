<properties urlDisplayName="Using Offline Data" pageTitle="Использование синхронизации с автономными данными в мобильных службах (iOS) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как использовать мобильные службы Azure для кэширования и синхронизации автономных данных в приложении iOS" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Приступая к работе с синхронизацией автономных данных в мобильных службах


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


В этом учебнике рассматривается функция автономной синхронизации мобильных служб в iOS, позволяющая разработчикам создавать приложения, которые могут использоваться даже в случае, если у конечного пользователя нет доступа к сети.

Автономная синхронизация может применяться в следующих случаях:

* Повышение скорости реагирования приложений путем локального кэширования данных сервера в устройстве
* Защита приложений от потери сетевой связности
* Предоставление конечным пользователям возможности создания и изменения данных даже в том случае, когда отсутствует сетевой доступ, поддерживая сценарии с минимальной связностью или при отсутствии связности
* Синхронизация данных между несколькими устройствами и обнаружение конфликтов, когда два устройства изменяют одну и ту же запись

В этом учебнике показано, как обновить приложение из учебника [Приступая к работе с мобильными службами] для поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

>[WACOM.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>.

Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Windows Azure для хранения и извлечения данных из приложения Магазина Windows. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником [Приступая к работе с мобильными службами].

>[WACOM.NOTE] Можно пропустить эти разделы и перейти к загрузке версии проекта "Приступая к работе", который уже содержит поддержку автономного режима и все описанное в этом разделе.  Чтобы загрузить проект с поддержкой автономного режима, см. [Приступая к работе с автономными функциями, пример для iOS].


В этом учебнике рассматриваются следующие основные действия:

1. [Получение образца приложения быстрого запуска]
2. [Загрузка Preview SDK и обновление платформы]
3. [Настройка основных данных]
4. [Определение модели основных данных]
5. [Инициализация и использование таблицы синхронизации и контекста синхронизации]
6. [Тестирование приложения]

## <a name="get-app"></a>Получение образца приложения быстрого запуска

Следуйте инструкциям в разделе [Приступая к работе с мобильными службами] и загрузите проект быстрого запуска.

## <a name="update-app"></a>Загрузка Preview SDK и обновление платформы

1. Чтобы добавить поддержку автономного режима в приложение, давайте установим версию пакета iOS SDK для мобильных служб, которая обеспечивает автономную синхронизацию. Поскольку мы запускаем эту функцию для предварительного ознакомления, она еще не содержится в официальном предоставляемом пакете SDK. [Загрузите ознакомительную версию пакета SDK здесь].

2. Затем удалите существующую ссылку **WindowsAzureMobileServices.framework** из проекта в Xcode, выбрав ее, а затем указав в меню **Правка** команду "Переместить в корзину", чтобы действительно удалить файлы.

      ![][update-framework-1]

3. Распакуйте содержимое нового предварительного пакета SDK и перетащите на место старого SDK новый пакет **WindowsAzureMobileServices.framework** SDK. Убедитесь, что выбран параметр "Копировать элементы в папку конечной группы (при необходимости)".

      ![][update-framework-2]


## <a name="setup-core-data"></a>Настройка основных данных

1. Пакет iOS SDK для мобильных служб позволяет использовать любое постоянное хранилище при условии, что оно совместимо с протоколом **MSSyncContextDataSource**. В SDK включен источник данных, реализующий этот протокол на основе [основных данных].

2. Поскольку приложение использует основные данные, перейдите к пунктам **Платформы** --> **Этапы построения** и в разделе **Связать двоичные файлы с библиотеками** добавьте **CoreData.framework**.

      ![][core-data-1]

      ![][core-data-2]

3. Мы добавляем основные данные в существующий проект в Xcode, который еще не поддерживает основные данные. Таким образом, необходимо добавить стереотипный стандартный код в различные части проекта. Сначала добавьте следующий код в **QSAppDelegate.h**:

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. Затем замените содержимое **QSAppDelegate.m** следующим кодом. Это почти тот же код, который получается при создании нового приложения в Xcode с установленным флажком "Использовать основные данные", но при инициализации **_managedObjectContext** используется частный тип параллелизма очереди. После этого изменения все почти готово для использования основных данных, но мы еще ничего с ними не делаем.

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>Определение модели основных данных

1. Продолжим настройку приложения с основными данными и определим модель данных. Мы не начнем работу с использованием только этой модели данных. Во-первых, давайте определим модель или схему основных данных. Для начала щелкните **Файл** -> **Новый файл** и выберите **Модель данных** в разделе **Основные данные**. При появлении запроса на ввод имени файла укажите **QSTodoDataModel.xcdatamodeld**.

      ![][defining-core-data-main-screen]

2. Далее мы определим необходимые фактические сущности (таблицы). Мы создадим три таблицы (сущности) с помощью редактора моделей основных данных. Для получения дополнительных сведений см. [справку редактора моделей основных данных].

  * TodoItem: для хранения самих элементов
  * MS_TableOperations: для отслеживания элементов, которые должны быть синхронизированы с сервером (необходимо для работы автономного режима)
  * MS_TableOperationErrors: для отслеживания возможных ошибок во время автономной синхронизации (необходимо для работы автономного режима)

      ![][defining-core-data-model-editor]

3. Определите три сущности, как показано ниже. Сохраните модель и постройте проект, чтобы убедиться, что все в порядке. Мы завершили настройку приложения для работы с основными данными, но приложение их еще не использует.

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | Атрибут  |  Тип   |
    |----------- |  ------ |
    | id         | Строка  |
    | complete   | Логический |
    | text       | Строка  |
    | ms_version | Строка  |

    **MS_TableOperations**

    | Атрибут  |    Тип     |
    |----------- |   ------    |
    | id         | 64-разрядный целочисленный  |
    | properties | Двоичные данные |
    | itemId     | Строка      |
    | table      | Строка      |

    **MS_TableOperationErrors**

    | Атрибут  |    Тип     |
    |----------- |   ------    |
    | id         | Строка      |
    | properties | Двоичные данные |

## <a name="setup-sync"></a> Инициализация и использование таблицы синхронизации и контекста синхронизации

1. Чтобы запустить кэширование данных в автономном режиме, заменим использование **MSTable** на **MSSyncTable** для доступа к мобильной службе. В отличие от обычной **MSTable** таблица синхронизации подобна локальной таблице, в которую добавлена возможность передачи локальных изменений в удаленную таблицу и извлечения этих изменений в локальную. В файле **QSTodoService.h** удалите определение свойства **table**:

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. В **QSTodoService.m** удалите следующие две строки из **init**:

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. Затем снова в **QSTodoService.m** давайте инициализируем контекст синхронизации в **MSClient** с помощью показанной выше реализации хранилища данных на базе основных данных. Контекст отвечает за отслеживание локально измененных элементов и за их отправку на сервер при запуске операции принудительной отправки. Для инициализации контекста требуется источник данных (реализация протокола в **MSCoreDataStore**) и необязательная реализация **MSSyncContextDelegate**. Вставьте следующие строки прямо над двумя строками, вставленными выше.

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. Теперь обновим операции в **QSTodoService.m** для использования таблицы синхронизации вместо обычной таблицы. Сначала замените **refreshDataOnSuccess** следующей реализацией. Она извлекает данные из службы, поэтому обновим ее для использования таблицы синхронизации, настроим эту таблицу для извлечения только элементов, соответствующих нашим критериям, и начнем загрузку данных из локальной таблицы синхронизации в свойство **items** службы. С помощью этого кода   **refreshDataOnSuccess** извлекает данные из удаленной таблицы в локальную (таблицу синхронизации). Обычно следует извлекать только часть таблицы, чтобы не перегрузить клиент информацией, которая может и не понадобиться.

    Для этой и остальных описанных ниже операций мы заключаем вызовы в блоки завершения в вызове **dispatch_async** основного потока. При инициализации контекста синхронизации мы не передаем параметр обратного вызова, поэтому платформа создает последовательную очередь по умолчанию, которая отправляет результаты всех операций syncTable в фоновый поток. При изменении компонентов пользовательского интерфейса требуется отправить код обратно в поток пользовательского интерфейса.

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. Затем замените **addItem** в **QSTodoService.m** следующим образом. С этим изменением операция ставится в очередь, чтобы вы передали изменения в удаленную службу и сделали ее видимой для всех:

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. Обновите **completeItem** в **QSTodoService.m** следующим образом. В отличие от **MSTable** блок завершения операции **обновления** для **MSSyncTable** не содержит обновленного элемента. В случае **MSTable** сервер изменяет обновляемый элемент и это изменение отражается на клиенте. При **MSSyncTable** обновленные элементы не изменяются и блок завершения не содержит параметра.

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. Добавьте следующее объявление операции **syncData** в **QSTodoService.h**:

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. Вернувшись в **QSTodoListViewController.m**, замените вызов **[самообновления]** в конце операции **viewDidLoad** следующим кодом:

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. Теперь протестируем приложение в автономном режиме. Добавьте в приложение несколько элементов, затем посетите портал управления Azure и взгляните на вкладку **Данные** вашего приложения. Вы увидите, что элементы еще не добавлены.

12. Далее выполните жест обновления в приложении, перетащив его сверху вниз. Затем вернитесь к порталу управления Azure и еще раз посмотрите на вкладку **Данные**. Вы увидите, что теперь данные сохранены в облаке. Также можно закрыть приложение после добавления элемента (или после его редактирования, если в приложении включена функция редактирования элементов). Когда приложение будет запущено повторно, оно синхронизируется с сервером и сохранит изменения.

13. Когда клиент выполняет некоторые изменения в элементах локально, эти изменения сохраняются в контексте синхронизации для отправки на сервер. Операция *передачи* отправляет отслеживаемые изменения на удаленный сервер, однако здесь у нас нет вызовов передачи на сервер. Тем не менее *перед выполнением извлечения любые ожидающие операции обычно передаются на сервер*. Таким образом, передача все равно происходит автоматически для предотвращения конфликтов. Вот почему в этом приложении нет явных вызовов *принудительной отправки*.

## <a name="test-app"></a>Тестирование приложения

Наконец, протестируем приложение в автономном режиме. Добавьте в приложение несколько элементов. Затем перейдите на портал и просмотрите данные (или воспользуйтесь сетевым инструментом, например PostMan или Fiddler, для прямого запроса к таблице).

Вы увидите, что элементы еще не добавлены в службу. Теперь выполните жест обновления в приложении, перетащив его сверху вниз. Вы увидите, что теперь данные сохранены в облаке. Можно даже закрыть приложение после добавления нескольких элементов. При повторном запуске приложения оно синхронизируется с сервером и изменения сохраняются.

## Дальнейшие действия

* [Обработка конфликтов с автономной поддержкой мобильных служб]

<!-- URLs. -->

[Получение образца приложения быстрого запуска]: #get-app
[Загрузка Preview SDK и обновление платформы]: #update-app
[Настройка основных данных]: #setup-core-data
[Определение модели основных данных]: #defining-core-data
[Инициализация и использование таблицы синхронизации и контекста синхронизации]: #setup-sync
[Тестирование приложения]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[Справка редактора моделей основных данных]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Создание выходного подключения]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Построение пользовательского интерфейса]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Добавление перехода между сценами в раскадровке]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Добавление сцены в раскадровку]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Основные данные]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Загрузите ознакомительную версию пакета SDK здесь]: http://aka.ms/Gc6fex
[ Использование клиентской библиотеки мобильных служб для iOS]: /ru-ru/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Приступая к работе с автономными функциями, пример для iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/
[Обработка конфликтов с автономной поддержкой мобильных служб]: /ru-ru/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
