<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!-- <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение iOS. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Apple (APNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Создание запроса подписи сертификата][Создание запроса подписи сертификата]
2.  [Регистрация приложения и включение push-уведомлений][Регистрация приложения и включение push-уведомлений]
3.  [Создание профиля подготовки для приложения][Создание профиля подготовки для приложения]
4.  [Локальная загрузка мобильной службы][Локальная загрузка мобильной службы]
5.  [Тестирование мобильной службы][Тестирование мобильной службы]
6.  [Обновление сервера для отправки push-уведомлений][Обновление сервера для отправки push-уведомлений]
7.  [Публикация мобильной службы в Azure][Публикация мобильной службы в Azure]
8.  [Добавление push-уведомлений в приложение][Добавление push-уведомлений в приложение]
9.  [Обновление скриптов для отправки push-уведомлений][Обновление скриптов для отправки push-уведомлений]
10. [Включение push-уведомлений для локального тестирования][Включение push-уведомлений для локального тестирования]
11. [Тестирование приложения с помощью опубликованной мобильной службы][Тестирование приложения с помощью опубликованной мобильной службы]

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

## <a name="download-the-service"></a><span class="short-header"> Загрузка службы</span>Загрузка службы на локальный компьютер

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">Тестирование службы</span>Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a> Обновление сервера для отправки push-уведомлений

1.  В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте файл TodoItemController.cs. Добавьте в начало файла следующие инструкции `using`:

        using System;
        using System.Collections.Generic;

2.  Обновите определение метода `PostTodoItem` с помощью следующего кода:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Этот код отправит push-уведомление (с текстом вставленного элемента) после вставки элемента списка дел. В случае возникновения ошибки код добавит запись в журнал ошибок, которую можно просмотреть на вкладке **Журналы** мобильной службы на портале управления.

## <a name="publish-the-service"></a><span class="short-header">Публикация службы</span>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

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

5.  В QSTodoListViewController.m импортируйте файл QSAppDelegate.h, чтобы было можно использовать делегат для получения маркера устройства:

        #import "QSAppDelegate.h"

6.  В QSTodoListViewController.m измените действие **(IBAction)onAdd**, найдя следующую строку:

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

Этот код добавляет ссылку на **QSAppDelegate** для получения маркера устройства, а затем изменяет полезные данные запроса для включения этого маркера устройства.

> [WACOM.NOTE] Этот код необходимо добавить перед вызовом метода **addItem**.

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## <span id="local-testing"></span></a> Включение push-уведомлений для локального тестирования

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## Тестирование push-уведомлений в приложении

1.  Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

    ![][0]

    > [WACOM.NOTE] Необходимо явным образом принимать push-уведомления из своего приложения. Этот запрос отображается только при первом запуске приложения.

2.  В приложении введите содержательный текст (например, *Новая задача для мобильных служб*) и щелкните знак "плюс" (**+**).

    ![][1]

3.  Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

    ![][2]

4.  Повторите шаг 2 и немедленно закройте приложение, а затем убедитесь, что отображается следующее push-уведомление.

    ![][3]

Вы успешно завершили ознакомление с данным учебником.

## Дальнейшие действия

В этом учебнике рассмотрены основы работы с приложением iOS для применения мобильных служб и концентраторов уведомлений с целью отправки push-уведомлений. Затем, следует ознакомиться со следующим учебником ([Отправка push-уведомлений пользователям, прошедшим проверку подлинности][Отправка push-уведомлений пользователям, прошедшим проверку подлинности]), где показано, как использовать теги для отправки push-уведомлений из мобильной службы только пользователю, прошедшему проверку подлинности.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

-   [Приступая к работе с данными][Приступая к работе с данными]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]
    Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

<!-- Anchors.  -->



  [Создание запроса подписи сертификата]: #certificates
  [Регистрация приложения и включение push-уведомлений]: #register
  [Создание профиля подготовки для приложения]: #profile
  [Локальная загрузка мобильной службы]: #download-the-service-locally
  [Тестирование мобильной службы]: #test-the-service
  [Обновление сервера для отправки push-уведомлений]: #update-server
  [Публикация мобильной службы в Azure]: #publish-mobile-service
  [Добавление push-уведомлений в приложение]: #add-push
  [Обновление скриптов для отправки push-уведомлений]: #update-scripts
  [Включение push-уведомлений для локального тестирования]: #local-testing
  [Тестирование приложения с помощью опубликованной мобильной службы]: #test-app
  [SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  [0]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Отправка push-уведомлений пользователям, прошедшим проверку подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
