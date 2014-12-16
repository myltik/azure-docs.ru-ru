<properties title="Azure Notification Hubs Secure Push" pageTitle="Концентраторы уведомлений Azure, безопасные push-уведомления" metaKeywords="push-уведомления Azure, концентраторы уведомлений Azure, безопасные push-уведомления" description="Learn how to send secure push notifications to an iOS app from Azure. Code samples written in Objective-C and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="yuaxu" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />

#Безопасные push-уведомления посредством концентраторов уведомлений Azure

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/ru-ru/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS" class="current">iOS</a>
		<a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

Поддержка push-уведомлений в Microsoft Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ.

Из-за ограничений, связанных с правовыми нормами или обеспечением безопасности, иногда в уведомлении могут присутствовать данные, которые нельзя передать через стандартную инфраструктуру push-уведомлений. В этом учебнике рассказывается о том, как реализовать этот принцип при отправке важной информации через защищенное соединение с проверкой подлинности, установленное между устройством клиента и серверной частью приложения.

На высоком уровне поток можно представить следующим образом.

1. Серверная часть приложения:
	- Сохраняет полезную нагрузку в базе данных серверной части.
	- Отправляет идентификатор этого уведомления устройству (защищаемые сведения не передаются).
2. Приложение на устройстве при получении уведомления:
	- Устройство связывается с серверной частью и запрашивает полезную нагрузку.
	- Приложение может показывать полезную нагрузку в виде уведомления на устройстве.

Важно отметить, что в предыдущем потоке (и в этом учебнике) подразумевается, что устройство сохраняет маркер проверки подлинности в локальном хранилище после входа пользователя. Это гарантирует совершенно беспрепятственную работу, так как устройство может получать полезную нагрузку с использованием этого маркера. Если приложение не сохраняет маркеры проверки подлинности на устройстве, или если истек срок действия маркеров, приложение устройства, после получения уведомления, должно отобразить общее уведомление, предлагая пользователю запустить приложение. Затем приложение выполняет проверку подлинности пользователя и отображает полезную нагрузку уведомления.

В этом учебнике по работе с безопасными push-уведомлениями показано, как безопасно отправлять push-уведомление. Данное руководство является продолжением другого учебника под названием **Уведомление пользователей**, поэтому необходимо сначала выполнить шаги в указанном учебнике.

> [AZURE.NOTE] В этом учебнике подразумевается, что вы создали и настроили концентратор уведомлений в соответствии с описанием в учебнике [Приступая к работе с концентраторами уведомлений (iOS)](http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-ios-get-started/).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Внесение изменений в проект iOS

После того как вы изменили серверную часть приложения для отправки только идентификатора уведомления, необходимо внести изменения в приложение iOS для обработки этого уведомления и осуществления обратного вызова к внутреннему компоненту для извлечения конфиденциального сообщения, которое должно быть отображено.

Для достижения этой цели мы напишем логику извлечения безопасного содержимого из серверной части приложения.

1. В файле **AppDelegate.m** убедитесь, что приложение зарегистрировано для автоматических уведомлений, чтобы оно обрабатывало идентификатор уведомления, отправленный из внутреннего компонента. Добавьте параметр **UIRemoteNotificationTypeNewsstandContentAvailability** в didFinishLaunchingWithOptions:

		[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. В начало файла **AppDelegate.m** добавьте реализационную часть со следующим объявлением:

		@interface AppDelegate ()
		- (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
		@end

3. Затем добавьте в реализационную часть следующий код, подставив вместо `{back-end endpoint}` конечную точку серверной части, которая была получена ранее:

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

4. Теперь мы обработаем входящее уведомление и используем вышеуказанный метод для извлечения содержимого и его отображения. Во-первых, мы должны создать для приложения iOS возможность работы в фоновом режиме при получении push-уведомления. В **XCode** выберите проект приложения на панели слева, затем выберите основное целевое приложение в разделе **Целевые объекты** центральной панели.

5. Затем щелкните вкладку **Возможности** в верхней части центральной панели и щелкните флажок **Удаленные уведомления**.

	![][IOS1]


6. В файле **AppDelegate.m** добавьте следующий метод для обработки push-уведомлений:

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

	Обратите внимание, что случаи отсутствующего или отклоненного свойства заголовков проверки подлинности предпочтительнее обрабатывать на серверном компоненте. Обработка в таких случаях в основном зависит от пользовательского опыта на целевой платформе. Один из таких вариантов - отображение уведомления с обычным предложением проверки подлинности для получения текущего уведомления.

## Запустите приложение

Для запуска приложения выполните следующие действия:

1. В XCode запустите приложение на физическом устройстве под управлением iOS (push-уведомления не будут работать в симуляторе).

2. В пользовательском интерфейсе приложения iOS введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми.

3. В пользовательском интерфейсе приложения iOS нажмите **Вход**. Затем нажмите кнопку **Отправить push-уведомление**. Вы должны увидеть конфиденциальное уведомление, которое будет отображено в центре уведомлений.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
