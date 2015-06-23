<properties 
	pageTitle="Создание приложений, работающих в режиме реального времени, с помощью Pusher (iOS) — мобильные службы" 
	description="Узнайте, как с помощью Pusher отправлять уведомления в приложение служб мультимедиа Azure в iOS." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="donnam"/>


# Создание приложений, работающих в режиме реального времени, с помощью мобильных служб и Pusher
<div class="dev-center-tutorial-selector sublanding">
	<a href="" title="iOS" class="current">iOS</a>
</div>

В этом разделе показано, как добавить функции реального времени в приложении на основе мобильных служб Azure. По завершении ваши данные TodoList будут синхронизироваться в режиме реального времени для всех запущенных экземпляров приложения.

В учебники [Push-уведомления для пользователей][] показано, как использовать push-уведомления для информирования пользователей о новых элементах в списке дел. Push-уведомления — это отличный способ для демонстрации случайных изменений. Однако иногда приложению необходимо часто отправлять уведомления в режиме реального времени. Поддержку уведомлений в реальном времени можно добавить в вашу мобильную службу с помощью интерфейса API Pusher. В этом учебнике мы используем Pusher с мобильными службами для синхронизации списка дел при изменении любого запущенного экземпляра приложения.

Pusher — это облачная служба, которая, как и мобильные службы, позволяет легко создавать приложения, работающие в режиме реального времени. Pusher можно использовать для быстрого создания опросов, чатов, многопользовательских игр, приложений для совместной работы, решений для трансляции динамических данных и контента — и это только начало! Дополнительные сведения см. на веб-сайте [http://pusher.com](http://pusher.com).

В этом учебнике рассматриваются следующие основные шаги для добавления в приложение списка дел функций совместной работы в реальном времени:

1. [Создание учетной записи Pusher][]
2. [Обновление приложения][]
3. [Установка серверных скриптов][]
4. [Тестирование приложения][]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][].

## <a name="sign-up"></a>Создание учетной записи Pusher

[AZURE.INCLUDE [pusher-sign-up](../../includes/pusher-sign-up.md)]

## <a name="update-app"></a>Обновление приложения

После настройки учетной записи Pusher следующим шагом является изменение кода приложения iOS для поддержки новых возможностей.

###Установка библиотеки libPusher

Библиотека [LibPusher][] позволяет получить доступ к Pusher из операционной системы iOS.

1. Скачайте библиотеку libPusher [по этой ссылке][libPusherDownload].

2. Создайте в проекте группу с именем _libPusher_.

3. В средстве Finder распакуйте скачанный ZIP-файл, выберите папки **libPusher-combined.a** и **/headers** и перетащите эти элементы в группу **libPusher** в своем проекте.

4. Установите флажок **Копировать элементы в папку конечной группы** и нажмите кнопку **Готово**

	![][add-files-to-group]

   При этом файлы LibPusher будут скопированы в ваш проект.

5. В корневом каталоге проекта в обозревателе проектов последовательно щелкните **Этапы построения**, **Добавить этап построения** и **Добавить файлы для копирования**.

6. Перетащите файл **libPusher-combined.a** из обозревателя проектов в раздел нового этапа сборки.

7. Замените **Назначение** на **Платформы** и щелкните **Копировать только при установке**.

	![][add-build-phase]

8. В области **Привязать двоичный файл к библиотекам** добавьте следующие библиотеки:

	- libicucore.dylib
	- CFNetwork.framework
	- Security.framework
	- SystemConfiguration.framework

9. Наконец, в разделе **Параметры построения** найдите параметр **Другие флаги компоновщика** и добавьте флаг **-all_load**.

	![][add-linker-flag]

	При этом флаг **-all_load** будет установлен для цели отладочной сборки.

Теперь библиотека установлена и готова к использованию.

### Добавление кода в приложение

1. В Xcode откройте файл **QSTodoService.h** и добавьте следующие объявления методов:

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2. Замените существующие объявления методов **addItem** и **completeItem** следующими:

		- (void) addItem:(NSDictionary *) item;
		- (void) completeItem: (NSDictionary *) item;

