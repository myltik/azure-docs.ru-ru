---
title: Безопасные push-уведомления посредством центров уведомлений Azure
description: Узнайте, как отправлять безопасные push-уведомления в приложение iOS из Azure. Примеры кода написаны на Objective-C и C#.
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: d3ba967a164a35af5bf66f7e74d5f95b5dc2a37f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776208"
---
# <a name="azure-notification-hubs-secure-push"></a>Безопасные push-уведомления посредством центров уведомлений Azure
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Обзор
Поддержка push-уведомлений в Microsoft Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ.

Из-за ограничений, связанных с правовыми нормами или обеспечением безопасности, иногда в уведомлении могут присутствовать данные, которые нельзя передать через стандартную инфраструктуру push-уведомлений. В этом учебнике рассказывается о том, как реализовать этот принцип при отправке важной информации через защищенное соединение с проверкой подлинности, установленное между устройством клиента и серверной частью приложения.

На высоком уровне поток можно представить следующим образом.

1. Серверная часть приложения:
   * Сохраняет полезную нагрузку в базе данных серверной части.
   * Отправляет идентификатор этого уведомления устройству (защищаемые сведения не передаются).
2. Приложение на устройстве при получении уведомления:
   * Устройство связывается с серверной частью и запрашивает полезную нагрузку.
   * Приложение может показывать полезную нагрузку в виде уведомления на устройстве.

Стоит отметить: в предыдущем потоке (и в этом учебнике) мы предположили, что устройство сохраняет маркер проверки подлинности в локальном хранилище после входа пользователя. Таким образом обеспечивается удобство работы, так как с помощью этого маркера устройство способно получать безопасные полезные данные уведомлений. Если приложение не сохраняет маркеры проверки подлинности на устройстве, или если истек срок действия маркеров, приложение устройства, после получения уведомления, должно отобразить общее уведомление, предлагая пользователю запустить приложение. Затем приложение выполняет проверку подлинности пользователя и отображает полезную нагрузку уведомления.

В этом учебнике по безопасности push-уведомлений показано, как безопасно отправлять push-уведомление. Данное руководство является продолжением другого учебника под названием [Уведомление пользователей](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , поэтому необходимо сначала выполнить шаги в указанном учебнике.

> [!NOTE]
> В этом учебнике подразумевается, что вы создали и настроили центр уведомлений, как описано в руководстве [Приступая к работе с центрами уведомлений (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Внесение изменений в проект iOS
После того, как вы изменили серверную часть приложения для отправки только *идентификатора* уведомления, необходимо внести изменения в приложение iOS для обработки этого уведомления и осуществления обратного вызова к серверной части для извлечения конфиденциального сообщения, которое должно быть отображено.

Для достижения этой цели мы напишем логику извлечения безопасного содержимого из серверной части приложения.

1. Убедитесь, что в **AppDelegate.m** приложение подписано на автоматические уведомления и обрабатывает отправленный из серверной части идентификатор уведомления. Добавьте параметр **UIRemoteNotificationTypeNewsstandContentAvailability** в didFinishLaunchingWithOptions:
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. В файле **AppDelegate.m** добавьте в начало файла реализационную часть со следующим объявлением:
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. Затем добавьте в реализационную часть следующий код, заменив заполнитель `{back-end endpoint}` на конечную точку серверной части, которая была получена ранее:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

1. Теперь мы обработаем входящее уведомление и используем вышеуказанный метод для извлечения содержимого и его отображения. Во-первых, мы должны создать для приложения iOS возможность работы в фоновом режиме при получении push-уведомления. В **XCode** выберите проект приложения в панели слева, затем выберите основную платформу в разделе **Targets** (Целевые объекты) центральной панели.
2. Затем щелкните вкладку **Capabilities** (Возможности) в верхней части центральной панели и установите флажок **Remote Notifications** (Удаленные уведомления).
   
    ![][IOS1]
3. В файле **AppDelegate.m** добавьте следующий метод для обработки push-уведомлений:
   
        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);
   
            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];
   
        }
   
    Обратите внимание, что случаи отсутствующего свойства заголовки проверки подлинности или отклонения предпочтительнее обрабатывать на серверной части. Обработка таких случаев в основном зависит от взаимодействия с пользователем на целевой платформе. Одним из вариантов является отображение уведомления с обычным предложением пользователю для проверки подлинности, чтобы получить текущее уведомление.

## <a name="run-the-application"></a>Запуск приложения
Для запуска приложения выполните следующие действия:

1. В XCode запустите приложение на физическом устройстве под управлением iOS (push-уведомления не будут работать в симуляторе).
2. В пользовательском интерфейсе приложения iOS введите имя пользователя и пароль. Это могут быть любые совпадающие строки.
3. В пользовательском интерфейсе приложения iOS нажмите **Вход**. Затем нажмите **Отправить push-уведомление**. Вы должны увидеть конфиденциальное уведомление, которое будет отображено в центре уведомлений.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
