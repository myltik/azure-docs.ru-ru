<properties 
	pageTitle="Обработка конфликтов с автономными данными в мобильных службах (iOS) | Центр мобильных разработок" 
	description="Узнайте об использовании возможностей мобильных служб Azure для обработки конфликтов при синхронизации автономных данных в приложении iOS" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/26/2015" 
	ms.author="krisragh,donnam"/>


# Обработка конфликтов синхронизации автономных данных в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-offline-conflicts](../includes/mobile-services-selector-offline-conflicts.md)]

В этом разделе показывается, как синхронизировать данные и обрабатывать конфликты при использовании возможностей автономной работы мобильных служб Azure. Данный учебник построен на сведениях и примере приложения из предыдущего учебника - [Приступая к работе с автономными данными]. Таким образом, перед началом работы с этим учебником необходимо выполнить задания учебника [Приступая к работе с автономными данными].

>[AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a>.

В этом учебнике рассматриваются следующие основные действия:

1. [Обновление проекта приложения в целях разрешения редактирования 
]
2. [Обновление контроллера представлений списка дел]
3. [Добавление контроллера представлений элементов списка дел]
4. [Добавление контроллера представлений элемента списка дел и перехода в раскадровку]
5. [Добавление сведений об элементах в контроллер представлений элемента списка дел]
6. [Добавление поддержки сохранения изменений]
7. [Проблема обработки конфликтов]
8. [Изменение QSTodoService для поддержки обработки конфликтов]
9. [Добавление вспомогательного представления предупреждений пользовательского интерфейса для поддержки обработки конфликтов]
10. [Добавление обработчика конфликтов в контроллер представлений списка дел]
11. [Тестирование приложения]

## Выполните задания учебника "Приступая к работе с автономными данными"

Следуя инструкциям учебника [Приступая к работе с автономными данными], завершите проект. Готовый проект из указанного учебника будет использоваться как основа данного курса.

## <a name="update-app"></a>Обновление проекта приложения в целях разрешения редактирования 


Изменим готовый проект из учебника [Приступая к работе с автономными данными], чтобы разрешить редактирование. В данный момент, если запустить одно и то же приложение на двух телефонах, локально изменить один и тот же элемент на обоих из них и передать изменения на сервер, возникнет конфликт, ведущий к сбою.

Функция синхронизации автономных данных в пакете SDK позволяет обрабатывать подобные конфликты с помощью кода и динамически принимать решения в отношении конфликтующих элементов. Изменение проекта быстрого запуска позволяет поэкспериментировать с этой функцией.

### <a name="update-list-view"></a>Обновление контроллера представлений списка дел

1. В навигаторе проекта выберите элемент **MainStoryboard_iPhone.storyboard**, а затем - **Контроллер представлений списка задач**. Выберите ячейку в табличном представлении и установите значение **Индикатор передачи сведений** для режима "Периферия". Этот индикатор указывает пользователям, что если коснуться контроллера табличного представления, откроется новое представление. Индикатор не создает события.

      ![][update-todo-list-view-controller-2]

2. В **TodoListViewController.m** удалите указанные ниже операции и их содержимое. Они не понадобятся.

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### <a name="add-view-controller"></a>Добавление контроллера представлений элементов списка дел

1. Создайте новый класс Cocoa Touch с именем **QSItemViewController**, производный от **UIViewController**.

2. В **QSItemViewController.h** добавьте следующее определение типа:

        typedef void (^ItemEditCompletionBlock) (NSDictionary *editedItem);

3. В **QSItemViewController.h** добавьте свойство для хранения редактируемого элемента и свойства для обратного вызова, который выполняется, когда пользователь нажимает кнопку "Назад" в подробном представлении:

        @property (nonatomic, weak) NSMutableDictionary *item;
        @property (nonatomic, strong) ItemEditCompletionBlock editCompleteBlock;

4. В файле **QSItemViewController.m** добавьте два частных свойства в два поля изменяемого элемента списка дел - состояние выполнения и текст самого элемента:

        @interface QSItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

5. В файле **QSItemViewController.m** замените реализацию заглушки **viewDidLoad** следующим кодом:

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

6. В файле **QSItemViewController.m** добавьте четыре метода для обработки изменяемых событий управления:

        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

7. В файле **QSItemViewController** также добавьте следующий метод, который вызывается, когда пользователь нажимает кнопку **Назад** на панели навигации. Метод может быть вызван для других событий, поэтому сначала мы проверяем родительское представление. Если произошло любое изменение элемента, то **self.item** изменяется и выполняется обратный вызов **editCompleteBlock**:

        - (void)didMoveToParentViewController:(UIViewController *)parent
        {
            if (![parent isEqual:self.parentViewController]) {
                NSNumber *completeValue = [NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0];
                
                Boolean changed =
                    [self.item valueForKey:@"text"] != [self.itemText text] ||
                    [self.item valueForKey:@"complete"] != completeValue;
                
                if (changed) {
                    [self.item setValue:[self.itemText text] forKey:@"text"];
                    [self.item setValue:completeValue forKey:@"complete"];
                    
                    self.editCompleteBlock(self.item);
                }
            }
        }

### <a name="add-segue"></a>Добавление контроллера представлений элемента списка дел и перехода в раскадровку

1. Снова откройте файл **MainStoryboard_iPhone.storyboard** в навигаторе проекта.

2. В раскадровку добавьте новый контроллер представлений для элемента списка задач справа от существующего **контроллера представлений списка задач**. Установите для нового контроллера пользовательский класс **QSItemViewController**. Дополнительные сведения см. в статье [Добавление сцены в раскадровку] (Англ.).

3. Добавьте переход с именем **Show** от **контроллера представлений списка дел** к **контроллеру представлений элемента списка дел**. После этого в инспекторе атрибутов задайте для перехода идентификатор **detailSegue**. 

    Не создавайте этот переход от ячейки или кнопки исходного контроллера представлений. Вместо этого нажмите и удерживайте клавишу CTRL и проведите от значка контроллера представлений над **контроллером представлений списка дел** в интерфейсе раскадровки к **контроллеру представлений элемента списка дел**.

    ![][todo-list-view-controller-add-segue]

    Если случайно создать переход от ячейки, он будет выполняться дважды при запуске приложения, что приведет к следующей ошибке:

        Nested push animation can result in corrupted navigation bar

    Дополнительные сведения о переходах см. в разделе [Добавление перехода между сценами в раскадровке]. 

4. Добавьте текстовое поле для текста элемента, сегментированный элемент управления для состояния выполнения и метки для нового **контроллера представления элемента списка задач**. В сегментированном элементе управления укажите для элемента **Segment 0** заголовок **Yes**, а для элемента **Segment 1** - **No**. Соедините новые поля с выходами кода. Дополнительные сведения можно найти в статьях [Создание пользовательского интерфейса] (Англ.) и [Сегментированные элементы управления] (Англ.).

      ![][add-todo-item-view-controller-3]

5. Соедините новые поля с соответствующими выходами, добавленными в файл **QSItemViewController.m**. Соедините поле текста элемента с выходом **itemText**, а сегментированный элемент управления состояния выполнения с выходом **itemComplete**. Дополнительные сведения можно найти в статье [Создание связи с выходом] (Англ).

6. Установите делегирование текстовых полей в контроллер представлений. Нажмите CTRL и перетащите текстовое поле на значок контроллера представления под **контроллером представления элемента списка задач** в интерфейсе раскадровки и выберите выход делегирования. Это укажет на делегирование текстового поля данному контроллеру.

7. Проверьте работу приложения после внесения изменений. Запустите приложение в имитаторе. Добавьте элементы в список задач и выберите их. Вы увидите контроллер представления элементов (пустой).

      ![][add-todo-item-view-controller-4]          ![][add-todo-item-view-controller-5]

### <a name="add-item-details"></a>Добавление сведений об элементах в контроллер представлений элемента списка дел

1. Мы будем обращаться к **QSItemViewController** из **QSTodoListViewController.m**. В **QSTodoListViewController.m** добавим строку для импорта **QSItemViewController.h**.

        #import "QSItemViewController.h"

2. В интерфейсе **QSTodoListViewController** в **QSTodoListViewController.m** добавьте новое свойство для хранения редактируемого элемента:

        @property (strong, nonatomic)   NSDictionary *editingItem;

3. Реализуйте **tableView:didSelectRowAtIndexPath:** в **QSTodoListViewController.m**, чтобы сохранить изменяемый элемент и вызвать переход к подробному представлению.

        - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
            NSManagedObject *item = [self.fetchedResultsController objectAtIndexPath:indexPath];
            self.editingItem = [MSCoreDataStore tableItemFromManagedObject:item]; // map from managed object to dictionary
            
            [self performSegueWithIdentifier:@"detailSegue" sender:self];
        }

4. Реализуйте **prepareForSegue:sender:** в **QSTodoListViewController.m** для передачи элемента в **контроллер представлений элемента списка дел** и укажите обратный вызов, когда пользователь выходит из подробного представления:

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSItemViewController *ivc = (QSItemViewController *) [segue destinationViewController];
                ivc.item = [self.editingItem mutableCopy];
                
                ivc.editCompleteBlock = ^(NSDictionary *editedValue) {
                    [self.todoService updateItem:editedValue completion:^(NSUInteger index) {
                        self.editingItem = nil;
                    }];
                };
            }
        }

5. Проверьте работу приложения после внесения изменений. Запустите приложение в имитаторе. Добавьте элементы в список задач и выберите их. В контроллере представления элементов будут показаны сведения об элементах списка задач.

      ![][add-todo-item-view-controller-6]

### <a name="saving-edits"></a>Добавление поддержки сохранения изменений

1. При нажатии кнопки "Назад" в навигации все изменения будут потеряны. Данные были отправлены в подробное представление, однако не возвращаются в основное представление. Поскольку мы уже передали указатель на копию элемента, можно использовать его для получения списка обновлений элемента и внесения изменений на сервере. Сначала изменим класс оболочки сервера **QSTodoService** в **QSTodoService.m**, удалив операцию **completeItem** и добавив новую операцию **updateItem**. Это связано с тем, что операция **completeItem** только помечает элементы как выполненные, а операция **updateItem** будет их обновлять.

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            
            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:mutable completion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                
                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

