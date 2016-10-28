<properties
	pageTitle="Включение автономной синхронизации для мобильного приложения Azure (iOS)"
	description="Использование мобильных приложений службы приложений для кэширования и синхронизации автономных данных в приложении iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="krisragh"/>

# Включение автономной синхронизации для мобильного приложения iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Обзор

В этом учебнике рассматривается функция автономной синхронизации мобильных приложений Azure для iOS. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением (просматривать, добавлять или изменять данные) даже при отсутствии подключения к сети. Изменения хранятся в локальной базе данных; после подключения устройства к сети эти изменения синхронизируются с удаленным внутренним сервером.

Если вы впервые работаете с мобильными приложениями Azure, сначала пройдите учебник [Создание приложения iOS]. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений доступа к данным. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## <a name="review-sync"></a>Просмотр кода синхронизации клиента

Клиентский проект, загруженный для изучения учебника [Создание приложения iOS], уже содержит код, который поддерживает автономную синхронизацию с использованием локальной базы данных Core Data. В этом разделе приводится краткое содержание материала, уже включенного в код учебника. Обзор этой функции см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

Функция автономной синхронизации данных в мобильных приложениях Azure позволяет конечным пользователям взаимодействовать с локальной базой данных в случае, когда сеть недоступна. Для использования этих возможностей в приложении необходимо инициализировать контекст синхронизации `MSClient` и указать ссылку на локальное хранилище. Затем укажите ссылку на таблицу с помощью интерфейса `MSSyncTable`.

1. В **QSTodoService.m** (Objective-C) или **ToDoTableViewController.swift** (Swift) обратите внимание, что типом участника `syncTable` является `MSSyncTable`. Автономная синхронизация использует этот интерфейс таблицы синхронизации вместо `MSTable`. При использовании таблицы синхронизации все операции направляются в локальное хранилище и синхронизируются только с удаленным внутренним сервером с явными push-уведомлениями и операциями получения.

    Чтобы получить ссылку на таблицу синхронизации, используйте метод `syncTableWithName` для `MSClient`. Чтобы удалить функцию автономной синхронизации, воспользуйтесь методом `tableWithName`.

2. Прежде чем можно будет выполнить операции с таблицами, необходимо инициализировать локальное хранилище. Ниже приведен соответствующий код.
	
	**Objective-C**:
	
	В методе `QSTodoService.init`:
	
	
	        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
	        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
	
	
	**Swift**:
	
	В методе `ToDoTableViewController.viewDidLoad`:
	
	
	        let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
	        let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
	        self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
	        client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
	

	Он создает локальное хранилище с использованием интерфейса `MSCoreDataStore`, который предоставляется в пакете SDK для мобильных приложений. Можно также предоставить другое локальное хранилище, реализовав протокол `MSSyncContextDataSource`.
	
	Кроме того, первый параметр `MSSyncContext` используется для указания обработчика конфликтов. Поскольку мы передали `nil`, то получим обработчик конфликтов по умолчанию, который завершается с ошибкой всякий раз, когда возникает конфликт.
	
