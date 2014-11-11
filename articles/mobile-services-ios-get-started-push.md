<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app (legacy push)." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Приступая к работе с push-уведомлениями в мобильных службах (принудительная отправка устаревшего типа)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Магазин Windows C#">Магазин Windows C#</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/ru-ru/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
    <a href="/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение для iOS. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Apple (APNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

> [WACOM.NOTE]Указания в этом разделе приведены с учетом использования *имеющихся* мобильных служб, которые *пока еще не были обновлены* до версии, поддерживающей интеграцию с центрами уведомлений. При создании *новой* мобильной службы интегрированные функции включаются автоматически. Информацию о работе с мобильными службами последних версий см. в разделе [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].
>
> Мобильные службы теперь интегрируются с центрами уведомлений Azure для поддержки дополнительных функций push-уведомлений, таких как шаблоны, использование нескольких платформ и лучшее масштабирование. *Если это возможно, обновите имеющиеся мобильные службы, чтобы использовать центры уведомлений*. После обновления ознакомьтесь с этой версией раздела [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Создание запроса подписи сертификата][Создание запроса подписи сертификата]
2.  [Регистрация приложения и включение push-уведомлений][Регистрация приложения и включение push-уведомлений]
3.  [Создание профиля подготовки для приложения][Создание профиля подготовки для приложения]
4.  [Настройка мобильных служб][Настройка мобильных служб]
5.  [Добавление push-уведомлений в приложение][Добавление push-уведомлений в приложение]
6.  [Обновление скриптов для отправки push-уведомлений][Обновление скриптов для отправки push-уведомлений]
7.  [Вставка данных для получения уведомлений][Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

-   [SDK мобильных служб для iOS][SDK мобильных служб для iOS]
-   [Xcode 4.5][Xcode 4.5]
-   Устройство с iOS 5.0 (или более поздней версии)
-   Участие в программе для разработчиков на платформе iOS

  > [WACOM.NOTE] ] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

[WACOM.INCLUDE [Включение push-уведомлений Apple](../includes/enable-apple-push-notifications.md)]

## Настройка мобильных служб для отправки push-запросов

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Добавление push-уведомлений в приложение

1.  В Xcode откройте файл QSAppDelegate.h и добавьте ниже свойства \***window** следующее:

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Если в мобильной службе включена динамическая схема, при вставке нового элемента с этим свойством в таблицу **TodoItem** автоматически добавляется столбец deviceToken.

2.  В файле QSAppDelegate.m замените следующий метод обработчика в реализации:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  В QSAppDelegate.m добавьте следующий метод обработчика в реализации:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  В QSAppDelegate.m добавьте следующий метод обработчика в реализации:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5.  В QSAppDelegate.m добавьте следующий метод обработчика в реализации:

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  В QSTodoListViewController.m импортируйте файл QSAppDelegate.h, чтобы можно было использовать этот делегат для получения маркера устройства:

        #import "QSAppDelegate.h"

7.  В QSTodoListViewController.m измените действие **(IBAction)onAdd**, найдя следующую строку:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

   Замените ее следующим кодом:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    This adds a reference to the **QSAppDelegate** to obtain the device token and then modifies the request payload to include that device token.

    > [WACOM.NOTE] You must add this code before to the call to the <strong>addItem</strong> method.

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## Обновление зарегистрированных сценариев вставки на портале управления

1.  На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

    ![][0]

2.  В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

    ![][1]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    При этом регистрируется новый скрипт вставки, в котором используется [объект apns][объект apns] для отправки push-уведомления (вставленный текст) на устройство, указанное в запросе вставки.

    > [WACOM.NOTE] Этот сценарий задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения всплывающего уведомления.

## Тестирование push-уведомлений в приложении

1.  Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

    ![][2]

    > [WACOM.NOTE] Необходимо явно разрешить прием push-уведомлений от приложения. Этот запрос отображается только при первом запуске приложения.

2.  В приложении введите осмысленный текст, например *Новая задача для мобильных служб*, и щелкните значок «плюс» (**+**).

    ![][3]

3.  Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

    ![][4]

4.  Повторите шаг 2 и немедленно закройте приложение, а затем проверьте, что отображается следующее всплывающее уведомление.

    ![][5]

Вы успешно завершили ознакомление с данным учебником.

## Дальнейшие действия

В этом простом примере пользователь получает push-уведомление с только что вставленными данными. Маркер устройства, используемый APNS, передается клиентом мобильной службе в запросе. В следующем учебнике [Принудительная отправка уведомлений пользователям приложения][Принудительная отправка уведомлений пользователям приложения] вы будете создавать отдельную таблицу "Устройства" для хранения маркеров устройств и отправлять push-уведомление всем сохраненным каналам при выполнении вставки.

<!-- Anchors.  Images.  URLs. -->

  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Создание запроса подписи сертификата]: #certificates
  [Регистрация приложения и включение push-уведомлений]: #register
  [Создание профиля подготовки для приложения]: #profile
  [Настройка мобильных служб]: #configure
  [Добавление push-уведомлений в приложение]: #add-push
  [Обновление скриптов для отправки push-уведомлений]: #update-scripts
  [Вставка данных для получения уведомлений]: #test
  [SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [Xcode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
  [Включение push-уведомлений Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [объект apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Принудительная отправка уведомлений пользователям приложения]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios
