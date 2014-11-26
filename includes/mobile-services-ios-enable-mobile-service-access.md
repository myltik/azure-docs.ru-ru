После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции.

1.  Если пакет [iOS SDK для мобильных служб][iOS SDK для мобильных служб] еще не установлен, установите его.

2.  В навигаторе проекта в Xcode откройте файлы TodoService.m и TodoService.h, расположенные в папке «Быстрый запуск», и добавьте следующий оператор импорта:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  В файле ToDoService.h найдите следующую закомментированную строку кода:

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    После этого комментария добавьте следующую строку кода:

        @property (nonatomic, strong)   MSClient *client;

    При этом создается свойство, которое представляет MSClient, который подключается к службе

4.  В файле TodoService.m найдите следующую закомментированную строку кода:

        // Create an MSTable property for your items. 

    После этого комментария добавьте следующую строку кода в объявлении @interface:

        @property (nonatomic, strong)   MSTable *table;

    Это создает представление свойств таблицы мобильных служб.

5.  На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

6.  Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

    ![][ ]

    Эти значения потребуются при обращении к мобильной службе из кода приложения.

7.  Вернитесь в Xcode, откройте файл TodoService.m и найдите следующую закомментированную строку кода:

        // Initialize the Mobile Service client with your URL and key.

    После этого комментария добавьте следующую строку кода:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    Будет создан экземпляр клиента мобильных служб.

8.  Замените значения **APPURL** и **APPKEY** в этом коде на URL-адрес и ключ приложения из мобильной службы, которые были получены на шаге 6.

9.  Найдите следующую закомментированную строку кода:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    После этого комментария добавьте следующую строку кода:

        self.table = [self.client getTable:@"TodoItem"];

    Будет создан экземпляр таблицы TodoItem.

10. Найдите следующую закомментированную строку кода:

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    После этого комментария добавьте следующую строку кода:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    При этом создается запрос для возврата всех задач, которые еще не были завершены.

11. Найдите следующую закомментированную строку кода:

        // Query the TodoItem table and update the items property with the results from the service.

    Замените этот комментарий и последующий блок вызова **completion** на следующий код:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. Найдите метод **addItem** и добавьте следующий код в текст метода:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Этот код отправляет в мобильную службу запрос на вставку.

13. Найдите метод **completeItem** и добавьте следующий код в текст метода:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    Этот код удаляет TodoItems, после того как они будут помечены как завершенные.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

  [iOS SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [ ]: ./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png
