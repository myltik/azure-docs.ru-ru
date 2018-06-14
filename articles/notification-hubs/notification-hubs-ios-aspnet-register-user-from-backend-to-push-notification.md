---
title: Регистрация текущего пользователя для push-уведомлений с помощью веб-API | Документация Майкрософт
description: Узнайте, как запросить регистрацию push-уведомления в приложении iOS с помощью центров уведомлений Azure, когда регистрации выполняется через веб-API ASP.NET.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c43c15131afb5fbf346b0137dac566f5331c65a2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776378"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Регистрация текущего пользователя для push-уведомлений с помощью ASP.NET
> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
> 
> 

## <a name="overview"></a>Обзор
В этом разделе рассказывается о том, как запросить регистрацию push-уведомлений с помощью концентраторов уведомлений Azure при выполнении регистрации средствами веб-API ASP.NET. Эта статья расширяет руководство [Уведомление пользователей с помощью концентраторов уведомлений]. Чтобы создать прошедшую проверку подлинности мобильную службу, вы должны завершить требуемые действия в этом учебнике. Дополнительные сведения о сценарии уведомления пользователей см. в руководстве [Уведомление пользователей с помощью концентраторов уведомлений].

## <a name="update-your-app"></a>Обновление приложения
1. В вашем MainStoryboard_iPhone.storyboard добавьте следующие компоненты из библиотеки объектов:
   
   * **Метка**: «Принудительно отправлять пользователю уведомления из центров уведомлений»
   * **Метка**: «InstallationId»
   * **Метка**: «Пользователь»
   * **Текстовое поле**: «Пользователь»
   * **Метка**: «Пароль»
   * **Текстовое поле**: «Пароль»
   * **Кнопка**: «Вход»
     
     На этом этапе раскадровка выглядит следующим образом:
     
      ![][0]
2. Во вспомогательном редакторе создайте выходы для всех коммутируемых элементов управления и вызовите их, соедините текстовые поля с контроллером представления (делегируйте) и создайте **Действие** для кнопки **Вход**.
   
       ![][1]
   
       Your BreakingNewsViewController.h file should now contain the following code:
   
        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;
   
        - (IBAction)login:(id)sender;
3. Создайте класс с именем **DeviceInfo**и скопируйте следующий код в раздел интерфейса файла DeviceInfo.h:
   
        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;
4. Скопируйте следующий код в реализационную часть файла DeviceInfo.m:
   
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
5. В PushToUserAppDelegate.h добавьте следующее одноэлементное свойство:
   
        @property (strong, nonatomic) DeviceInfo* deviceInfo;
6. В метод **didFinishLaunchingWithOptions** в файле PushToUserAppDelegate.m добавьте следующий код:
   
        self.deviceInfo = [[DeviceInfo alloc] init];
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
   
    Первая строка обеспечивает инициализацию одноэлементного **DeviceInfo** . Во второй строке начинается регистрация для получения push-уведомлений, которая уже существует, если вы уже изучили руководство [Приступая к работе с концентраторами уведомлений].
7. В PushToUserAppDelegate.m реализуйте метод **didRegisterForRemoteNotificationsWithDeviceToken** в своем AppDelegate и добавьте следующий код:
   
        self.deviceInfo.deviceToken = deviceToken;
   
    Таким образом задается маркер устройства для запроса.
   
   > [!NOTE]
   > На этом этапе в методе не должно быть никакого другого кода. Если в методе **registerNativeWithDeviceToken** уже есть вызов, добавленный при прохождении учебника [Приступая к работе с концентраторами уведомлений](/manage/services/notification-hubs/get-started-notification-hubs-ios/) , этот вызов нужно закомментировать или удалить.
   > 
   > 
8. В файле PushToUserAppDelegate.m добавьте следующий метод обработчика:
   
   * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {   NSLog(@"%@", userInfo);   UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:                         [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:                         @"OK" otherButtonTitles:nil, nil];   [alert show]; }
   
   Этот метод отображает предупреждение в пользовательском интерфейсе, когда приложение получает уведомления во время работы.
9. Откройте файл PushToUserViewController.m и верните клавиатуру в следующей реализации:
   
        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }
10. В методе **viewDidLoad** в файле PushToUserViewController.m инициализируйте метку installationId, как показано ниже.
    
         DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
         Self.installationId.text = deviceInfo.installationId;
11. В интерфейс файла PushToUserViewController.m добавьте следующие свойства:
    
        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;
12. Затем добавьте следующую реализацию:
    
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
13. Скопируйте следующий код в метод обработчика **login** , созданный с помощью XCode:
    
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
    
    Этот метод возвращает ИД установки и канал для push-уведомлений и отправляет его, вместе с типом устройства, прошедшему проверку подлинности методу веб-API, который создает регистрацию в концентраторах уведомлений. Этот веб-API был определен в учебнике [Уведомление пользователей с помощью концентраторов уведомлений].

Теперь, когда клиентское приложение было обновлено, вернитесь к учебнику [Уведомление пользователей с помощью концентраторов уведомлений] и обновите мобильную службу для отправки уведомлений с помощью концентраторов уведомлений.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Уведомление пользователей с помощью концентраторов уведомлений]: /manage/services/notification-hubs/notify-users-aspnet

[Приступая к работе с концентраторами уведомлений]: /manage/services/notification-hubs/get-started-notification-hubs-ios
