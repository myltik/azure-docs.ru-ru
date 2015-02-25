<properties pageTitle="Приступая к работе с push-уведомлениями (iOS) | Центр мобильных разработок" description="Узнайте, как использовать мобильные службы Azure для отправки push-уведомлений в приложение iOS." services="mobile-services, notification-hubs" documentationCenter="ios" manager="dwrede" editor="" authors="krisragh"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="12/15/2014" ms.author="krisragh"/>

# Добавление push-уведомлений в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений с помощью службы APNS в приложение для iOS. В этом учебнике объясняется включение push-уведомлений с помощью концентраторов уведомлений Azure в [проекте быстрого запуска](http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-ios-get-started/). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание запроса подписи сертификата](#certificates)
2. [Регистрация приложения и включение push-уведомлений](#register)
3. [Создание профиля подготовки для приложения](#profile)
4. [Настройка мобильных служб](#configure)
5. [Добавление push-уведомлений в приложение](#add-push)
6. [Обновление скриптов для отправки push-уведомлений](#update-scripts)
7. [Вставка данных для получения уведомлений](#test)

Для работы с данным учебником требуется следующее:

+ [Пакет SDK для мобильных служб для iOS]
+ [XCode 4.5][Установка Xcode]
+ Устройство с iOS 6.0 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS

   > [AZURE.NOTE] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с данным учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами] или [Добавление мобильных служб к существующему приложению][Приступая к работе с данными].


[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>Настройка мобильных служб для отправки push-запросов

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>Обновление зарегистрированного скрипта вставки на портале управления

1. На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

   	![][21]

2. В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

  	![][22]

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

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

   	При этом регистрируется новый скрипт вставки, в котором используется [объект apns] для отправки push-уведомления (вставленный текст) на устройство, указанное в запросе вставки.


   	> [AZURE.NOTE] Этот сценарий задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения push-уведомления.

## <a id="add-push"></a>Добавление push-уведомлений в приложение

1. Вставьте в QSAppDelegate.m следующий фрагмент, чтобы импортировать пакет SDK для мобильных служб для iOS:

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

3. В QSAppDelegate.m добавьте следующий метод обработчика в реализации: Обязательно скопируйте значения "URL-адрес мобильной службы" и "Ключ приложения" и замените их заполнителями:

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

## <a id="test"></a>Тестирование push-уведомлений в приложении

1. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

  	![][23]

    > [AZURE.NOTE] Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите содержательный текст, например _A new Mobile Services task_, и щелкните значок "плюс" (**+**).

  	![][24]

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

  	![][25]

4. Повторите шаг 2 и немедленно закройте приложение, а затем проверьте, что отображается следующее push-уведомление.

  	![][26]

Вы успешно завершили ознакомление с данным учебником.

## <a id="next-steps"></a>Дальнейшие действия

В этом учебнике демонстрируются основы включения отправки push-уведомлений в мобильных службах и концентраторах уведомлений приложений iOS. Затем рекомендуется ознакомиться с одним из следующих учебников:

+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Дополнительная информация о том, как использовать теги для отправки push-уведомлений из мобильной службы только прошедшим проверку пользователям.

+ [Рассылка широковещательных уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.
<!---
+ [Рассылка уведомлений по шаблону подписчикам]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.
-->
Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными]
  <br/>Дополнительная информация о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительная информация об аутентификации учетных данных пользователей приложения с другими типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Дополнительные сведения о работе центров уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Руководство по устранению неполадок и решения по отладке центров уведомлений. 

* [Справочник по принципам использования мобильных служб Objective-C]
  <br/>Дополнительная информация об использовании мобильных служб с Objective-C и iOS.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительная информация о реализации бизнес-логики в вашей мобильной службе.

<!-- Anchors. -->


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
[Установка Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Портал подготовки iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Пакет SDK для мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users
[Портал управления Azure]: https://manage.windowsazure.com/
[Объект apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Рассылка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-ios-send-breaking-news/
[Рассылка уведомлений по шаблону подписчикам]: /ru-ru/documentation/articles/notification-hubs-ios-send-localized-breaking-news/

[Справочник по принципам использования мобильных служб Objective-C]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


<!--HONumber=42-->
