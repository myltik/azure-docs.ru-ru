---
title: "Уведомление пользователей iOS через центры уведомлений с помощью серверной части .NET"
description: "Узнайте, как отправлять push-уведомления пользователям в Azure. Примеры кода написаны на Objective-C с использованием API .NET для серверной части."
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 0fa7a886e1ecb0a90b6aebc1dbf9ef0c6ce1acf1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs-notify-users-for-ios-with-net-backend"></a>Уведомление пользователей iOS через центры уведомлений с помощью серверной части .NET
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Обзор
Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ. В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть ASP.NET WebAPI используется для проверки подлинности клиентов и создания уведомлений, как показано в разделе руководства [Управление регистрацией из серверной части](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

> [!NOTE]
> В этом учебнике подразумевается, что вы создали и настроили центр уведомлений, как описано в руководстве [Приступая к работе с центрами уведомлений (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Это руководство также необходимо изучить перед переходом к руководству [Безопасные push-уведомления для концентраторов уведомлений Azure](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md).
> Если вы хотите использовать мобильные приложения в качестве внутренней службы, см. статью [Добавление push-уведомлений в приложение iOS](../app-service-mobile/app-service-mobile-ios-get-started-push.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Изменение приложения iOS
1. Откройте в одностраничном режиме просмотра приложение, которое вы создали с помощью руководства [Приступая к работе с центрами уведомлений (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) .
   
   > [!NOTE]
   > В этом разделе подразумевается, что вы настроили свой проект, указав пустое имя организации. Если вы этого не сделали, вам нужно будет добавлять имя своей организации перед всеми именами классов.
   > 
   > 
2. В своем Main.storyboard добавьте компоненты из библиотеки объектов, показанные на снимке экрана.
   
    ![][1]
   
   * **Имя пользователя**: текстовое поле UITextField с замещающим текстом *Enter Username*, расположенное под меткой отправки результатов, справа, слева и сверху ограниченное полями.
   * **Пароль**: текстовое поле UITextField с замещающим текстом *Enter Password*, расположенное под текстовым полем имени пользователя, справа, слева и сверху ограниченное полями. Отметьте параметр **Защищенный ввод текста** в инспекторе атрибутов в разделе *Символ вывода*.
   * **Вход**: кнопка UIButton с меткой, которая расположена под текстовым полем пароля. Снимите флажок **Включить** в инспекторе атрибутов в разделе *Управление содержимым*.
   * **WNS**: метка и переключатель для включения отправки уведомлений службы уведомлений Windows, если она установлена на концентраторе. См. руководство [Начало работы с центрами уведомлений для приложений универсальной платформы Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
   * **GCM**: метка и переключатель для включения отправки уведомлений в службу Google Cloud Messaging, если она установлена в концентраторе. См. руководство [Отправка push-уведомлений в приложения Android с помощью центров уведомлений Azure](notification-hubs-android-push-notification-google-gcm-get-started.md).
   * **APNS**: метка и переключатель для включения отправки уведомлений в службу уведомлений платформы Apple Platform Notification Service.
   * **Имя получателя**: текстовое поле UITextField с замещающим текстом *Введите имя получателя*, расположенное непосредственно под меткой GCM, справа, слева и сверху ограниченное полями.

    Подробнее о добавлении некоторых компонентов см. в руководстве [Отправка push-уведомлений с помощью центров уведомлений Azure в iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md).

1. **Ctrl** , перетащите компоненты из представления в файл ViewController.h и добавьте эти новые элементы.
   
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
        @property (weak, nonatomic) IBOutlet UITextField *NotificationField;
   
        // Used to enable the buttons on the UI
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;
   
        // Used to enabled sending notifications across platforms
        @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;
   
        - (IBAction)LogInAction:(id)sender;
2. В ViewController.h добавьте `#define` после операторов импорта. Замените заполнитель *<Enter Your Backend Endpoint>\>* URL-адресом назначения, который использовался для развертывания серверной части приложения в предыдущем разделе. Например, *http://you_backend.azurewebsites.net*.
   
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
3. В проекте создайте класс **Cocoa Touch class** с именем **RegisterClient** для взаимодействия с серверной частью ASP.NET. Создайте класс, наследующий `NSObject`. Затем добавьте следующий код в раздел RegisterClient.h:
   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
4. В RegisterClient.m обновите раздел `@interface` .
   
        @interface RegisterClient ()
   
        @property (strong, nonatomic) NSURLSession* session;
        @property (strong, nonatomic) NSURLSession* endpoint;
   
        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion;
        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;
   
        @end
5. Замените раздел `@implementation` RegisterClient.m следующим кодом.

        @implementation RegisterClient

        // Globals used by RegisterClient
        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

        -(instancetype) initWithEndpoint:(NSString*)Endpoint
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
                _endpoint = Endpoint;
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                    andCompletion:(void(^)(NSError*))completion
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description]
                stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                    uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
                completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                    tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
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
                        NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                    @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                                options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                                encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                    registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
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

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                        objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                    _endpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data
                        encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                        [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId
                        forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        @end

    В коде, указанном выше, реализуется логика, описываемая в статье [Регистрация из серверной части приложения](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) , при этом используется NSURLSession для выполнения вызовов REST в серверную часть приложения, и NSUserDefaults для локального сохранения идентификатора registrationId, возвращаемого концентратором уведомлений.

    Обратите внимание, что для нормальной работы этого класса должно быть правильно задано свойство **authorizationHeader** . Это свойство может быть установлено классом **ViewController** после выполнения входа.

1. В ViewController.h добавьте оператор `#import` для RegisterClient.h. Затем добавьте объявление для токена устройства и ссылку на экземпляр `RegisterClient` в разделе `@interface`.
   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
2. Добавьте в ViewController.m объявление частного метода в разделе `@interface` .
   
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end

> [!NOTE]
> Этот фрагмент не является безопасной схемой проверки подлинности. Следует заменить реализацию **createAndSetAuthenticationHeaderWithUsername:AndPassword:** своим собственным механизмом проверки подлинности, формирующим маркер проверки подлинности, используемый классом клиента register, например OAuth, Active Directory.
> 
> 

1. Затем в разделе `@implementation` ViewController.m добавьте приведенный ниже код, который задает токен и заголовок проверки подлинности устройства.
   
        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }
   
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];
   
            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];
   
            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                        encoding:NSUTF8StringEncoding];
        }
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
   
    Обратите внимание, как при задании токена устройства активируется кнопка входа. Это происходит по той причине, что контроллер представления, являясь частью действия входа, регистрирует push-уведомления в серверной части приложения. Таким образом, действие «Вход» должно быть недоступно, пока токен устройства не будет настроен должным образом. Можно разъединить вход и push-регистрацию, если первая операция выполняется раньше второй.
2. Чтобы в ViewController.m реализовать метод действия для кнопки **Вход** и метод отправки сообщений уведомления с помощью серверной части ASP.NET, используйте следующие фрагменты кода.
   
       - (IBAction) LogInAction: отправителя (id) {/ / создать заголовок проверки подлинности и установите его в клиент регистра NSString * имя пользователя = self. UsernameField.text;   Пароль NSString * = self. PasswordField.text;
   
           [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           __weak ViewController * selfie = self;   [self.registerClient registerWithDeviceToken:self.deviceToken теги: nil andCompletion:^(NSError* error) {Если (! ошибка) {dispatch_async(dispatch_get_main_queue(), ^ {selfie. SendNotificationButton.enabled = YES;               [self MessageBox:@"Success» message:@"Registered успешно! "];});}}];}

        - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag            Message:(NSString*)message {    NSURLSession* session = [NSURLSession        sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil        delegateQueue:nil];

            Передайте тег pns и имя пользователя в качестве параметров URL-адрес REST серверной части ASP.NET NSURL * requestURL = [NSURL URLWithString: [NSString stringWithFormat:@"%@/api/notifications? pns = % @& to_tag = % @», BACKEND_ENDPOINT pns, usernameTag]];

            Запрос NSMutableURLRequest * = [NSMutableURLRequest requestWithURL:requestURL];    [запрос setHTTPMethod:@"POST»];

            Получить макетов authenticationheader от клиента регистра NSString * authorizationHeaderValue = [NSString stringWithFormat:@"Basic % @», self.registerClient.authenticationHeader];    [запрос setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization»];

            Добавьте текст сообщения уведомления [запрос setValue:@"application/json;charset=utf-8» forHTTPHeaderField:@"Content-Type»];    [запросить setHTTPBody: [сообщений dataUsingEncoding:NSUTF8StringEncoding]];

            Выполнить API-интерфейса REST отправить уведомление на dataTask NSURLSessionDataTask серверной части ASP.NET * = [сеанса dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError  *Ошибка) {NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) ответа;        Если (ошибка || httpResponse.statusCode! = 200) {NSString* состояние = [stringWithFormat:@"Error NSString состояние для % @: % d\nError: %@\n», pns, httpResponse.statusCode, ошибка];            dispatch_async(dispatch_get_main_queue(), ^ {/ / добавление текста, поскольку все вызовы 3 PNS также могут иметь информацию, представление [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];            });            NSLog(status);        }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];    [dataTask resume]; }


1. Обновите действие для кнопки **Отправить уведомление** для использования серверной части ASP.NET и отправки любой PNS, разрешенной коммутатором.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            //[self SendNotificationRESTAPI];
            [self SendToEnabledPlatforms];
        }


        -(void)SendToEnabledPlatforms
        {
            NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

            [self.sendResults setText:@""];

            if ([self.WNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

            if ([self.GCMSwitch isOn])
                [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

            if ([self.APNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
        }



1. В функции **ViewDidLoad**введите следующий код, чтобы создать экземпляр RegisterClient и задать делегат для текстовых полей.
   
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
2. Теперь удалите в **AppDelegate.m** все содержимое метода **application:didRegisterForPushNotificationWithDeviceToken:** и замените его следующим содержимым, чтобы контроллер представления содержал последний токен устройства, полученный из элементов APN:
   
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
3. Наконец, убедитесь, что в **AppDelegate.m**есть следующий метод:
   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }

## <a name="test-the-application"></a>Тестирование приложения
1. В XCode запустите приложение на физическом устройстве под управлением iOS (push-уведомления не будут работать в симуляторе).
2. В пользовательском интерфейсе приложения iOS введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми. Затем нажмите кнопку **Log In**(Войти).
   
    ![][2]
3. Должно отобразиться всплывающее окно с сообщением об успешной регистрации. Нажмите кнопку **ОК**.
   
    ![][3]
4. В текстовом поле*Тег имени получателя* введите тег имени получателя, используемый при регистрации с другого устройства.
5. Введите сообщение уведомления и нажмите кнопку **Отправить уведомление**.  Сообщения уведомления могут получать только устройства с зарегистрированным тегом имени получателя.  Сообщения отправляются только таким пользователям.
   
    ![][4]

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
