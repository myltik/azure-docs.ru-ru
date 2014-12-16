<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="Начало работы с push-уведомлениями (iOS) | Центр мобильных разработок" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Добавление push-уведомлений к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В данном разделе рассматривается использование мобильных служб Azure для отправки push-уведомлений через службу push-уведомлений Apple (APNS) в приложение iOS. В этом учебнике объясняется включение push-уведомлений с помощью концентраторов уведомлений Azure в [проекте быстрого запуска](http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-ios-get-started/). В результате ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание запроса подписи сертификата]
2. [Регистрация приложения и включение push-уведомлений]
3. [Создание профиля подготовки для приложения]
4. [Настройка мобильных служб]
5. [Добавление push-уведомлений в приложение]
6. [Обновление скриптов для отправки push-уведомлений]
7. [Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

+ [SDK мобильных служб для iOS]
+ [XCode 4.5][Установка Xcode]
+ Устройство с iOS 6,0 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS

   > [WACOM.NOTE] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Этот учебник создан на основе краткого руководства по мобильным службам. Прежде чем приступать к этому учебнику, необходимо пройти до конца учебник [Начало работы с мобильными службами].


[WACOM.INCLUDE [Включение push-уведомлений Apple](../includes/enable-apple-push-notifications.md)]


## Настройка мобильных служб для отправки push-запросов

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Добавление push-уведомлений в приложение

1. В QSAppDelegate.m вставьте следующий фрагмент, чтобы импортировать SDK мобильных служб iOS:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. В файле QSAppDelegate.m замените следующий метод обработчика в реализации:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. В QSAppDelegate.m добавьте следующий метод обработчика в реализацию. Не забудьте скопировать значения URL-адреса мобильной службы и ключа приложения и включить их для заполнителей:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
			MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"];

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. В QSAppDelegate.m добавьте следующий метод обработчика в реализации:

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. В QSAppDelegate.m добавьте следующий метод обработчика в реализации:  

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

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## Обновление зарегистрированных сценариев вставки на портале управления

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

   	![][21]

2. В **todoitem** откройте вкладку **Скрипт** и выберите **Вставка**.

  	![][22]

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и щелкните **Сохранить**:

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

   	При этом регистрируется новый скрипт вставки, в котором используется [объект apns] для отправки push-уведомления (вставленного текста) на устройство, указанное в запросе вставки.


   	> [WACOM.NOTE] Этот скрипт задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения push-уведомления.

## Тестирование push-уведомлений в приложении

1. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с поддержкой iOS, затем щелкните **ОК** для получения push-уведомлений

  	![][23]

    > [WACOM.NOTE]  Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите значимый текст, такой как _Новая задача мобильных служб_, затем щелкните значок (**+**).

  	![][24]

3. Проверьте, получено ли уведомление, затем щелкните **ОК**, чтобы прекратить уведомления.

  	![][25]

4. Повторите шаг 2 и немедленно закройте приложение, а затем проверьте, что отображается следующее push-уведомление.

  	![][26]

Вы успешно завершили ознакомление с данным учебником.

## Дальнейшие действия

В этом учебнике показаны основы использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений в приложение iOS. Затем рекомендуется ознакомиться с одним из следующих учебников:

+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Применение тегов для отправки push-уведомлений из мобильной службы только пользователю, прошедшему проверку подлинности.

+ [Отправка широковещательных уведомлений подписчикам]
	<br/>Предоставление пользователям возможности регистрироваться и получать push-уведомления только по интересующим их категориям.
<!---
+ [Send template-based notifications to subscribers]
	<br/>Использование шаблонов для отправки push-уведомлений из мобильной службы без создания зависящих от платформы полезных данных на сервере.
-->
Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с различными типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Применение концентраторов уведомлений для доставки уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Устранение неполадок и отладка решений на основе концентраторов уведомлений. 

* [Концептуальный справочник с практическими рекомендациями по мобильным службам Objective-C]
  <br/>Дополнительные сведения об использовании мобильных служб с Objective-C и iOS.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о способах реализации бизнес-логики в мобильной службе.

<!-- Anchors. -->
[Создание запроса подписи сертификата]: #certificates
[Регистрация приложения и включение push-уведомлений]: #register
[Создание профиля подготовки для приложения]: #profile
[Настройка мобильных служб]: #configure
[Обновление скриптов для отправки push-уведомлений]: #update-scripts
[Добавление push-уведомлений в приложение]: #add-push
[Вставка данных для получения уведомлений]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs.   -->
[Установить Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Портал подготовки iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users
[Портал управления Azure]: https://manage.windowsazure.com/
[объект apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Отправка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-ios-send-breaking-news/
[Отправка подписчикам уведомлений на основе шаблона]: /ru-ru/documentation/articles/notification-hubs-ios-send-localized-breaking-news/

[Концептуальный справочник с практическими рекомендациями по мобильным службам Objective-C]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
