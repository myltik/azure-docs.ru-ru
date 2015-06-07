<properties 
	pageTitle="Добавление push-уведомлений в приложение iOS с помощью службы приложений Azure" 
	description="Использование службы приложений Azure для отправки push-уведомлений в приложение iOS." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="ysxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# Добавление push-уведомлений в приложение iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

В этом разделе показано, как использовать службу приложений Azure для отправки push-уведомлений в приложение iOS с помощью службы push-уведомлений Apple (APNs). По завершении внутренний сервер .NET будет отправлять push-уведомление в приложение QuickStart ToDo при каждой вставке записи. Приложение совместимо с iOS 8 и предыдущими версиями.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание запроса на подписывание сертификата iOS]
2. [Регистрация приложения и включение push-уведомлений]
3. [Создание профиля подготовки для приложения]
4. [Настройка мобильного внутреннего сервера для отправки push-запросов]
5. [Обновление сервера для отправки push-уведомлений](#update-server)
6. [Публикация мобильного внутреннего сервера в Azure]
7. [Добавление push-уведомлений в приложение]
8. [Тестирование приложения]

Для работы с данным учебником требуется следующее:

+ [Пакет SDK iOS мобильного приложения Azure]
+ [Пакет NuGet концентраторов уведомлений Azure]
+ [XCode 6.0][Install Xcode]
+ Устройство с iOS 6.0 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS

   >[AZURE.NOTE]В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Этот учебник создан на основе краткого руководства по мобильным приложениям службы приложений. Прежде чем приступить к этому учебнику, необходимо сначала пройти учебник [Начало работы с мобильными приложениями службы приложений].

[AZURE.INCLUDE [Включение push-уведомлений Apple](../includes/enable-apple-push-notifications.md)]

## Настройка мобильного приложения для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

## <a id="update-server"></a>Обновление сервера для отправки push-уведомлений

1. В Visual Studio щелкните правой кнопкой мыши решение, а затем щелкните **Управление пакетами NuGet**.

2. Выполните поиск **Microsoft.Azure.NotificationHubs** и нажмите кнопку **Установить** для всех проектов в решении.

3. В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильного внутреннего сервера. Откройте файл TodoItemController.cs. Добавьте следующие операторы `using` в начало файла:

		using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Добавьте следующий фрагмент в метод `PostTodoItem` после вызова **InsertAsync**:

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    Этот код запрашивает в концентраторе уведомлений, связанном с этим мобильным приложением, отправку push-уведомления после вставки элемента задачи.


## <a name="publish-the-service"></a>Публикация мобильного внутреннего сервера в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Добавление push-уведомлений в приложение
1. Загрузите и добавьте ссылку на пакет SDK для клиента мобильного приложения службы приложений в Xcode.

2. В **QSAppDelegate.m** добавьте следующее в **application:didFinishLaunchingWithOptions**, чтобы зарегистрировать клиент в службе push-уведомлений Apple:

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && 	
        	[[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        } else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. В том же файле добавьте следующий метод обработчика в реализацию **QSAppDelegate**:

        // registration with APNs is successful
        - (void)application:(UIApplication *)application 
            didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. Затем добавьте метод обработчика сбоев в реализацию:

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. В QSAppDelegate.m добавьте следующий метод обработчика в реализации:

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application 
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" 
                                                            message:alertString 
                                                           delegate:nil 
                                                  cancelButtonTitle:@"OK" 
                                                  otherButtonTitles:nil];
            [alert show];
        }

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## Тестирование push-уведомлений в приложении

1. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

  	![][23]

    > [AZURE.NOTE]Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите содержательный текст (например, _Новая задача для мобильных служб_) и щелкните значок «плюс» (**+**).

  	![][24]

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

  	![][25]

4. Повторите шаг 2 и немедленно закройте приложение, а затем проверьте, что отображается следующее push-уведомление.

  	![][26]

Вы успешно завершили ознакомление с данным учебником.

<!-- Anchors.  -->
[Создание запроса на подписывание сертификата iOS]: #certificates
[Регистрация приложения и включение push-уведомлений]: #register
[Создание профиля подготовки для приложения]: #profile
[Добавление push-уведомлений в приложение]: #add-push
[Настройка мобильного внутреннего сервера для отправки push-запросов]: #configure
[Update the server to send push notifications]: #update-server
[Публикация мобильного внутреннего сервера в Azure]: #publish-mobile-service
[Тестирование приложения]: #test-the-service

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
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Пакет SDK iOS мобильного приложения Azure]: https://go.microsoft.com/fwLink/?LinkID=529823
[Пакет NuGet концентраторов уведомлений Azure]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=54-->