## <a name="update-app"></a><span class="short-header">Обновление приложения </span>Обновление приложения для вызова настраиваемого API

1.  Создайте кнопку, чтобы ее можно было использовать для вызова пользовательского API. Перетащите элемент **Round Rect Button** из **библиотеки объектов**, расположенной в нижней части панели **Служебные программы**, и поместите его под или рядом с текстовым полем. Дважды щелкните, чтобы добавить текст **"Все"**

    Это приведет к добавлению новой кнопки **"Все"**.

2.  Откройте файл кода **QSTodoService.m**, найдите метод `refreshDataOnSuccess` и убедитесь в том, что он содержит следующий код:

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion
        {          
            // Create a predicate that finds items where complete is false
            NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

            // Query the TodoItem table and update the items property with the results from the service
            [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
            {
                [self logErrorIfNotNil:error];

                items = [results mutableCopy];

                // Let the caller know that we finished
                completion();
            }];                                 
        }

    Это приводит к фильтрации элементов, так что завершенные элементы не возвращаются в результатах запроса.

3.  Теперь пора подключить этот объект к исходному коду контроллера представления. **Щелкните при нажатой клавише CTRL** новую кнопку **«Все»** и перетащите ее мышью на позицию перед строкой `@end` в **QSTodoListViewController.h**. Подключите объект к новому элементу **Action** с именем `onCompleteAll` в **QSTodoListViewController**. Xcode будет автоматически вставлять следующую строку перед строкой `@end`:

           - (IBAction)onCompleteAll:(id)sender;

4.  Цель этого метода `onCompleteAll` — обработка события Click для новой кнопки. Он вызывает новый метод `completeAll`, который мы добавим в наш пользовательский класс, который, в свою очередь, отправляет запрос POST в новый пользовательский API. Результат, возвращаемый настраиваемым интерфейсом API, отображается в диалоговом окне сообщения, как и любые ошибки. Измените **QSTodoListViewController.m**, чтобы добавить следующую реализацию перед строкой `@end`:

           - (IBAction)onCompleteAll:(id)sender {
            [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
             {
                 if (error)
                 {
                     NSString* errorMessage = @"There was a problem! ";
                     errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Error!"
                                             message:errorMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 } else {
                     NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];                   
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Success!"
                                             message:successMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 }
             }];
           }

5.  Обратите внимание, что приведенный выше код ссылается на новый метод `completeAll`, который еще не был определен в **QSTodoService**. Измените **QSTodoService.h** и добавьте следующую строку перед строкой `@end`:

        - (void) completeAll:(MSAPIBlock)completion;

6.  Добавьте реализацию `completeAll` в **QSTodoService.m** перед строкой `@end`. iOS, как и JavaScript, не поддерживает сериализацию JSON произвольного типа. Таким образом, здесь имеет место довольно простой API для вызова пользовательских API, состоящий из метода `invokeAPI`.

        - (void) completeAll:(MSAPIBlock)completion
        {
            [self.client
             invokeAPI:@"completeall"
             body:nil
             HTTPMethod:@"POST"
             parameters:nil
             headers:nil
             completion:completion ];
        }

## <a name="test-app"></a> Тестирование приложения

1.  В Xcode выберите эмулятор для развертывания (iPhone или iPad) и нажмите кнопку **Запуск** (или клавиши **Command+R**), чтобы повторить сборку проекта и запустить приложение. Это приведет к выполнению клиента мобильных служб Windows Azure, созданного с использованием iOS SDK, который запрашивает элементы из вашей мобильной службы.

2.  Введите текст в текстовом поле и нажмите кнопку **+**. В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3.  Повторяйте предыдущий шаг, пока в список не будет добавлено несколько элементов.

4.  Коснитесь кнопки **Все**. Отображается окно с предупреждением, в котором указывается количество элементов, помеченных как завершенные, после чего снова выполняется фильтрованный запрос, что приводит к удалению всех элементов из списка.

    ![][0]

  [0]: ./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png
