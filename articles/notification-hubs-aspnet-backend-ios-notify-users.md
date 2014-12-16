<properties title="Azure Notification Hubs Notify Users" pageTitle="Концентраторы уведомлений Azure уведомляют пользователей" metaKeywords="push-уведомления Azure, концентраторы уведомлений Azure" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="yuaxu" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />

#Уведомление пользователей посредством концентраторов уведомлений

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/ru-ru/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для отдельных пользователей, так и для корпоративных приложений для мобильных платформ. В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть ASP.NET WebAPI используется для проверки подлинности клиентов и создания уведомлений, как показано в разделе руководства [Регистрация из серверной части приложения](http://msdn.microsoft.com/ru-ru/library/dn743807.aspx).

> [AZURE.NOTE] В этом учебнике подразумевается, что вы создали и настроили концентратор уведомлений в соответствии с описанием в учебнике [Приступая к работе с концентраторами уведомлений (iOS)](http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-ios-get-started/). Этот учебник также необходимо изучить перед обращением к учебнику [Безопасные push-уведомления](http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/).


## Создание и настройка концентратора уведомлений

Выполните действия, описанные в разделах с 1 по 5 учебника [Приступая к работе с концентраторами уведомлений (iOS)](http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-ios-get-started/). Дополнительные материалы о подготовке устройств iOS см. в блоге [Big Nerd Ranch](http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html)..

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Изменение приложения iOS

1. Откройте приложение Single Page View, созданное вами в соответствии с инструкциями в разделах с 1 по 5 учебника [Приступая к работе с концентраторами уведомлений (iOS)](http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-ios-get-started/).

> [AZURE.NOTE] В этом разделе подразумевается, что вы настроили свой проект, указав пустое имя организации. В противном случае нужно добавить название организации в начало имени для всех классов.

2. В вашем Main.Storyboard добавьте следующие компоненты из библиотеки объектов:
	+ UITextField с текстом-заполнителем **Имя пользователя**
	+ UITextField с текстом-заполнителем**Пароль**, и установите флажок  **Безопасное** в инспекторе атрибутов, под текстовым полем "Ключ возврата"
	+ UIButton с меткой **1. Вход**, и снимите флажок **Включить** в инспекторе атрибутов под "Проверка, затем контент"
	+ UIButton с меткой **2. Отправить уведомление** и снимите флажок **Включен**

	Раскадровка должна выглядеть следующим образом:

    ![][IOS1]

3. Создайте выходы для всех компонентов UITextField и UIButton в разделе интерфейса вашего ViewController.m

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. Сначала создается класс RegisterClient для взаимодействия с серверной частью. Создайте класс "Objective-C" с именем RegisrterClient, наследуемый из NSObject. Затем добавьте следующий код в раздел интерфейса RegisterClient.h:

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. В RegisterClient.m добавьте следующий раздел интерфейса:

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. Добавьте следующий код в раздел реализации и замените заполнитель {backend endpoint} на "URL-адрес назначения", использованный вами для развертывания серверной части приложения в предыдущем разделе.

		NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
		NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

		- (instancetype)init
		{
		    self = [super init];
		    if (self) {
		        NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
		        _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
		    }
		    return self;
		}

		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
		{
		    [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
		}

		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		{
		    NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

		    NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
		    deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

		    [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
		        NSLog(@"regId: %@", registrationId);
		        if (error) {
		            completion(error);
		            return;
		        }

		        [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
		            if (error) {
		                completion(error);
		                return;
		            }

		            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
		            if (httpResponse.statusCode == 200) {
		                completion(nil);
		            } else if (httpResponse.statusCode == 410 && retry) {
		                [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
		            } else {
		                NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

		                completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
		            }

		        }];
		    }];
		}

		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
		{
		    NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
		    NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

		    NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

		    NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
		    NSURL* requestURL = [NSURL URLWithString:endpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"PUT"];
		    [request setHTTPBody:jsonData];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
		    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        if (!error)
		        {
		            completion(response, error);
		        }
		        else
		        {
		            NSLog(@"Error request: %@", error);
		            completion(nil, error);
		        }
		    }];
		    [dataTask resume];
		}

		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		{
		    NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

		    if (registrationId)
		    {
		        completion(registrationId, nil);
		        return;
		    }

		    // request new one & save
		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

		            // remove quotes
		            registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

		            [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
		            [[NSUserDefaults standardUserDefaults] synchronize];

		            completion(registrationId, nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}

	В коде, указанном выше, реализуется логика, описываемая в статье [Регистрация из серверной части приложения](http://msdn.microsoft.com/ru-ru/library/dn743807.aspx), при этом используется NSURLSession для выполнения вызовов REST в серверную часть приложения, и NSUserDefaults для локального сохранения идентификатора registrationId, возвращаемого концентратором уведомлений.

	Обратите внимание, что для нормальной работы этого класса должно быть задано свойство **authorizationHeader**. Это свойство задается классом **ViewController** после выполнения входа.

7. В файле ViewController.h добавьте следующее объявление для ссылки на экземпляр RegisterClient и маркер устройства:

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. В ViewController.m превратите класс ViewController в UITextFieldDelegate. Затем добавьте объявление закрытого метода:

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] Следующий фрагмент кода не является безопасной схемой проверки подлинности, следует заменить реализацию **createAndSetAuthenticationHeaderWithUsername:AndPassword:** на ваш механизм проверки подлинности, который создает маркер проверки подлинности, используемый классом регистрации клиентов, например для OAuth, Active Directory.

9. Затем в разделе реализации ViewController.m добавьте следующий код:

		-(void) setDeviceToken: (NSData*) deviceToken
		{
		    _deviceToken = deviceToken;
		    self.LogInButton.enabled = YES;
		}

		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
		{
		    NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

		    NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

		    self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
		}

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
		    [textField resignFirstResponder];
		    return YES;
		}

	Обратите внимание, как при задании токена устройства активируется кнопка входа. Это происходит по той причине, что контроллер представления, являясь частью действия входа, регистрирует push-уведомления в серверной части приложения. Так действие "Log In" недоступно, пока не будет правильно настроен маркер устройства. Вы можете отделить выполнение входа от регистрации push-уведомления, если первое действие происходит раньше второго.

10. В ViewController.m добавьте константу конечной точки серверной части и используйте следующие фрагменты кода для реализации методов действия для своих кнопок UIButton. Замените подстановочную конечную точку на URL-адрес назначения, используемый для вашей серверной части.

		- (IBAction)LogInAction:(id)sender {
		    // create authentication header and set it in register client
		    NSString* username = self.UsernameField.text;
		    NSString* password = self.PasswordField.text;

		    [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

		    __weak ViewController* selfie = self;
		    [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
		        if (!error) {
		            dispatch_async(dispatch_get_main_queue(), ^{
		                selfie.SendPushButton.enabled = YES;

		                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
		                                      @"Registered successfully!" delegate:nil cancelButtonTitle:
		                                      @"OK" otherButtonTitles:nil, nil];
		                [alert show];
		            });
		        }
		    }];
		}

		NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

		- (IBAction)SendPushAction:(id)sender {
		    NSURLSession* session = [NSURLSession
		                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
		                             delegate:nil
		                             delegateQueue:nil];


		    NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
		        }
		    }];
		    [dataTask resume];
		}

11. В функции **ViewDidLoad** введите следующий код, чтобы инициировать экземпляр RegisterClient и задать делегата для текстовых полей.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. Теперь в файле **AppDelegate.m** удалите все содержимое метода **application:didRegisterForPushNotificationWithDeviceToken:** и замените его на следующий код, чтобы контроллер представления имел последний маркер устройства, полученный из APN:

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. И наконец, проверьте наличие в файле**AppDelegate.m** метода:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## Запустите приложение

1. В XCode запустите приложение на физическом устройстве под управлением iOS (push-уведомления не будут работать в симуляторе).

2. В пользовательском интерфейсе приложения iOS введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми. Затем нажмите кнопку **Вход**..

3. Появится всплывающее окно с сообщением об успехе регистрации. Нажмите кнопку **ОК**.

4. Нажмите кнопку **Отправить уведомление** и нажмите на устройстве кнопку "home". Вскоре должно появиться push-уведомление.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
