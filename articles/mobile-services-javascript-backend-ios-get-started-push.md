<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS" class="current">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a><!---<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Серверная часть .NET" >Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение iOS. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Apple (APNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

> [WACOM.NOTE]В этом учебнике демонстрируется интеграция концентраторов уведомлений в мобильные службы, т. е. способ отправки push-уведомлений из мобильной службы. Если вы используете более позднюю версию мобильной службы с устаревшими push-уведомлениями, которая еще не обновлена и не может использовать концентраторы уведомлений, *рекомендуется обновить ее* в рамках этого учебника. Если вы не хотите обновлять ее, воспользуйтесь этой версией учебника: [Начало работы с push-уведомлениями (устаревшая версия)][Начало работы с push-уведомлениями (устаревшая версия)].

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
-   [XCode 4.5][XCode 4.5]
-   Устройство с iOS 6.0 (или более поздней версии)
-   Участие в программе для разработчиков на платформе iOS

> [WACOM.NOTE] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

[WACOM.INCLUDE [Включение push-уведомлений Apple](../includes/enable-apple-push-notifications.md)]

## Настройка мобильных служб для отправки push-запросов

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Добавление push-уведомлений в приложение

1.  В QSAppDelegate.m замените следующий метод обработчика в реализации:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  В QSAppDelegate.m добавьте следующий метод обработчика в реализации:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3.  В QSAppDelegate.m добавьте следующий метод обработчика в реализации:

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4.  В QSAppDelegate.m добавьте следующий метод обработчика в реализации:

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5.  В QSTodoListViewController.m импортируйте файл QSAppDelegate.h, чтобы можно было использовать делегата для получения токена устройства:

        #import "QSAppDelegate.h"

6.  В QSTodoListViewController.m измените действие **(IBAction)onAdd**, определив расположение следующей строки:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

Замените ее следующим кодом:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

Этот код добавляет ссылку на **QSAppDelegate** для получения токена устройства, а затем изменяет полезные данные запроса для включения этого токена устройства.

> [WACOM.NOTE] Необходимо добавить этот код перед вызовом метода **addItem**.

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## Обновление зарегистрированных скриптов вставки на портале управления

1.  На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

    ![][0]

2.  В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

    ![][1]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    При этом регистрируется новый скрипт вставки, в котором используется [объект apns][объект apns] для отправки push-уведомления (вставленный текст) на устройство, указанное в запросе вставки.

    > [WACOM.NOTE] Этот скрипт задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения push-уведомления.

## Тестирование push-уведомлений в приложении

1.  Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

    ![][2]

    > [WACOM.NOTE] Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2.  В приложение введите содержательный текст (например, *Новая задача мобильных служб*) и щелкните знак "плюс" (**+**).

    ![][3]

3.  Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

    ![][4]

4.  Повторите шаг 2 и немедленно закройте приложение, а затем проверьте, что отображается следующее push-уведомление.

    ![][5]

Вы успешно завершили ознакомление с данным учебником.

## Дальнейшие действия

В этом учебнике демонстрируются основы включения отправки push-уведомлений в мобильных службах и концентраторах уведомлений приложений iOS. Далее изучите один из следующих учебников:

-   [Отправка push-уведомлений выполнившим проверку подлинности пользователям][Отправка push-уведомлений выполнившим проверку подлинности пользователям]
    Использование тегов для отправки push-уведомлений из мобильной службы только прошедшим проверку подлинности пользователям.

-   [Рассылка уведомлений подписчикам][Рассылка уведомлений подписчикам]
    Регистрация пользователей и получение push-уведомлений для интересующих их категорий.
    <!--- + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->
    Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах:

-   [Приступая к работе с данными][Приступая к работе с данными]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]
    Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

-   [Справочник принципов использования мобильных служб Objective-C][Справочник принципов использования мобильных служб Objective-C]
    Дополнительные сведения об использовании мобильных службы с Objective-C и iOS.

-   [Справочник скриптов сервера мобильных служб][Справочник скриптов сервера мобильных служб]
    Дополнительные сведения о реализации бизнес-логики в мобильной службе.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs.   -->

  [Создание запроса подписи сертификата]: #certificates
  [Регистрация приложения и включение push-уведомлений]: #register
  [Создание профиля подготовки для приложения]: #profile
  [Настройка мобильных служб]: #configure
  [Добавление push-уведомлений в приложение]: #add-push
  [Обновление скриптов для отправки push-уведомлений]: #update-scripts
  [Вставка данных для получения уведомлений]: #test
  [SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started
  [Включение push-уведомлений Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [объект apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Отправка push-уведомлений выполнившим проверку подлинности пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/
  [Рассылка уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-ios-send-breaking-news/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users
  [Справочник принципов использования мобильных служб Objective-C]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Справочник скриптов сервера мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
