---
title: Включение автономной синхронизации с помощью мобильных приложений iOS | Документация Майкрософт
description: Узнайте, как использовать мобильные приложений службы приложений Azure для кэширования и синхронизации автономных данных в приложениях iOS.
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 2f415f1886c654f3bdd880cdccaadc7aa3e69892
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32156501"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Включение автономной синхронизации с помощью мобильных приложений iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Обзор
В этом руководстве рассматривается функция автономной синхронизации с помощью мобильных приложений службы приложений Azure для iOS. Автономная синхронизация позволяет конечным пользователям взаимодействовать с мобильным приложением, просматривая, добавляя или изменяя данные даже при отсутствии подключения к сети. Изменения сохраняются в локальной базе данных. Когда устройство возвращается в режим подключения к сети, изменения синхронизируются с удаленной серверной частью.

Если вы впервые работаете с мобильными приложениями, то сначала ознакомьтесь с руководством [Создание приложения iOS]. Если вы не используете скачанный проект быстрого запуска сервера, то в проект необходимо добавить пакеты расширений для доступа к данным. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Дополнительные сведения о функции автономной синхронизации см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

## <a name="review-sync"></a>Просмотр кода синхронизации клиента
Клиентский проект, скачанный для изучения руководства [Создание приложения iOS], уже содержит код, который поддерживает автономную синхронизацию с использованием локальной базы данных Core Data. В этом разделе приводится краткое содержание материала, уже включенного в код руководства. Общие сведения об этой функции см. в статье [Автономная синхронизация данных в мобильных приложениях Azure].

С помощью функции автономной синхронизации данных в мобильных приложениях конечные пользователи могут взаимодействовать с локальной базой данных даже в тех случаях, когда сеть недоступна. Для использования этих возможностей в приложении необходимо инициализировать контекст синхронизации `MSClient` и указать ссылку на локальное хранилище. Затем необходимо сослаться на таблицу с помощью интерфейса **MSSyncTable**.

Обратите внимание, что в **QSTodoService.m** (Objective-C) или **ToDoTableViewController.swift** (Swift) типом элемента **syncTable** является **MSSyncTable**. Автономная синхронизация использует этот интерфейс таблицы синхронизации вместо **MSTable**. Если используется таблица синхронизации, то все операции направляются в локальное хранилище и синхронизируются с удаленной серверной частью только по явным операциям отправки и извлечения.

 Чтобы получить ссылку на таблицу синхронизации, используйте метод **syncTableWithName** для `MSClient`. Чтобы удалить функцию автономной синхронизации, воспользуйтесь методом **tableWithName**.

Прежде чем можно будет выполнить операции с таблицами, необходимо инициализировать локальное хранилище. Ниже приведен соответствующий код.

* **Objective-C**. В методе **QSTodoService.init**:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. В методе **ToDoTableViewController.viewDidLoad**:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Этот метод создает локальное хранилище с использованием интерфейса `MSCoreDataStore`, который предоставляется в пакете SDK для мобильных приложений. Также можно предоставить другое локальное хранилище, реализовав протокол `MSSyncContextDataSource`. Кроме того, первый параметр **tableWithName** используется для указания обработчика конфликтов. Так как мы передали `nil`, то получили обработчик конфликтов по умолчанию, который завершается с ошибкой всякий раз, когда возникает конфликт.

Теперь давайте выполним фактическую операцию синхронизации и получим данные из удаленной серверной части.

