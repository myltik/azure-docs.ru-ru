<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-ios" urlDisplayName="Уведомление пользователей приложения iOS с помощью мобильных служб" pageTitle="Регистрация текущего пользователя для push-уведомлений с помощью мобильной службы - Концентраторы уведомлений" metaKeywords="Регистрация приложения Azure, концентраторы уведомлений, push-уведомления Azure, push-уведомления приложения iOS" description="Узнайте, как запросить регистрацию push-уведомления в приложении iOS с помощью концентраторов уведомлений Azure, когда регистрация выполняется с помощью мобильных служб Azure." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Регистрация текущего пользователя для push-уведомлений с помощью мобильной службы" authors="" solutions="" manager="" editor="" />

# Регистрация текущего пользователя для push-уведомлений мобильной службы

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS" class="current">iOS</a>
</div>

В этом разделе показано, как запросить регистрацию push-уведомления с помощью концентраторов уведомлений Azure, когда регистрация выполняется с помощью мобильных служб Azure. Этот раздел расширяет учебник [Уведомление пользователей с помощью концентраторов уведомлений]. Чтобы создать прошедшую проверку подлинности мобильную службу, вы должны завершить требуемые действия в этом учебнике. Дополнительные сведения о сценарии уведомления пользователей см. в учебнике [Уведомление пользователей с помощью концентраторов уведомлений].  

1. В Xcode откройте файл QSTodoService.h в проекте, который создали при прохождении предыдущего необходимого учебника [Приступая к работе с проверкой подлинности], и добавьте следующее свойство **deviceToken**:

		@property (nonatomic) NSData* deviceToken;

 	В этом свойстве хранится маркер устройства.

2. В файле QSTodoService.m добавьте следующий метод **getDeviceTokenInHex**: 

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

	Этот метод преобразует маркер устройства в шестнадцатеричное строковое значение.

3. В файле QSAppDelegate.m добавьте следующие строки кода в метод **didFinishLaunchingWithOptions**:

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	Это приведет к включению push-уведомлений в приложении.

4. 	Добавьте следующий метод в файл QSAppDelegate.m: 
	
			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
			    [QSTodoService defaultService].deviceToken = deviceToken;
			}

	При этом будет обновлено свойство **deviceToken**.

	<div class="dev-callout"><b>Примечание.</b>
	<p>На этом этапе в методе не должно быть никакого другого кода. Если в методе **registerNativeWithDeviceToken** уже есть вызов, добавленный при прохождении учебника <a href="/ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios/" target="_blank">Приступая к работе с концентраторами уведомлений</a>, этот вызов нужно закомментировать или удалить.</p>
	</div>
	
5.  (Необязательно) В файле QSAppDelegate.m добавьте следующий метод обработчика: 
	
			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
			    NSLog(@"%@", userInfo);
			    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
			                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
			                          @"OK" otherButtonTitles:nil, nil];
			    [alert show];
			}

 	Этот метод отображает предупреждение в пользовательском интерфейсе, когда приложение получает уведомления во время работы.

6. В файле QSTodoListViewController.m добавьте метод **registerForNotificationsWithBackEnd**:

			- (void)registerForNotificationsWithBackEnd {    
			    NSString* json = [NSString  stringWithFormat:@"{\"platform\":\"ios\", \"deviceToken\":\"%@\"}", [self.todoService getDeviceTokenInHex] ];
			    
			    [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
			        if (error != nil) {
			            NSLog(@"Registration failed: %@", error);
			        } else {
			            // display UIAlert with successful login
			            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
			            [alert show];
			        }
			    }];
			}

	Этот метод создает полезные данные json, содержащие маркер устройства. Затем вызывается пользовательский интерфейс API в мобильной службе для регистрации уведомлений. Этот метод создает маркер устройства для push-уведомлений и отправляет его, вместе с типом устройства, методу настраиваемого интерфейса API, который создает регистрацию в концентраторах уведомлений. Этот метод настраиваемого интерфейса API был определен в учебнике [Уведомление пользователей с помощью концентраторов уведомлений].

7.	И, наконец, в методе **viewDidAppear** добавьте вызов этого нового метода **registerForNotificationsWithBackEnd** после того, как пользователь успешно пройдет проверку подлинности, как показано ниже:
	
			- (void)viewDidAppear:(BOOL)animated
			{
			    MSClient *client = self.todoService.client;
			
			    if (client.currentUser != nil) {
			        return;
			    }
			    
			    [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
			        [self refresh];
			        [self registerForNotificationsWithBackEnd];
			    }];
			}
	
	<div class="dev-callout"><b>Примечание.</b>
	<p>Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. Может потребоваться выполнять эту регистрацию в приложении только периодически, чтобы гарантировать, что регистрация актуальна.</p>
	</div>

Теперь, когда клиентское приложение было обновлено, вернитесь к учебнику [Уведомление пользователей с помощью концентраторов уведомлений] и обновите мобильную службу для отправки уведомлений с помощью концентраторов уведомлений.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios/

[Портал управления Azure]: https://manage.windowsazure.com/
[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios/

