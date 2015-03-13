<properties 
	pageTitle="Приступая к работе с push-уведомлениями (iOS) | Центр мобильных разработок" 
	description="Узнайте, как использовать мобильные службы Azure для отправки push-уведомлений в приложение iOS." 
	services="mobile-services, notification-hubs" 
	documentationCenter="ios" 
	manager="dwrede" 
	editor="" 
	authors="krisragh"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="krisragh"/>


# Добавление push-уведомлений в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение iOS. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Apple (APNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.


В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание запроса подписи сертификата]
2. [Регистрация приложения и включение push-уведомлений]
3. [Создание профиля подготовки для приложения]
4. [Обновление сервера для отправки push-уведомлений](#update-server)
5. [Публикация мобильной службы в Azure]
6. [Добавление push-уведомлений в приложение]
7. [Включение push-уведомлений для локального тестирования](#local-testing)
8. [Тестирование приложения с помощью опубликованной мобильной службы]

Для работы с данным учебником требуется следующее:

+ [Пакет SDK для мобильных служб для iOS]
+ [XCode 4.5][Установка Xcode]
+ Устройство с iOS 6.0 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS

   > [AZURE.NOTE] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с данным учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами] или [Добавление мобильных служб к приложению][Приступая к работе с данными].


[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]


## Настройка мобильных служб для отправки push-запросов

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]


##<a id="update-server"></a>Обновление сервера для отправки push-уведомлений

1. В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте файл TodoItemController.cs. Добавьте следующие операторы  `using` в начало файла:


		using System;
		using System.Collections.Generic;

2. Обновите определение метода  `PostTodoItem` с помощью следующего кода:  

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


<h2><a name="publish-the-service"></a>Публикация мобильной службы в Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Добавление push-уведомлений в приложение

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

3. В QSAppDelegate.m добавьте следующий метод обработчика в реализации: Не забудьте скопировать значения URL-адреса мобильной службы и ключа приложения и включить их для заполнителей:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
            MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

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

   > [AZURE.NOTE] Этот код необходимо добавить перед вызовом метода <strong>addItem</strong>.

Ваше приложение теперь обновлено для поддержки push-уведомлений.

##<a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## Тестирование push-уведомлений в приложении

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

## Дальнейшие действия

В этом учебнике демонстрируются основы включения отправки push-уведомлений в мобильных службах и концентраторах уведомлений приложений iOS. Далее мы рекомендуем изучить учебник [Рассылка push-уведомлений проверенным пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только тем пользователям, которые прошли проверку подлинности.

<!--[Рассылка push-уведомлений проверенным пользователям]
	<br/>Дополнительная информация о том, как использовать теги для отправки push-уведомлений из мобильной службы только прошедшим проверку пользователям.

+ [Рассылка широковещательных уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

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

<!-- Anchors.  -->
[Создание запроса подписи сертификата]: #certificates
[Регистрация приложения и включение push-уведомлений]: #register
[Создание профиля подготовки для приложения]: #profile
[Настройка мобильных служб]: #configure
[Обновление скриптов для отправки push-уведомлений]: #update-scripts
[Добавление push-уведомлений в приложение]: #add-push
[Вставка данных для получения уведомлений]: #test
[Тестирование приложения с помощью опубликованной мобильной службы]: #test-app
[Дальнейшие действия]:#next-steps
[Локальная загрузка мобильной службы]: #download-the-service-locally
[Тестирование мобильной службы]: #test-the-service
[Публикация мобильной службы в Azure]: #publish-mobile-service

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

<!-- URLs. -->
[Установка Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Портал подготовки iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Пакет SDK для мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Портал управления Azure]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Рассылка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-ios-send-breaking-news/
[Рассылка уведомлений по шаблону подписчикам]: /ru-ru/documentation/articles/notification-hubs-ios-send-localized-breaking-news/


<!--HONumber=42-->
