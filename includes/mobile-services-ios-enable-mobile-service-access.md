
Давайте обновим приложение, чтобы элементы сохранялись в мобильных службах Azure, а не в локальной коллекции в памяти.

* В файле **TodoService.h** найдите следующую строку:

```
// TODO - create an MSClient property
```

Замените этот комментарий на следующую строку. При этом создается свойство, которое представляет из себя `MSClient`, подключаемый к службе.

```
@property (nonatomic, strong)   MSClient *client;
```


* В файле **TodoService.m** найдите следующую строку:

```
// TODO - create an MSTable property for your items
```

Замените этот комментарий на следующую строку в объявлении `@interface`. Это создает представление свойств таблицы мобильных служб.

```
@property (nonatomic, strong)   MSTable *table;
```


* На портале управления щелкните **Мобильные службы**, а затем выберите мобильную службу. Откройте вкладку **Панель мониторинга** и запишите значение параметра **URL-адрес сайта**. Затем щелкните **Управление ключами** и запишите значение параметра **Ключ приложения**. Эти значения потребуются при обращении к мобильной службе из кода приложения.


* В файле **TodoService.m** найдите следующую строку:

```
// Initialize the Mobile Service client with your URL and key.
```

После этого комментария добавьте следующую строку кода: Замените `APPURL` и `APPKEY` на URL-адрес сайта и ключ приложения, полученные в предыдущем шаге.

```
self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];
```


* В файле **TodoService.m** найдите следующую строку:

```
// Create an MSTable instance to allow us to work with the TodoItem table.
```

Замените этот комментарий на следующую строку. Будет создан экземпляр таблицы TodoItem.

```
self.table = [self.client tableWithName:@"TodoItem"];
```


* В файле **TodoService.m** найдите следующую строку:

```
// Create a predicate that finds items where complete is false
```

Замените этот комментарий на следующую строку. При этом создается запрос для возврата всех задач, которые еще не были завершены.

```
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
```


* Найдите следующую строку:

```
// Query the TodoItem table and update the items property with the results from the service
```

Замените этот комментарий и последующий блок вызова **completion** на следующий код:

```
[self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
{
self.items = [results mutableCopy];
   completion();
}];
```


* Найдите метод **addItem** и замените его содержимое на следующий код. Этот код отправляет в мобильную службу запрос на вставку.

```
// Insert the item into the TodoItem table and add to the items array on completion
[self.table insert:item completion:^(NSDictionary *result, NSError *error) {
    NSUInteger index = [items count];
    [(NSMutableArray *)items insertObject:item atIndex:index];

    // Let the caller know that we finished
    completion(index);
}];
```


* В методе **completeItem** найдите следующую закомментированную строку кода:

```
// Update the item in the TodoItem table and remove from the items array on completion
```

Замените содержимое метода с этой точки и до конца на следующий код. Этот код удаляет элементы TodoItem после того, как они будут помечены как завершенные.

```
// Update the item in the TodoItem table and remove from the items array on completion
[self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

    // Get a fresh index in case the list has changed
    NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

    [mutableItems removeObjectAtIndex:index];

    // Let the caller know that we have finished
    completion(index);
}];
```


* В файле TodoListController.m найдите метод **onAdd** и замените его на следующий код:

```
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
```

<!---HONumber=August15_HO6-->