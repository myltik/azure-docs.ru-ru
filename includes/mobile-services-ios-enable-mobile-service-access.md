
После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции.

1. Если пакет [iOS SDK для мобильных служб](https://go.microsoft.com/fwLink/p/?LinkID=266533) еще не установлен, установите его. После установки скопируйте каталог WindowsAzureMobileServices.framework и перезапишите каталог WindowsAzureMobileServices.framework, входящую в загруженный проект. Таким образом вы перейдете к использованию последней версии Azure Mobile Services SDK.

2. В файле ToDoService.h найдите следующую закомментированную строку кода:

        // TODO - create an MSClient proeprty

   	После этого комментария добавьте следующую строку кода:

        @property (nonatomic, strong)   MSClient *client;

   	При этом создается свойство, которое представляет MSClient, который подключается к службе

3. В файле TodoService.m найдите следующую закомментированную строку кода:

        // TODO - create an MSTable property for your items

   	После этого комментария добавьте следующую строку кода в объявлении @interface:

        @property (nonatomic, strong)   MSTable *table;

   	Это создает представление свойств таблицы мобильных служб.

4. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

5. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

6. Вернитесь в Xcode, откройте файл TodoService.m и найдите следующую закомментированную строку кода:

        // Initialize the Mobile Service client with your URL and key.

    После этого комментария добавьте следующую строку кода:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Будет создан экземпляр клиента мобильных служб.

7. Замените значения **APPURL** и **APPKEY** на URL-адрес и ключ приложения из мобильной службы, которые были получены на шаге 6.

8. Найдите следующую закомментированную строку кода:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    После этого комментария добавьте следующую строку кода:

        self.table = [self.client tableWithName:@"TodoItem"];

    Будет создан экземпляр таблицы TodoItem.

9. Найдите следующую закомментированную строку кода:

 	    // Create a predicate that finds items where complete is false

    После этого комментария добавьте следующую строку кода:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    При этом создается запрос для возврата всех задач, которые еще не были завершены.

10. Найдите следующую закомментированную строку кода:

        // Query the TodoItem table and update the items property with the results from the service

     Замените этот комментарий и последующий блок вызова **completion** на следующий код:

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. Найдите метод **addItem** и замените его содержимое на следующий код:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Этот код отправляет в мобильную службу запрос на вставку.

12. Найдите метод **completeItem** и найдите следующую закомментированную строку кода:

        // Update the item in the TodoItem table and remove from the items array on completion

    Замените содержимое метода с этой точки и до конца на следующий код:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	Этот код удаляет TodoItems, после того как они будут помечены как завершенные.

13. В файле TodoListController.m найдите метод **onAdd** и замените его на следующий код:

      - (IBAction)onAdd:(id)sender
      {
          if (itemText.text.length  == 0)
          {
              return;
          }

          NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
          UITableView *view = self.tableView;
          [self.todoService addItem:item completion:^(NSUInteger index)
          {
              NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
              [view insertRowsAtIndexPaths:@[ indexPath ]
                          withRowAnimation:UITableViewRowAnimationTop];
          }];

          itemText.text = @"";
      }


Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.
