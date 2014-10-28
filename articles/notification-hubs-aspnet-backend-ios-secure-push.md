<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, secure push" description="Learn how to send secure push notifications to an iOS app from Azure. Code samples written in Objective-C and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Безопасные push-уведомления для концентраторов уведомлений Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ru-ru/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS" class="current">iOS</a>
        <a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ.

В связи с законодательными ограничениями или из соображений безопасности, приложениям иногда может потребоваться включить в уведомление информацию, которая не может быть передана через стандартную инфраструктуру push-уведомлений. В этом руководстве описываются действия для достижения того же результата, путем отправки конфиденциальной информации через безопасное подключение с проверкой подлинности между клиентским устройством и серверной частью приложения.

На верхнем уровне процесс выглядит так:

1.  Серверная часть приложения:

    -   Хранит конфиденциальную информацию в базе данных серверной части.
    -   Отправляет идентификаторы этой информации на устройство (конфиденциальная информация не пересылается).

2.  Приложение на устройстве при получении уведомления:

    -   Устройство связывается с серверной частью и запрашивает конфиденциальную информацию.
    -   Приложение может показать информацию в виде уведомления на устройстве.

Важно отметить, что в предыдущем процессе (и в учебнике), мы предполагаем, что устройство хранит маркер проверки подлинности в локальном хранилище после того, как пользователь выполнит вход. Это гарантирует полностью удобную работу, так как устройство сможет получить конфиденциальную информацию уведомления с помощью этого маркера. Если приложение не хранит маркеры проверки подлинности на устройстве, либо срок их действия истек, приложение при получении уведомления должно вывести стандартное уведомление с предложением пользователю запустить приложение. Затем приложение производит проверку подлинности пользователя и отображает конфиденциальную информацию.

В этом учебнике по безопасным push-уведомлениям показано, как отправлять push-уведомления безопасно. Учебник основан на учебнике **Уведомление пользователей**, поэтому вам необходимо сначала выполнить этапы указанного учебника.

> [AZURE.NOTE] В данном учебнике предполагается, что вы создали и настроили концентратор уведомлений, как описано в разделе [Приступая к работе с концентраторами уведомлений (iOS)][Приступая к работе с концентраторами уведомлений (iOS)].

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush][notification-hubs-aspnet-backend-securepush]]

## Внесение изменений в проект iOS

После того, как вы изменили серверную часть приложения для отправки только *идентификатора* уведомления, необходимо внести изменения в приложение iOS для обработки этого уведомления и осуществления обратного вызова к серверной части для извлечения конфиденциального сообщения, которое должно быть отображено.

Для достижения этой цели мы напишем логику извлечения безопасного содержимого из серверной части приложения.

1.  В файле **AppDelegate.m** добавьте в начало файла реализационную часть со следующим объявлением:

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

2.  Затем добавьте в реализационную часть следующий код, подставив вместо `{back-end endpoint}` конечную точку серверной части, которая была получена ранее:

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

    Этот метод вызывает серверную часть вашего приложения для извлечения содержимого уведомления с использованием учетных данных, хранящихся в общих настройках.

3.  Теперь мы обработаем входящее уведомление и используем вышеуказанный метод для извлечения содержимого и его отображения. Во-первых, мы должны создать для приложения iOS возможность работы в фоновом режиме при получении push-уведомления. В **XCode** выберите проект приложения в панели слева, затем выберите основную платформу в разделе **Targets (Платформы)** центральной панели.

4.  Затем щелкните вкладку **Capabilities (Возможности)** в верхней части центральной панели и щелкните флажок **Remote Notifications (удаленные уведомления)**.

    ![][]

5.  В файле **AppDelegate.m** добавьте следующий метод для обработки push-уведомлений:

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

    Обратите внимание, что случаи отсутствующего свойства заголовки проверки подлинности или отклонения предпочтительнее обрабатывать на серверной части. Обработка в таких случаях в основном зависит от пользовательского опыта на целевой платформе. Одним из вариантов является отображение уведомления с обычным предложением пользователю для проверки подлинности, чтобы получить текущее уведомление.

## Запуск приложения

Для запуска приложения выполните следующие действия:

1.  Убедитесь, что **AppBackend** развернут в Azure. При использовании Visual Studio, запустите приложение веб-API **AppBackend**. Отобразится веб-страница ASP.NET.

2.  В XCode запустите приложение на физическом устройстве iOS (push-уведомления не работают в симуляторе).

3.  В пользовательском интерфейсе приложения iOS введите имя пользователя и пароль. Это могут быть любые совпадающие строки.

4.  В пользовательском интерфейсе приложения iOS нажмите **Вход**. Затем нажмите **Отправить push-уведомление**. Вы должны увидеть конфиденциальное уведомление, которое будет отображено в центре уведомлений.

  [Windows Phone]: /ru-ru/documentation/articles/notification-hubs-windows-dotnet-secure-push/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/ "Android"
  [Приступая к работе с концентраторами уведомлений (iOS)]: http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-securepush]: ../includes/notification-hubs-aspnet-backend-securepush.md
  []: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