* **Objective-C**. Метод `syncData` сначала передает новые изменения, а затем вызывает **pullData** для получения данных из удаленной серверной части. В свою очередь, метод **pullData** получает новые данные, удовлетворяющие запросу:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
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
   ```

В версии на Objective-C в `syncData` сначала вызывается **pushWithCompletion** для контекста синхронизации. Этот метод является участником `MSSyncContext` (а не самой таблицы синхронизации), поэтому изменения передаются во все таблицы. Только те записи, которые были каким-либо образом локально изменены (с помощью операций CUD), отправляются на сервер. Затем вызывается вспомогательный метод **pullData**, который вызывает **MSSyncTable.pullWithQuery** для получения удаленных данных и их сохранения в локальной базе данных.

Так как в версии на Swift операция принудительной отправки не является обязательной, вызов **pushWithCompletion** отсутствует. Если в контексте синхронизации для таблице, осуществляющей операцию принудительной отправки, имеются ожидающие изменения, то операция принудительного получения всегда предварительно выполняет операцию принудительной отправки. Однако при наличии нескольких таблиц синхронизации рекомендуется явно вызывать операцию принудительной отправки, чтобы обеспечить согласованность для связанных таблиц.

В версиях на языках Objective-C и Swift метод **pullWithQuery** можно использовать, чтобы указать запрос для фильтрации извлекаемых записей. В этом примере запрос извлекает все записи в удаленной таблице `TodoItem`.

Второй параметр для **pullWithQuery** — это идентификатор запроса, который используется для *добавочной синхронизации*. Добавочная синхронизация извлекает только те записи, которые были изменены с момента последней синхронизации, используя для этого метку времени `UpdatedAt` записи (в локальном хранилище она называется `updatedAt`). Идентификатор запроса должен быть описательной строкой, уникальной для каждого логического запроса в приложении. Чтобы явно отказаться от добавочной синхронизации, передайте `nil` в качестве идентификатора запроса. Этот подход может привести к снижению производительности, так как при каждой операции извлечения будут извлекаться все записи.

Приложение Objective-C выполняет синхронизацию при изменении или добавлении данных, выполнении пользователем жеста обновления и при запуске.

Приложение Swift осуществляет синхронизацию при выполнении пользователем жеста обновления и при запуске.

Так как приложение выполняет синхронизацию всякий раз, когда изменяются данные (Objective-C), или при каждом запуске приложения (Objective-C и Swift), то приложение предполагает, что пользователь находится в сети. В следующем разделе будет выполнено обновление приложения, чтобы пользователи могли изменять данные, даже работая в автономном режиме.

## <a name="review-core-data"></a>Обзор модели Core Data
При использовании автономного хранилища Core Data необходимо определить конкретные таблицы и поля в модели данных. Пример приложения уже содержит модель данных с подходящим форматом. В этом разделе мы рассмотрим эти таблицы и покажем, как их использовать.

Откройте **QSDataModel.xcdatamodeld**. Определено четыре таблицы — три используются пакетом SDK, а одна предназначена для самих элементов списка дел.
  * MS_TableOperations: отслеживает элементы, которые должны быть синхронизированы с сервером.
  * MS_TableOperationErrors: отслеживает ошибки, возникающие во время автономной синхронизации.
  * MS_TableConfig: отслеживает время последнего обновления у последней операции синхронизации для всех операций извлечения.
  * TodoItem: сохраняет элементы списка дел. Системные столбцы **mcreatedAt**, **updatedAt** и **version** представляют собой необязательные системные свойства.

> [!NOTE]
> Пакет SDK для мобильных приложений резервирует имена столбцов, начинающиеся с "**``**". Не используйте этот префикс где-либо, кроме системных столбцов. В противном случае при использовании удаленной серверной части имена таких столбцов будут изменены.
>
>

При использовании функции автономной синхронизации определите три системные таблицы и таблицу данных.

### <a name="system-tables"></a>Системные таблицы

**MS_TableOperations**  

![Атрибуты таблицы MS_TableOperations][defining-core-data-tableoperations-entity]

| Атрибут | type |
| --- | --- |
| id | Integer 64 |
| itemId | Строка |
| properties | Двоичные данные |
| таблица | Строка |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![Атрибуты таблицы MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Атрибут | type |
| --- | --- |
| id |Строка |
| operationId |Integer 64 |
| properties |Двоичные данные |
| tableKind |Integer 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Атрибут | type |
| --- | --- |
| id |Строка |
| key |Строка |
| keyType |Integer 64 |
| таблица |Строка |
| value |Строка |

### <a name="data-table"></a>Таблица данных

**TodoItem**

| Атрибут | type | Примечание |
| --- | --- | --- |
| id | Строка, помеченная как обязательная |Первичный ключ в удаленном хранилище |
| complete | Логическое | Поле элемента списка дел |
| текст |Строка |Поле элемента списка дел |
| дата создания | Дата | (необязательно) Сопоставляется с системным свойством **createdAt** |
| дата обновления | Дата | (необязательно) Сопоставляется с системным свойством **updatedAt** |
| версия | Строка | (необязательно) Используется для обнаружения конфликтов, сопоставляется со свойством version |

## <a name="setup-sync"></a>Изменение режима синхронизации приложения
В этом разделе приложение изменяется так, чтобы оно не синхронизировалось при запуске или при вставке и обновлении элементов. Оно синхронизируется только при выполнении жеста обновления.

**Objective-C**:

1. В **QSTodoListViewController.m** измените метод **viewDidLoad**, чтобы удалить вызов `[self refresh]` в конце метода. Теперь данные не синхронизируются с сервером при запуске приложения. Вместо этого они синхронизируются с содержимым локального хранилища.
2. В **QSTodoService.m** измените определение `addItem`, чтобы запретить синхронизацию после вставки элемента. Удалите блок `self syncData` и замените его следующим:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Измените определение `completeItem`, как упоминалось ранее. Удалите блок для `self syncData` и замените его следующим:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

В методе `viewDidLoad` в **ToDoTableViewController.swift** закомментируйте следующие две строки, чтобы остановить синхронизацию при запуске приложения. На момент написания этой статьи приложение Todo на языке Swift не обновляет службу при добавлении или вводе элемента пользователем. Обновление выполняется только при запуске приложения.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Тестирование приложения
В этом разделе описывается подключение к недействительному URL-адресу для имитации сценария автономной работы. При добавлении элементов данных они хранятся в локальном хранилище Core Data, но не синхронизируются с серверной частью мобильного приложения.

1. Измените URL-адрес мобильного приложения в файле **QSTodoService.m** на недействительный и снова запустите приложение:

   **Objective-C**. В файле QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. В файле ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Добавьте несколько элементов списка дел. Выполните выход из симулятора (или принудительно закройте приложение) и перезапустите его. Убедитесь, что изменения сохранились.

3. Просмотрите содержимое удаленной таблицы **TodoItem**:
   * Для серверной части Node.js перейдите на [портал Azure](https://portal.azure.com/), затем на странице серверной части мобильного приложения щелкните **Простые таблицы** > **TodoItem**.  
   * Для серверной части .NET используйте средства SQL, например SQL Server Management Studio, или клиент REST, например Fiddler или Postman.  

4. Убедитесь, что новые элементы *не* были синхронизированы с сервером.

5. Исправьте URL-адрес в файле **QSTodoService.m** и снова запустите приложение.

6. Сделайте жест обновления, потянув список элементов вниз.  
Отобразится счетчик хода выполнения.

7. Снова просмотрите данные таблицы **TodoItem**. Должны отображаться как новые, так и измененные элементы списка дел.

## <a name="summary"></a>Сводка
Для поддержки функции автономной синхронизации мы использовали интерфейс `MSSyncTable` и инициализировали `MSClient.syncContext` в локальном хранилище. В этом случае локальным хранилищем была база данных Core Data.

При использовании локального хранилища Core Data необходимо определить несколько таблиц с помощью [правильных системных свойств](#review-core-data).

Обычные операции создания, чтения, обновления и удаления (CRUD) для мобильных приложений работают так, как если бы приложение по-прежнему было подключено, но все операции выполнялись в локальном хранилище.

Когда мы синхронизировали локальное хранилище с сервером, то использовали метод **MSSyncTable.pullWithQuery**.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Автономная синхронизация данных в мобильных приложениях Azure]
* [Cloud Cover: Offline Sync in Azure Mobile Services] (Облачное покрытие: автономная синхронизация в мобильных службах Azure). \(Это видео рассказывает о мобильных службах, однако точно так же автономная синхронизация работает в мобильных приложениях.\)

<!-- URLs. -->


[Создание приложения iOS]: app-service-mobile-ios-get-started.md
[Автономная синхронизация данных в мобильных приложениях Azure]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
