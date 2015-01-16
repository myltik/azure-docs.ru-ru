
<properties urlDisplayName="Notify iOS app users by using Web API" pageTitle="Регистрация текущего пользователя для push-уведомлений с помощью веб-API - Концентраторы уведомлений" metaKeywords="регистрация приложений Azure, концентраторы уведомлений, push-уведомления Azure, push-уведомления в приложениях iOS" description="Узнайте, как запросить регистрацию push-уведомления в приложении iOS с помощью центров уведомлений Azure, когда регистрации выполняется через веб-API ASP.NET." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="yuaxu" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />
# Регистрация текущего пользователя для push-уведомлений с помощью ASP.NET

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="Windows Store C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS" class="current">iOS</a>
</div>

В этом разделе рассказывается о том, как запросить регистрацию push-уведомлений с помощью концентратора уведомлений Azure при выполнении регистрации средствами веб-API для ASP.NET. Этот раздел расширяет учебник [Уведомление пользователей с помощью концентраторов уведомлений]. Чтобы создать прошедшую проверку подлинности мобильную службу, вы должны завершить требуемые действия в этом учебнике. Дополнительные сведения о сценарии уведомления пользователей см. в учебнике [Уведомление пользователей с помощью концентраторов уведомлений].  

1. В вашем MainStoryboard_iPhone.storyboard добавьте следующие компоненты из библиотеки объектов:

	+ **Метка** "Принудительно отправлять пользователю уведомления от концентраторов"
	+ **Метка** "InstallationId"
	+ **Метка** "Пользователь"
	+ **Текстовое поле** "Пользователь"
	+ **Метка** "Пароль"
	+ **Текстовое поле** "Пароль"
	+ **Кнопка** "Login"

	На этом этапе раскадровка выглядит следующим образом:

   	![][0]

2. Создайте в редакторе помощника точки подключения и дайте им названия, подключите текстовые поля с помощью контроллера представления (делегат) и создайте **действие** для кнопки **входа**.

   	![][1]

   	Теперь файл BreakingNewsViewController.h должен содержать следующий код:

		@property (weak, nonatomic) IBOutlet UILabel *installationId;
		@property (weak, nonatomic) IBOutlet UITextField *User;
		@property (weak, nonatomic) IBOutlet UITextField *Password;

		- (IBAction)login:(id)sender;

5. Создайте класс под названием **DeviceInfo** и скопируйте следующий код в раздел интерфейса файла DeviceInfo.h:

		@property (readonly, nonatomic) NSString* installationId;
		@property (nonatomic) NSData* deviceToken;

6. Скопируйте следующий код в реализационную часть файла DeviceInfo.m:

			@synthesize installationId = _installationId;

			- (id)init {
			    if (!(self = [super init]))
					return nil;

			    NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
			    _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
			    if(!_installationId) {
			        CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
			        _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
			        CFRelease(newUUID);

			        //store the install ID so we don't generate a new one next time
			        [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
			        [defaults synchronize];
			    }

			    return self;
			}

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

7. В PushToUserAppDelegate.h добавьте следующее одноэлементное свойство:

		@property (strong, nonatomic) DeviceInfo* deviceInfo;

8. Добавьте следующий код в метод **didFinishLaunchingWithOptions** в файле PushToUserAppDelegate.m:

		self.deviceInfo = [[DeviceInfo alloc] init];

		[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	В первой строке инициализируйте одноэлементный объект **DeviceInfo**. Во второй строке начинается регистрация для push-уведомлений, которая у вас уже есть, если вы закончили изучение учебника [Начало работы с концентраторами уведомлений].

9. В файле PushToUserAppDelegate.m реализуйте метод **didRegisterForRemoteNotificationsWithDeviceToken** в AppDelegate и добавьте следующий код:

		self.deviceInfo.deviceToken = deviceToken;

	Таким образом задается маркер устройства для запроса.

	<div class="dev-callout"><b>Примечание.</b>
	<p>На этом этапе в методе не должно быть никакого другого кода. Если в методе **registerNativeWithDeviceToken** уже есть вызов, добавленный при прохождении учебника <a href="/ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios/" target="_blank">Приступая к работе с концентраторами уведомлений</a>, этот вызов нужно закомментировать или удалить.</p>
	</div>

10.	В файле PushToUserAppDelegate.m добавьте следующий метод обработчика:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

	 Этот метод отображает предупреждение в пользовательском интерфейсе, когда приложение получает уведомления во время работы.

9. Откройте файл PushToUserViewController.m и верните клавиатуру в следующей реализации:

		- (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
		    if (theTextField == self.User || theTextField == self.Password) {
		        [theTextField resignFirstResponder];
		    }
		    return YES;
		}

9. В методе **viewDidLoad** в файле PushToUserViewController.m file инициализируйте метку installationId следующим образом:

		DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
		Self.installationId.text = deviceInfo.installationId;

10. В интерфейс файла PushToUserViewController.m добавьте следующие свойства:

		@property (readonly) NSOperationQueue* downloadQueue;
		- (NSString*)base64forData:(NSData*)theData;

11. Затем добавьте следующую реализацию:

			- (NSOperationQueue *)downloadQueue {
			    if (!_downloadQueue) {
			        _downloadQueue = [[NSOperationQueue alloc] init];
			        _downloadQueue.name = @"Download Queue";
			        _downloadQueue.maxConcurrentOperationCount = 1;
			    }
			    return _downloadQueue;
			}

			// base64 encoding
			- (NSString*)base64forData:(NSData*)theData
			{
			    const uint8_t* input = (const uint8_t*)[theData bytes];
			    NSInteger length = [theData length];

			    static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

			    NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
			    uint8_t* output = (uint8_t*)data.mutableBytes;

			    NSInteger i;
			    for (i=0; i < length; i += 3) {
			        NSInteger value = 0;
			        NSInteger j;
			        for (j = i; j < (i + 3); j++) {
			            value <<= 8;

			            if (j < length) {
			                value |= (0xFF & input[j]);
			            }
			        }

			        NSInteger theIndex = (i / 3) * 4;
			        output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
			        output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
			        output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
			        output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
			    }

			    return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
			}


12. Скопируйте следующий код в метод обработчика **входа в систему**, созданный XCode:

			DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

		    // build JSON
		    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

		    // build auth string
		    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
		    [request setHTTPMethod:@"POST"];
		    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
		    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
		    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
		    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

		    // connect with POST
		    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
		        // add UIAlert depending on response.
		        if (error != nil) {
		            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
		            if ([httpResponse statusCode] == 200) {
		                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		                [alert show];
		            } else {
		                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
		            }
		        } else {
		            NSLog(@"error: %@", error);
		        }
		    }];

	Этот метод получает идентификатор установки и канал для push-уведомлений, а затем отправляет их вместе с типом устройства прошедшему проверку методу веб-API, который создает регистрацию на концентраторах уведомлений. Этот веб-API был определен в разделе [Уведомление пользователей с помощью концентраторов уведомлений].

Теперь, когда клиентское приложение обновлено, вернитесь к учебнику [Уведомление пользователей с помощью концентраторов уведомлений] и обновите мобильную службу для отправки уведомлений с помощью концентраторов уведомлений.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet

[Портал управления Azure]: https://manage.windowsazure.com/
[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios
