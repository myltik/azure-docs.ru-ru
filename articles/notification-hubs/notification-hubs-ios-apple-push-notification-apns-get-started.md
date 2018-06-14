---
title: Отправка push-уведомлений в приложения iOS с помощью Центров уведомлений Azure | Документация Майкрософт
description: Из этого руководства вы узнаете, как отправлять push-уведомления в приложения iOS с помощью центров уведомлений Azure.
services: notification-hubs
documentationcenter: ios
keywords: push-уведомление, push-уведомления, push-уведомления node.js, push-уведомления ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 083b0c956055ab5b54a4af2eec57f096613cbe65
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778224"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Руководство по отправке push-уведомлений в приложения iOS с помощью Центров уведомлений Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

При работе с этим руководством вы отправите push-уведомления в приложения iOS с помощью Центров уведомлений Azure. Вы создадите пустое приложение iOS, получающее push-уведомления с помощью [Службы push-уведомлений Apple (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

В этом руководстве вы выполните следующие задачи:

> [!div class="checklist"]
> * создание файла запроса на подпись сертификата;
> * регистрация приложения для работы с push-уведомлениями;
> * Создание профиля подготовки для приложения
> * настройка push-уведомлений iOS в центре уведомлений;
> * подключение приложения iOS к центрам уведомлений;
> * Отправка тестовых push-уведомлений
> * проверка получения уведомлений приложением.

Полный код для этого учебника можно найти на портале [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>предварительным требованиям

- Активная учетная запись Azure. Если ее нет, можно создать [бесплатную пробную учетную запись](https://azure.microsoft.com/free) всего за несколько минут. 
- [платформу обмена сообщениями Microsoft Azure]
- последняя версия [Xcode]
- устройство с iOS 10 (или более поздней версии);
- [программе для разработчиков на платформе Apple](https://developer.apple.com/programs/) .
  
  > [!NOTE]
  > В соответствии с требованиями к настройкам push-уведомлений необходимо развернуть push-уведомления и протестировать их на физическом устройстве под управлением iOS (iPhone или iPad), а не в эмуляторе iOS.
  
Изучение этого руководства является необходимым условием для работы со всеми другими руководствами, посвященными Центрам уведомлений для приложений iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Настройка push-уведомлений iOS в центре уведомлений 
В этом разделе вы создадите центр уведомлений и настроите аутентификацию с использованием службы APNS и раннее созданного сертификата push-уведомлений (файл с расширением **.p12**). Если вы хотите использовать уже созданный центр уведомлений, перейдите к шагу 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-your-notification-hub-with-apns-information"></a>Добавление данных о APNS в центр уведомлений

1. В разделе **Notification Services** выберите **Apple (APNS)**. 
2. Выберите **Сертификат**.
3. Щелкните **значок файла**.
4. Выберите экспортированный ранее файл с расширением **.p12**.
5. Укажите **пароль**.
6. Выберите режим **Песочница**. Используйте **рабочую среду**, только если push-уведомления нужно отправлять пользователям, выполнившим покупку приложения в магазине.

    ![Настройка сертификации APNS на портале Azure][7]

Вы настроили центр для работы со службой APNS, и у вас есть строки подключения, с помощью которых вы можете зарегистрировать свое приложение и отправлять push-уведомления.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Подключение приложения iOS к центрам уведомлений
1. В XCode создайте новый проект iOS и выберите шаблон **Single View Application** (Приложение с одним представлением).
   
    ![Xcode — приложение с одним представлением][8]
    
2. При настройке параметров нового проекта используйте те же **имя продукта** и **идентификатор организации**, которые вы использовали при указании идентификатора пакета на портале разработчиков Apple.
   
    ![Xcode — параметры проекта][11]
    
3. В навигаторе проекта выберите вкладку **General** (Общие) и найдите раздел **Signing** (Подписывание). Обязательно выберите соответствующую команду для учетной записи разработчика Apple. Среда XCode должна автоматически извлечь профиль подготовки, который вы создали ранее, используя ваш идентификатор пакета.
   
    Если новый профиль подготовки, созданный в Xcode, не отображается, обновите профили для идентификатора подписи. В строке меню щелкните **Xcode** выберите **Preferences** (Настройки), откройте вкладку **Account** (Учетная запись), нажмите кнопку **View Details** (Просмотреть сведения), щелкните свой идентификатор подписи, а затем нажмите кнопку Refresh (Обновить) в нижнем правом углу.

    ![Xcode — профиль подготовки][9]

4. Выберите вкладку **Capabilities** (Возможности) и активируйте параметр "Push Notifications" (Push-уведомления).

    ![Xcode — возможности push-уведомлений][12]
   
5. Загрузите [платформу обмена сообщениями Microsoft Azure] и распакуйте файлы. В XCode щелкните проект правой кнопкой мыши и выберите параметр **Add Files to** (Добавить файлы в), чтобы добавить папку **WindowsAzureMessaging.framework** в проект XCode. Нажмите кнопку **Options** (Параметры), установите флажок **Copy items if needed** (Копировать элементы при необходимости) и нажмите кнопку **Add** (Добавить).

    ![Распаковка пакета SDK Azure][10]

6. Добавьте новый файл заголовка в проект с именем **HubInfo.h**. Этот файл содержит константы для центра уведомлений. Добавьте следующие определения и замените буквенные заполнители строк *именем центра* и значением *DefaultListenSharedAccessSignature*, записанным ранее.

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. Откройте файл **AppDelegate.h** и добавьте следующие директивы импорта:

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. В файле **AppDelegate.m** добавьте следующий код в метод **didFinishLaunchingWithOptions** с учетом вашей версии iOS. Этот код регистрирует маркер вашего устройства в APNs.

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. В этом же файле добавьте следующие методы:

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    Этот код подключается к центру уведомлений, используя сведения о соединении, указанные в проекте HubInfo.h. Затем он передает маркер устройства в центр уведомлений, чтобы центр мог отправлять уведомления.

10. В том же файле добавьте следующий метод для отображения **UIAlert** , если уведомление получено, когда приложение активно:

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. Выполните сборку приложения и запустите его на устройстве, чтобы убедиться в отсутствии сбоев.

## <a name="send-test-push-notifications"></a>Отправка тестовых push-уведомлений
Можно проверить, поступают ли в приложение уведомления, с помощью параметра *Тестовая отправка* на [портале Azure]. Этот параметр позволяет отправить на устройство тестовое push-уведомление.

![Портал Azure — тестовая отправка][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="verify-that-your-app-receives-push-notifications"></a>Проверка получения push-уведомлений приложением
Для тестирования push-уведомлений в iOS необходимо развернуть приложение на физическом устройстве под управлением iOS. Отправка push-уведомлений Apple через эмулятор iOS невозможна.

1. Запустите приложение и убедитесь, что оно успешно зарегистрировано, а затем нажмите кнопку **ОК**.
   
    ![Проверка регистрации push-уведомления приложения iOS][33]
2. Отправьте тестовое push-уведомление с [портале Azure], как описано в предыдущем разделе. 

3. Push-уведомление будет отправлено на все устройства, зарегистрированные для получения уведомлений от того или иного центра уведомлений.
   
    ![Проверка получения push-уведомления приложения iOS][35]

## <a name="next-steps"></a>Дополнительная информация
В этом простом примере мы отправили push-уведомления на все зарегистрированные устройства iOS. Сведения о том, как отправлять push-уведомления на конкретные устройства iOS, см. в следующем руководстве. 

> [!div class="nextstepaction"]
>[Отправка push-уведомлений на конкретные устройства](notification-hubs-ios-xplat-segmented-apns-push-notification.md)


<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[платформу обмена сообщениями Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[портале Azure]: https://portal.azure.com
