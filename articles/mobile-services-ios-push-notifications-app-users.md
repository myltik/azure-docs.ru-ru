<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Отправка push-уведомлений пользователям с использованием мобильных служб

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS" class="current">iOS</a><a href="/ru-ru/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>

Этот раздел является расширением [предыдущего учебника по push-уведомлениям][предыдущего учебника по push-уведомлениям] и добавляет новую таблицу для хранения маркеров службы push-уведомлений Apple (APNS). Эти маркеры можно использовать для отправки push-уведомлений пользователям приложения iPhone или iPad.

В этом учебнике рассматриваются следующие шаги по обновлению push-уведомлений в приложении:

1.  [Создание таблицы устройств][Создание таблицы устройств]
2.  [Обновление приложения][Обновление приложения]
3.  [Обновление серверных скриптов][Обновление серверных скриптов]
4.  [Проверка режима работы push-уведомлений][Проверка режима работы push-уведомлений]

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с push-уведомлениями][предыдущего учебника по push-уведомлениям]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с push-уведомлениями][предыдущего учебника по push-уведомлениям].

## <a name="create-table"></a>

## <span class="short-header">Создание таблицы</span>Создание новой таблицы Devices</h2>

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

    ![][0]

2.  Щелкните вкладку **Данные**, а затем нажмите **Создать**.

    ![][1]

    Откроется диалоговое окно **Создание новой таблицы**.

3.  Сохранив параметр по умолчанию **Все с ключом приложения** для всех разрешений, введите *Registration* в поле **Имя таблицы** и нажмите кнопку «Проверить».

    ![][2]

    При этом создается таблица **Devices**, где отдельно от данных элементов хранятся маркеры устройств, используемые для отправки push-уведомлений.

Далее следует изменить приложение push-уведомлений, чтобы данные сохранялись в этой новой таблице, а не в таблице **TodoItem**.

## <a name="update-app"></a>Обновление приложения

1.  В Xcode откройте файл QSTodoService.h и добавьте следующее объявление метода:

        // Declare method to register device token for other users
        - (void)registerDeviceToken:(NSString *)deviceToken;

    Это позволяет делегату приложения регистрировать deviceToken в мобильной службе.

2.  В QSTodoService.m добавьте следующий метод экземпляра:

        // Instance method to register deviceToken in Devices table.
        // Called in AppDelegate.m when APNS registration succeeds.
        - (void)registerDeviceToken:(NSString *)deviceToken
        {
            MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
            NSDictionary *device = @{ @"deviceToken" : deviceToken };
            // Insert the item into the devices table and add to the items array on completion
            [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
                if (error) {
                    NSLog(@"ERROR %@", error);
                }
            }];
        }

    Это позволяет другим вызывающим объектам регистрировать маркер устройства в мобильных службах.

3.  В файле QSAppDelegate.m добавьте следующую инструкцию import:

        #import "QSTodoService.h"

    Этот код позволяет AppDelegate узнать о реализации TodoService.

4.  В QSAppDelegate.m замените метод **didRegisterForRemoteNotificationsWithDeviceToken** следующим кодом:

        // We have registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
           // Register the APNS deviceToken with the Mobile Service Devices table.
           NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
           NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
           QSTodoService *instance = [QSTodoService defaultService];
           [instance registerDeviceToken:token];
        }

5.  В QSTodoListViewController.m найдите метод **(IBAction)onAdd** и *удалите* следующий код:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];
    
        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    Замените ее следующим кодом:

        // We removed the delegate; this application no longer passes the deviceToken here.
        // Remove the device token from the payload
        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

Приложение было обновлено для использования новой таблицы Devices для хранения маркеров устройств, которые используются для отправки push-уведомлений обратно на устройство.

## <a name="update-scripts"></a>Обновление серверных сценариев

1.  На портале управления щелкните вкладку **Данные** и затем щелкните таблицу **Devices**.

    ![][3]

2.  В таблице **Devices** щелкните вкладку **Сценарий** и выберите команду **Вставить**.

    ![][4]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **Devices**.

3.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
           var devicesTable = tables.getTable('Devices');
           devicesTable.where({
               token: item.token
           }).read({
               success: insertTokenIfNotFound
           });
           function insertTokenIfNotFound(existingTokens) {
               if (existingTokens.length > 0) {
                   request.respond(200, existingTokens[0]);
               } else {
                   request.execute();
               }
           }
        }

    Этот сценарий проверяет таблицу **Devices** на наличие существующего устройства с таким же маркером. Вставка выполняется только в том случае, если совпадающее устройство не найдено. Это предотвращает дублирование записей устройств.

4.  Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**.

    ![][5]

5.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
          request.execute({
              success: function() {
                  request.respond();
                  sendNotifications();
              }
          });
          function sendNotifications() {
              var devicesTable = tables.getTable('Devices');
              devicesTable.read({
                  success: function(devices) {
                      // Set timeout to delay the notifications, 
                      // to provide time for the app to be closed 
                      // on the device to demonstrate toast notifications.
                      setTimeout(function() {
                          devices.forEach(function(device) {
                              push.apns.send(device.deviceToken, {
                                  alert: "Toast: " + item.text,
                                  payload: {
                                      inAppMessage: 
                                      "Hey, a new item arrived: '" + 
                                      item.text + "'"
                                  }
                              });
                          });
                      }, 2500);
                  }
              });
          }

    }

    Этот скрипт вставки отправляет push-уведомление (с текстом вставленного элемента) по всем устройствам, хранящимся в таблице **Devices**.

## <a name="test"></a><span class="short-header">Тестирование приложения</span>Тестирование push-уведомлений в приложении

1.  Нажмите кнопку **Запуск**, чтобы выполнить сборку проекта, и запустите приложение на устройстве iOS, затем введите в приложении осмысленный текст, например *Новая задача мобильных служб*, и нажмите значок «плюс» (**+**).

    ![][6]

2.  Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

    ![][7]

3.  Повторите шаг 2 и немедленно закройте приложение, а затем проверьте, что отображается следующее всплывающее уведомление.

    ![][8]

Вы успешно завершили ознакомление с данным учебником.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с push-уведомлениями. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]
    <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    <br/>Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    <br/>Узнайте больше о регистрации и использовании серверных сценариев.

<!-- Anchors.  Images. URLs. -->

  [предыдущего учебника по push-уведомлениям]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios
  [Создание таблицы устройств]: #create-table
  [Обновление приложения]: #update-app
  [Обновление серверных скриптов]: #update-scripts
  [Проверка режима работы push-уведомлений]: #test-app
  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png
  [3]: ./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png
  [4]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png
  [5]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png
  [7]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png
  [8]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