2. Удалите объявление операции **completeItem** из **QSTodoService.h** и добавьте такое объявление операции **updateItem**:

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion;

3. Протестируйте приложение. Проверьте работу приложения после внесения изменений. Запустите приложение в имитаторе. Добавьте элементы в список задач и выберите их. Измените элемент и вернитесь назад. Убедитесь, что описание элемента обновлено в основном представлении приложения. Обновите приложение перетаскиванием вниз и убедитесь, что изменение отражено в удаленной службе.

### <a name="conflict-handling-problem"></a>Проблема обработки конфликтов

1. Рассмотрим, что происходит, если два разных клиента пытаются одновременно изменить один и тот же фрагмент данных. В примере списке ниже есть элемент "Mobile Services is Cool!" Давайте изменить его, например, на "I love Mobile Services!" на одном устройстве и на "I love Azure!" на другом.

      ![][conflict-handling-problem-1]

2. Запустите приложение в двух местах: на двух устройствах iOS или на устройстве iOS и в имитаторе. Если у вас нет физического устройства для тестирования, запустите один экземпляр в имитаторе и, используя клиент REST, отправьте запрос PATCH в мобильную службу. URL-адрес запроса PATCH отражает имя мобильной службы, имя таблицы элемента списка задач и идентификатор изменяемой таблицы, а заголовок x-zumo-application является ключом приложения:

        PATCH https://donnam-tutorials.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: xuAdWVDcLuCNfkTvOfaqzCCSBVHqoy96

        {
            "id": "CBBF4464-E08A-47C9-B6FB-6DCB30ACCE7E",
            "text": "I love Azure!"
        }

3. Теперь обновите элементы в двух экземплярах приложения. В выходном журнале в Xcode появится ошибка:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

  Это связано с тем, что в вызове **pullWithQuery:completion:** в блоке выполнения параметр ошибки не будет пустым. В результате ошибка будет записана в выходной журнал в **NSLog**.

### <a name="service-add-conflict-handling"></a>Изменение QSTodoService для поддержки обработки конфликтов

1. Предоставим пользователю возможность принимать решение об обработке конфликта в клиенте. Для этого реализуем протокол **MSSyncContextDelegate**. В **QSTodoService.h** и **QSTodoService.m** замените объявление фабричного метода **(QSTodoService *)defaultService;** приведенным ниже оператором, чтобы получать делегирование контекста синхронизации в качестве параметра:

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2. В **QSTodoService.m** измените строку **init** как показано ниже, чтобы получать делегирование контекста синхронизации в качестве параметра:

€

3. В **QSTodoService.m** замените вызов **init** в **defaultServiceWithDelegate** на **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4. В **QSTodoService.m** измените инициализацию **self.client.syncContext** для передачи делегирования в **syncDelegate** вместо **nil**:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### <a name="add-alert-view"></a>Добавление вспомогательного представления предупреждений пользовательского интерфейса для поддержки обработки конфликтов

1. В случае конфликта предоставим пользователю возможность выбора версии для сохранения:
  * оставить версию клиента (версия на сервере будет заменена);
  * оставить версию сервера (будет обновлена локальная таблица клиента);
  * не оставлять ни одну из версий (передача отменяется, операция остается незавершенной).

  Так как во время отображения подсказки может появиться другое изменение, варианты будут отображаться, пока сервер возвращает отрицательный ответ. В нашем коде используем вспомогательный класс, который выводит представление предупреждения и принимает делегирование, вызываемое при отображении этого представления. Сначала определим вспомогательный класс **QSUIAlertViewWithBlock**.

2. Используя Xcode, добавьте новый класс **QSUIAlertViewWithBlock** и замените **QSUIAlertViewWithBlock.h** таким содержимым:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

3. Затем замените **QSUIAlertViewWithBlock.m** таким файлом:

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### <a name="add-conflict-handling"></a>Добавление обработчика конфликтов в контроллер представлений списка дел

1. Измените **viewDidLoad** в **QSTodoListViewController.m**. Замените вызов **defaultService** на вызов **defaultServiceWithDelegate**:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. В **QSTodoListViewController.h** добавьте **&lt;MSSyncContextDelegate&gt;** в объявление интерфейса, чтобы реализовать протокол **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. Добавьте следующую инструкцию импорта в начало **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4. Наконец, добавьте две описанные ниже операции в **QSTodoListViewController.m** для использования вспомогательного класса и приглашения пользователю обработать конфликт одним из трех способов.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

### <a name="test-app"></a>Тестирование приложения

Протестируйте приложение с конфликтами. Одновременно измените один элемент в двух разных экземплярах приложения либо воспользуйтесь приложением и клиентом REST. 

Выполните жест обновления в экземплярах приложения (перетаскивание сверху вниз). Появится запрос на согласование конфликта.

![][conflict-ui]


### Сводка

Для создания готового проекта по обнаружению конфликтов на основе проекта из учебника [Приступая к работе с автономными данными] сначала была добавлена возможность редактирования и обновления элементов списка задач.

Для этого был добавлен новый контроллер подробного представления элементов, связанный с контроллерами основного и подробного представлений, и добавлена возможность сохранения изменений и их передачи в облако.

Затем вы узнали, что происходит при возникновении конфликта. Была добавлена поддержка обработчика конфликтов с помощью реализации протокола **MSSyncContextDelegate**. Также включена поддержка делегирования контекста синхронизации с помощью серверного класса интерфейса **QSTodoService**, **QSTodoListViewController** и вспомогательных классов.

Добавлен вспомогательный класс **QSUIAlertViewWithBlock** для отображения предупреждений для пользователей. Наконец, добавлен код в **QSTodoListViewController** для вывода предложения пользователю согласовать конфликт одним из трех способов.

<!-- URLs. -->

[Обновление проекта приложения в целях разрешения редактирования 
]: #update-app
[Обновление контроллера представлений списка дел]: #update-list-view
[Добавление контроллера представлений элементов списка дел]: #add-view-controller
[Добавление контроллера представлений элемента списка дел и перехода в раскадровку]: #add-segue
[Добавление сведений об элементах в контроллер представлений элемента списка дел]: #add-item-details
[Добавление поддержки сохранения изменений]: #saving-edits
[Проблема обработки конфликтов]: #conflict-handling-problem
[Изменение QSTodoService для поддержки обработки конфликтов]: #service-add-conflict-handling
[Добавление вспомогательного представления предупреждений пользовательского интерфейса для поддержки обработки конфликтов]: #add-alert-view
[Добавление обработчика конфликтов в контроллер представлений списка дел]: #add-conflict-handling
[Тестирование приложения]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Сегментированные элементы управления]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Справка редактора моделей Core Data]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Создание связи с выходом]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Создание пользовательского интерфейса]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Добавление перехода между сценами в раскадровке]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Добавление сцены в раскадровку]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Загрузка предварительной версии пакета SDK]: http://aka.ms/Gc6fex
[Использование клиентской библиотеки мобильных служб для iOS]: /ru-ru/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Приступая к работе с автономными функциями (для iOS)]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Приступая к работе с автономными данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-offline-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/


<!--HONumber=42-->
