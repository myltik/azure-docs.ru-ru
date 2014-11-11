<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Уведомление пользователей посредством концентраторов уведомлений

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ru-ru/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
        <a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ. В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть ASP.NET WebAPI используется для проверки подлинности клиентов и создания уведомлений, как показано в разделе руководства [Регистрация из серверной части приложения][Регистрация из серверной части приложения]. Материал этого учебника основан на концентраторе уведомлений, созданном вами в учебнике **Приступая к работе с концентраторами уведомлений**.

Этот учебник также необходимо изучить перед обращением к учебнику **Безопасные push-уведомления**. После выполнения шагов учебника **Уведомление пользователей** можно приступать к работе с учебником **Безопасные push-уведомления**, в котором показано, как следует изменять код **Уведомления пользователей** для безопасной отправки push-уведомлений.

> [AZURE.NOTE] В этом учебнике подразумевается, что вы создали и настроили концентратор уведомлений в соответствии с описанием в учебнике [Приступая к работе с концентраторами уведомлений (iOS)][Приступая к работе с концентраторами уведомлений (iOS)].

## Создание и настройка концентратора уведомлений

Перед началом работы с этим учебником необходимо создать профиль подготовки и push-сертификат разработки, после чего следует создать концентратор уведомлений Azure и подключить его к этому приложению. Выполните шаги в учебнике [Приступая к работе с концентраторами уведомлений (iOS)][Приступая к работе с концентраторами уведомлений (iOS)], в частности, разделы с 1 по 5.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Изменение приложения iOS

1.  Выполните шаги в учебнике [Приступая к работе с концентраторами уведомлений (iOS)][Приступая к работе с концентраторами уведомлений (iOS)] в разделах с 1 по 5, чтобы создать приложение с одностраничное приложение просмотра для получения Push уведомлений от концентратора уведомлений.

> [AZURE.NOTE] В этом разделе подразумевается, что вы настроили свой проект, указав пустое имя организации. Если это не так, необходимо присоединить имя организации к именам всех классов в коде ниже.

1.  В вашем Main.Storyboard добавьте следующие компоненты из библиотеки объектов:

    -   UITextField с текстом заполнителя **Username**
    -   UITextField с текстом заполнителя **Пароль**; следует включить параметр **Защищенный текстовый ввод** в группе свойств TextField в области справа.
    -   UIButton с меткой **1. Выполнить вход** и отключите параметр **Включено** в группе свойств "Специальные возможности" справа
    -   UIButton с меткой **2. Отправить push-уведомление** и отключите параметр **Включено** в группе свойств "Специальные возможности" справа

    Раскадровка должна выглядеть следующим образом:

    ![][0]

2.  Создайте выходы для всех компонентов UITextField и UIButton в разделе интерфейса вашего ViewController.m

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  Создайте действия для обеих кнопок в реализации ViewController.m:

     -(IBAction)LogInAction:(id)sender { }
     -(IBAction)SendPushAction:(id)sender { }

4.  Сначала создается класс RegisterClient для взаимодействия с серверной частью. Создайте класс "Objective-C" с именем RegisrterClient, наследуемый из NSObject. Затем добавьте следующий код в раздел интерфейса RegisterClient.h:

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  В RegisterClient.m добавьте следующий раздел интерфейса:

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  Затем добавьте следующий код в раздел реализации и замените заполнитель *{backend endpoint}* конечной точкой, используемой для развертывания серверной части приложения в предыдущем разделе.

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

    В коде, указанном выше, реализуется логика, описываемая в статье [Регистрация из серверной части приложения][Регистрация из серверной части приложения], при этом используется NSURLSession для выполнения вызовов REST в серверную часть приложения, и NSUserDefaults для локального сохранения идентификатора registrationId, возвращаемого концентратором уведомлений.

    Обратите внимание, что для нормальной работы этого класса должно быть правильно задано свойство **authorizationHeader**. Это свойство может быть установлено классом **ViewController** после выполнения входа.

7.  В ViewController.h добавьте следующее свойство, которое будет содержать токен устройства:

        @property (strong, nonatomic) NSData* deviceToken;

8.  В ViewController.m создайте UITextFieldDelegate из класса ViewController, добавьте ссылку на экземпляр RegisterClient и добавьте объявление частного метода. Раздел интерфейса должен выглядеть следующим образом.

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    Частный метод будет использоваться для создания заголовка авторизации с целью выполнения базовой проверки подлинности в серверной части вашего приложения.

> [AZURE.NOTE] Это не схема не является безопасной проверкой подлинности; следует заменить реализацию **createAndSetAuthenticationHeaderWithUsername:AndPassword:** вашим собственным механизмом проверки подлинности, генерирующим маркер проверки подлинности, используемый классом клиента register, например, OAuth, Active Directory.

1.  Затем в разделе реализации ViewController.m добавьте следующий код:

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

    Обратите внимание на то, как при задании токена устройства включается кнопка входа. Это происходит по той причине, что контроллер представления, являясь частью действия входа, регистрирует push-уведомления в серверной части приложения. Таким образом, необходимо предотвратить нажатие пользователем кнопки входа до тех пор, пока система не будет правильно настроена. В приложении может потребоваться разъединить вход и push-регистрацию, если первая операция выполняется раньше второй.

2.  В ViewController.m добавьте константу конечной точки серверной части и заполните реализации методов действия для компонента UIButton. Обязательно замените заполнитель конечной точкой серверной части, которая использовалась в предыдущем разделе.

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

3.  Наконец, в **ViewDidLoad** введите следующий код, чтобы инициировать экземпляр RegisterClient и задать делегата для текстовых полей.

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  Теперь в **AppDelegate.m** удалите все содержимое метода **application:didRegisterForPushNotificationWithDeviceToken:** и замените его следующим:

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    Таким образом, контроллер представления будет содержать самый последний токен устройства, полученный от APNS.

5.  По-прежнему находясь в **AppDelegate.m**, убедитесь, что присутствует следующий метод:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## Запустите приложение

Для запуска приложения выполните следующие действия:

1.  Убедитесь, что в Azure выполнено развертывание **AppBackend**. Если используется Visual Studio, запустите приложение веб-API **AppBackend**. Отобразится веб-страница ASP.NET.

2.  В XCode запустите приложение на физическом устройстве под управлением iOS (push-уведомления не будут работать в симуляторе).

3.  В пользовательском интерфейсе приложения iOS введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми.

4.  В пользовательском интерфейсе приложения iOS щелкните **Вход**. Затем щелкните **Отправить push-уведомление**.

  [Регистрация из серверной части приложения]: http://msdn.microsoft.com/ru-ru/library/dn743807.aspx
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  [0]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
