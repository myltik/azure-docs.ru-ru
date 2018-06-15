---
title: Отправка локализованных уведомлений на устройства iOS с помощью службы "Центры уведомлений Azure" | Документация Майкрософт
description: Узнайте, как отправлять локализованные уведомления на устройства iOS с помощью службы "Центры уведомлений Azure".
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b3d74086ee233da50138aff00d8da78aa0243a75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776504"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Руководство по отправке локализованных уведомлений на устройства iOS с помощью службы "Центры уведомлений Azure" 
> [!div class="op_single_selector"]
> * [C# в Магазине Windows](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

В этом руководстве показано, как использовать функцию [шаблонов](notification-hubs-templates-cross-platform-push-messages.md) службы "Центры уведомлений Azure" для рассылки уведомлений об экстренных новостях, локализованных для языка и устройства. В этом руководстве вы начнете с приложения iOS, созданного в руководстве [Использование концентраторов уведомлений для передачи экстренных новостей]. По завершении вы сможете регистрироваться для категорий, которые вас интересуют, указывать язык уведомлений и получать push-уведомления только для выбранных категорий на этом языке.

Этот сценарий состоит из двух частей:

* приложение iOS позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей;
* сервер рассылает уведомления, используя функции **тегов** и **шаблонов** службы "Центры уведомлений Azure".

В этом руководстве вы выполните следующие задачи:

> [!div class="checklist"]
> * обновление пользовательского интерфейса приложения;
> * сборка приложения iOS;
> * отправка локализованных шаблонных уведомлений из консольного приложения .NET;
> * отправка локализованных шаблонных уведомлений с устройства.


## <a name="overview"></a>Обзор
В руководстве [Использование концентраторов уведомлений для передачи экстренных новостей] вы создали приложение, которое использовало **теги** для подписки на уведомления для различных категорий новостей. Однако многие приложения ориентированы на несколько рынков и требуют локализации. Это означает, что само содержимое уведомлений должно быть локализовано и доставлено в правильный набор устройств. В этом руководстве демонстрируются способы использования **шаблонов** службы "Центры уведомлений", которые позволяют легко доставлять локализованные уведомления об экстренных новостях.

> [!NOTE]
> Один из способов отправки локализованных уведомлений — создание нескольких версий каждого тега. Например, для поддержки английского, французского и китайского языков понадобится три разных тега для мировых новостей: world_en, world_fr и world_ch. Затем необходимо отправить локализованную версию мировых новостей по каждому из этих тегов. В этом разделе мы используем шаблоны во избежание избыточного количества тегов и необходимости отправки нескольких сообщений.

В общих чертах, шаблоны представляют собой способ указать, как конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В нашем случае отправляется независимое от языка сообщение, которое содержит все поддерживаемые языки.

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Затем следует убедиться, что устройство зарегистрировано с использованием шаблона, который ссылается на нужное свойство. Например, приложение iOS, которое требуется зарегистрировать для французских новостей, использует следующую синтаксическую конструкцию для регистрации.

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Дополнительные сведения о шаблонах см. в статье [Шаблоны](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="prerequisites"></a>предварительным требованиям

- Выполните задания руководства [Отправка push-уведомлений на определенные устройства iOS с помощью инфраструктуры "Центры уведомлений Azure"](notification-hubs-ios-xplat-segmented-apns-push-notification.md) и подготовьте код, так как он используется в текущем руководстве.
- Наличие Visual Studio 2012 или более поздней версии не обязательно.

## <a name="update-the-app-user-interface"></a>обновление пользовательского интерфейса приложения;
В этом разделе вы измените приложение Breaking News, созданное в разделе [Использование концентраторов уведомлений для передачи экстренных новостей] для отправки локализованных экстренных новостей с помощью шаблонов.

В файл MainStoryboard_iPhone.storyboard добавьте Segmented Control для трех языков: английского, французского и китайского (мандаринский диалект).

![][13]

Затем обязательно добавьте IBOutlet в свой файл ViewController.h, как показано на рисунке ниже.

![][14]

## <a name="build-the-ios-app"></a>Выполнение сборки приложения iOS
1. Добавьте в файл Notification.h метод *retrieveLocale* и измените методы store и subscribe, как показано в коде ниже.
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    Измените в файле Notification.m метод *storeCategoriesAndSubscribe* , добавив параметр языка и сохранив его в пользовательских параметрах по умолчанию:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    Затем измените метод *subscribe* , чтобы включить языковой стандарт:
   
    ```obj-c
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
       ```
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. Теперь, когда вы внесли изменения в класс Notifications, необходимо убедиться в том, что ViewController использует новый UISegmentControl. Добавьте следующую строку в метод *viewDidLoad* , чтобы убедиться в том, что отображается языковой стандарт, выбранный в данный момент:
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    Затем в методе *subscribe* замените свой вызов *storeCategoriesAndSubscribe* следующим кодом.
   
    ```obj-c
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
    ```
3. И наконец, необходимо обновить метод *didRegisterForRemoteNotificationsWithDeviceToken* в методе AppDelegate.m, чтобы правильно обновить регистрацию при запуске приложения. Замените вызов метода уведомления *subscribe* следующим кодом.
   
    ```obj-c
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(Необязательно.) Отправка локализованных шаблонных уведомлений из консольного приложения .NET
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(Необязательно.) Отправка локализованных шаблонных уведомлений с устройства
Предположим, у вас нет доступа к Visual Studio или вы просто хотите проверить отправку локализованных шаблонных уведомлений непосредственно из приложения на устройстве. Вы можете добавить параметры локализованных шаблонов в метод `SendNotificationRESTAPI`, определенный в предыдущем руководстве.

    ```obj-c
        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }
    ```


## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы отправили локализованные уведомления на устройства iOS. Чтобы узнать, как отправлять push-уведомления конкретным пользователям приложений iOS, перейдите к следующему руководству. 

> [!div class="nextstepaction"]
>[Отправка push-уведомлений определенным пользователям](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