3. Теперь давайте выполним фактическую операцию синхронизации и получим данные из удаленной серверной части.

	**Objective-C**:
	
	Метод `syncData` сначала передает новые изменения, а затем вызывает `pullData` для получения данных из удаленной серверной части. В свою очередь, метод `pullData` получает новые данные, удовлетворяющие запросу:
	
	
	        -(void)syncData:(QSCompletionBlock)completion
	        {
	            // push all changes in the sync context, then pull new data
	            [self.client.syncContext pushWithCompletion:^(NSError *error) {
	                [self logErrorIfNotNil:error];
	                [self pullData:completion];
	            }];
	        }
	
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
        
        
      **Swift**:
        
        
		func onRefresh(sender: UIRefreshControl!) {
		    UIApplication.sharedApplication().networkActivityIndicatorVisible = true
		    
		    self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
		        (error) -> Void in
		        
		        UIApplication.sharedApplication().networkActivityIndicatorVisible = false
		        
		        if error != nil {
		            // A real application would handle various errors like network conditions,
		            // server conflicts, etc via the MSSyncContextDelegate
		            print("Error: (error!.description)")
		            
		            // We will just discard our changes and keep the servers copy for simplicity
		            if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
		                for opError in opErrors {
		                    print("Attempted operation to item (opError.itemId)")
		                    if (opError.operation == .Insert || opError.operation == .Delete) {
		                        print("Insert/Delete, failed discarding changes")
		                        opError.cancelOperationAndDiscardItemWithCompletion(nil)
		                    } else {
		                        print("Update failed, reverting to server's copy")
		                        opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
		                    }
		                }
		            }
		        }
		        self.refreshControl?.endRefreshing()
		    }
		} 
	
	
	В версии на Objective-C в `syncData` сначала вызывается `pushWithCompletion` для контекста синхронизации. Этот метод является членом `MSSyncContext` (а не самой таблицы синхронизации), поэтому изменения будут переданы во все таблицы. Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), будут отправлены на сервер. Затем вызывается вспомогательный метод `pullData`, который вызывает метод `MSSyncTable.pullWithQuery` для извлечения удаленных данных и их сохранения в локальной базе данных.
	
	В версии на Swift вызов `pushWithCompletion` отсутствует. Причина этого в том, что операция принудительной отправки не является обязательной. Если в контексте синхронизации для таблице, осуществляющей операцию принудительной отправки, имеются ожидающие изменения, то операция принудительного получения всегда предварительно выполняет операцию принудительной отправки. Однако при наличии нескольких таблиц синхронизации, рекомендуется явно вызывать операцию принудительной отправки, чтобы обеспечить согласованность для связанных таблиц.
	
	В версиях на языках Objective-C и Swift метод `pullWithQuery` позволяет указать запрос для фильтрации извлекаемых записей. В этом примере запрос просто извлекает все записи в удаленной таблице `TodoItem`.
	
	Второй параметр для `pullWithQuery` — это идентификатор запроса, который используется для *добавочной синхронизации*. Добавочная синхронизация извлекает только те записи, которые были изменены с момента последней синхронизации, используя для этого метку времени `UpdatedAt` записи (в локальном хранилище она называется `updatedAt`). Идентификатор запроса должен быть описательной строкой, уникальной для каждого логического запроса в приложении. Чтобы явно отказаться от добавочной синхронизации, передайте `nil` в качестве идентификатора запроса. Помните, что это может привести к снижению производительности, поскольку при каждой операции принудительного получения будут извлекаться все записи.

5. Приложение Objective-C выполняет синхронизацию при изменении или добавлении данных, выполнении пользователем жеста обновления и при запуске. Приложение Swift осуществляет синхронизацию при выполнении пользователем жеста обновления и при запуске.

Так как приложение выполняет синхронизацию всякий раз, когда изменяются данные (Objective-C), или при каждом запуске приложения (Objective-C и Swift), то приложение предполагает, что пользователь находится в сети. В другом разделе мы обновим приложение, чтобы пользователи могли изменять данные, даже работая в автономном режиме.

## <a name="review-core-data"></a>Обзор модели Core Data

При использовании автономного хранилища Core Data необходимо определить конкретные таблицы и поля в модели данных. Пример приложения уже содержит модель данных с подходящим форматом. В этом разделе мы рассмотрим эти таблицы и их использование.

- Откройте **QSDataModel.xcdatamodeld**. Определено четыре таблицы — три используются пакетом SDK и одна предназначена для самих элементов списка дел.
      * MS\_TableOperations: для отслеживания элементов, которые должны быть синхронизированы с сервером.
      * MS\_TableOperationErrors: для отслеживания ошибок, возникающих во время автономной синхронизации.
      * MS\_TableConfig: для отслеживания времени последнего обновления у последней операции синхронизации для всех операций Pull.
      * TodoItem: для хранения элементов списка дел. Системные столбцы **mcreatedAt**, **updatedAt** и **version** представляют собой необязательные системные свойства.

>[AZURE.NOTE] Пакет SDK для мобильных приложений Azure резервирует имена столбцов, начинающиеся с **``**. Не следует использовать этот префикс где-либо, кроме системных столбцов, в противном случае при использовании удаленного внутреннего сервера имена таких столбцов будут изменены.

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

    .![][defining-core-data-tableoperationerrors-entity]

    | Атрибут | Тип |
    |----------- |   ------    |
    | id | Строка |
    | operationId | Integer 64 |
    | properties | Двоичные данные |
    | tableKind | Integer 16 |

    <br>**MS\_TableConfig**

    .![][defining-core-data-tableconfig-entity]

    | Атрибут | Тип |
    |----------- |   ------    |
    | id | Строка |
    | key | Строковый |
    | keyType | Integer 64 |
    | таблица | Строковый |
    | value | Строка |

    ### Таблица данных

    **TodoItem**

    | Атрибут | Тип | Примечание |
    |-----------   |  ------ | -------------------------------------------------------|
    | id | Строка, помеченная как обязательная | первичный ключ в удаленном хранилище |
    | complete | Логический | поле элемента todo |
    | text | Строка | поле элемента todo |
    | дата создания | Дата | (необязательно) сопоставляется с системным свойством createdAt |
    | дата обновления | Дата | (необязательно) сопоставляется с системным свойством updatedAt |
    | версия | Строка | (необязательно) используется для обнаружения конфликтов, сопоставляется со свойством version |


## <a name="setup-sync"></a>Изменение режима синхронизации приложения

В этом разделе вы измените приложение, чтобы оно не синхронизировалось при запуске и при вставке или изменении элементов, а синхронизировалось только при выполнении жеста обновления.

**Objective-C**:

1. В **QSTodoListViewController.m** измените метод **viewDidLoad**, чтобы удалить вызов `[self refresh]` в конце метода. Теперь данные не будут синхронизироваться с сервером при запуске приложения, а будут находиться в локальном хранилище.

2. В **QSTodoService.m** измените определение `addItem`, чтобы запретить синхронизацию после вставки элемента. Удалите блок `self syncData` и замените его следующим:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Измените определение `completeItem`, как указано выше; удалите блок для `self syncData` и замените его следующим:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. В `viewDidLoad` в **ToDoTableViewController.swift** закомментируйте следующие две строки, чтобы остановить синхронизацию при запуске приложения. На момент написания этой статьи приложение Todo на языке Swift не обновляло службу при добавлении или вводе элемента пользователем, а выполняло это только при запуске приложения.

		self.refreshControl?.beginRefreshing()
		self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Тестирование приложения

В этом разделе описывается подключение к недействительному URL-адресу для имитации сценария автономной работы. При добавлении элементов данных они будут находиться в локальном хранилище Core Data, но не будут синхронизированы с мобильным внутренним сервером.

1. Измените URL-адрес мобильного приложения в файле **QSTodoService.m** на недействительный и снова запустите приложение:

	**Objective-C** в QSTodoService.m.
	
        	self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
	
	**Swift** в ToDoTableViewController.swift.

		let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Добавьте несколько элементов списка дел. Выполните выход из имитатора (или принудительно закройте приложения) и перезапустите его. Убедитесь, что изменения были сохранены.

3. Просмотрите содержимое удаленной таблицы TodoItem:

    + Для серверной части Node.js перейдите на [портал Azure](https://portal.azure.com/), затем на странице серверной части своего мобильного приложения щелкните **Easy Tables** > **TodoItem**, чтобы просмотреть содержимое таблицы `TodoItem`.
   	+ Для серверной части .NET просмотрите содержимое таблицы с помощью средства SQL, например SQL Server Management Studio, или с помощью клиента REST, например Fiddler или Postman.

    Убедитесь, что новые элементы *не* были синхронизированы с сервером.

4. Исправьте URL-адрес в файле **QSTodoService.m** и снова запустите приложение. Сделайте жест обновления, потянув список элементов вниз. Появится счетчик хода выполнения.

5. Снова просмотрите данные таблиц TodoItem. Должны отображаться как новые, так и измененные TodoItem.

## Сводка

Для поддержки функции автономной синхронизации мы использовали интерфейс `MSSyncTable` и инициализировали `MSClient.syncContext` в локальном хранилище. В этом случае локальным хранилищем была база данных Core Data.

При использовании локального хранилища Core Data необходимо определить несколько таблиц с помощью [правильных системных свойств](#review-core-data).

Обычные операции CRUD для мобильных приложений Azure работают так, как если бы приложение по-прежнему было подключено, но все операции выполнялись в локальном хранилище.

Когда мы хотели синхронизировать локальное хранилище с сервером, то использовали метод `MSSyncTable.pullWithQuery`.


## дополнительные ресурсы.

* [Автономная синхронизация данных в мобильных приложениях Azure]

* [Облачное покрытие: автономная синхронизация в мобильных службах Azure] \(примечание: видео рассказывает о мобильных службах, однако точно так же автономная синхронизация работает в мобильных приложениях Azure)

<!-- URLs. -->


[Создание приложения iOS]: ../app-service-mobile-ios-get-started.md
[Автономная синхронизация данных в мобильных приложениях Azure]: ../app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Облачное покрытие: автономная синхронизация в мобильных службах Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0810_2016-->