3. В файле **QSTodoService.m** добавьте следующий код для реализации новых методов:

        // Allows retrieval of items by id
		- (NSUInteger) getItemIndex:(NSDictionary *)item
		{
		    NSInteger itemId = [[item objectForKey: @"id"] integerValue];

		    return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
		}

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item not already in list
		    if(index == NSNotFound)
		    {
        		NSUInteger newIndex = [items count];
		        [(NSMutableArray *)items insertObject:item atIndex:newIndex];
		        return newIndex;
		    }
		    else
        		return -1;
		}

        // To be called when items are completed by other users
		- (NSUInteger) itemCompleted:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item exists in items list
		    if(index != NSNotFound)
		    {
		        NSMutableArray *mutableItems = (NSMutableArray *) items;
		        [mutableItems removeObjectAtIndex:index];
		    }
		    return index;
		}

	QSTodoService теперь позволяет находить элементы по **идентификатору** и добавлять завершенные элементы локально без отправки явных запросов к удаленной службе.

4. Замените существующие методы **addItem** и **completeItem** следующим кодом:

		-(void) addItem:(NSDictionary *)item
		{
		    // Insert the item into the TodoItem table and add to the items array on completion
		    [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
        		[self logErrorIfNotNil:error];
		    }];
		}

		-(void) completeItem:(NSDictionary *)item
		{
		    // Set the item to be complete (we need a mutable copy)
		    NSMutableDictionary *mutable = [item mutableCopy];
		    [mutable setObject:@(YES) forKey:@"complete"];

		    // Update the item in the TodoItem table and remove from the items array on completion
		    [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
		        [self logErrorIfNotNil:error];
		    }];
		}


	Обратите внимание, что теперь при получении событий от Pusher элементы добавляются и завершаются, а также обновляется пользовательский интерфейс.

5. В файле **QSTodoListViewController.h** добавьте следующие инструкции import:

		#import "PTPusherDelegate.h"
		#import "PTPusher.h"
		#import "PTPusherEvent.h"
		#import "PTPusherChannel.h"

6. Измените объявление интерфейса, добавив протокол **PTPusherDelegate** следующим образом:

		@interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7. Добавьте следующее новое свойство:

		@property (nonatomic, strong) PTPusher *pusher;

8. Добавьте следующий код объявления нового метода:

		// Sets up the Pusher client
		- (void) setupPusher;

9. В файле **QSTodoListViewController.m** добавьте следующую строку под остальными строками **@synthesise**, чтобы реализовать новое свойство:

		@synthesize pusher = _pusher;

10. Теперь добавьте следующий код, чтобы реализовать новый метод:

		// Sets up the Pusher client
		- (void) setupPusher {

			// Create a Pusher client, using your Pusher app key as the credential
		    // TODO: Move Pusher app key to configuration file
		    self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
		    self.pusher.reconnectAutomatically = YES;

    		// Subscribe to the 'todo-updates' channel
		    PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

    		// Bind to the 'todo-added' event
		    [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Add item to the todo list
        		NSUInteger index = [self.todoService itemAdded:channelEvent.data];

        		// If the item was not already in the list, add the item to the UI
		        if(index != -1)
        		{
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];

    		// Bind to the 'todo-completed' event
		    [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Update the item to be completed
		        NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

        		// As long as the item did exit in the list, update the UI
		        if(index != NSNotFound)
		        {
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];
		}

11. Замените заполнитель `**your_app_key**` на значение app_key, ранее скопированное из диалогового окна "Сведения о подключении".

12. Замените метод **onAdd** следующим кодом:

		- (IBAction)onAdd:(id)sender
		{
		    if (itemText.text.length  == 0) {
        		return;
		    }

		    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
		    [self.todoService addItem:item];

		    itemText.text = @"";
		}

13. В файле **QSTodoListViewController.m** найдите метод (void)viewDidLoad и добавьте вызов метода **setupPusher**, в результате чего первые несколько строк должны выглядеть следующим образом:

		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    [self setupPusher];

14. В конце метода **tableView:commitEditingStyle:forRowAtIndexPath** замените вызов **completeItem** следующим кодом:

		// Ask the todoService to set the item's complete value to YES
	    [self.todoService completeItem:item];

Приложение теперь может получать события от службы Pusher и обновлять локальный список дел соответствующим образом.



<h2><a name="install-scripts"></a>Установка серверных сценариев</h2>



Все, что осталось — настроить серверные скрипты. Мы вставим скрипт для обработки вставки или обновления элемента в таблице TodoList.



1. Выполните вход на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свою мобильную службу.


2. На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

	![][1]



3. В **TodoItem** перейдите на вкладку **Сценарий** и выберите **Вставить**.


	![][2]



	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.


4. Замените функцию вставки следующим кодом:


		var Pusher = require('pusher');

		function insert(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been inserted, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemCreatedEvent(item);
			    }
			});

			function publishItemCreatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-added', item );
			}
		}



5. Замените заполнители в сценарии выше на значение, ранее скопированное из диалогового окна «Сведения о подключении»:

	- **`**your_app_id**`**: значение app&#95;id
	- **`**your_app_key**`**: значение app&#95;key
	- **`**your_app_key_secret**`**: app&#95;key&#95;secret


6. Нажмите кнопку **Сохранить**. Вы настроили скрипт для публикации события в службе Pusher при каждой вставке элемента в таблицу **TodoItem**.


7. В раскрывающемся списке **Операция** выберите **Обновить**.


8. Замените функцию обновления следующим кодом:

		var Pusher = require('pusher');

		function update(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been updated, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemUpdatedEvent(item);
			    }
			});

			function publishItemUpdatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-completed', item );

			}
		}



9. Повторите шаг 5 для этого сценария, чтобы заменить заполнители.


10. Нажмите кнопку **Сохранить**. Вы настроили скрипт для публикации события в службе Pusher при каждом обновлении элемента.



<h2><a name="test-app"></a>Тестирование приложения</h2>



Для тестирования приложения необходимо запустить два экземпляра. Один экземпляр может работать на устройстве iOS, а другой — в симуляторе iOS.

1. Подключите устройство iOS, нажмите кнопку **Запуск** (или клавиши Command+R), чтобы запустить приложение на устройстве, а затем остановите отладку.

	Теперь приложение установлено на устройстве.

2. Запустите приложение в симуляторе iOS и одновременно запустите приложение на устройстве iOS.

	Теперь вы запустили два экземпляра приложения.

3. В одном из экземпляров приложения добавьте новый элемент списка дел.

	Убедитесь, что добавленный элемент отображается в другом экземпляре.

4. В одном экземпляре приложения выберите элемент списка дел, чтобы пометить его как завершенный.

	Убедитесь, что добавленный элемент исчез в другом экземпляре.

Поздравляем, вы успешно настроили приложение мобильной службы для синхронизации на всех клиентах в реальном времени.

## <a name="nextsteps"> </a>Дальнейшие действия

Теперь, когда вы знаете, насколько просто можно использовать службу Pusher с мобильными службами, перейдите по следующим ссылкам, чтобы узнать больше о службе Pusher.

-   Документация к интерфейсу API службы Pusher: <http://pusher.com/docs>
-   Учебники по службе Pusher: <http://pusher.com/tutorials>

Дополнительные сведения о регистрации и использовании серверных скриптов сценариев см. [справочнике по серверным скриптам мобильных служб].

<!-- Anchors. -->
[Создание учетной записи Pusher]: #sign-up
[Обновление приложения]: #update-app
[Установка серверных скриптов]: #install-scripts
[Тестирование приложения]: #test-app

<!-- Images. -->
[1]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png

[add-files-to-group]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
[add-build-phase]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
[add-linker-flag]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png

<!-- URLs. -->
[Push-уведомления для пользователей]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started
[libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
[libPusherDownload]: http://go.microsoft.com/fwlink/p/?LinkId=276998


[портал управления Azure]: https://manage.windowsazure.com/

[справочнике по серверным скриптам мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293

<!--HONumber=54--> 