---
title: "Начало работы с Центрами уведомлений Azure для приложений iOS | Документация Майкрософт"
description: "Из этого руководства вы узнаете, как отправлять push-уведомления в приложения iOS с помощью центров уведомлений Azure."
services: notification-hubs
documentationcenter: ios
keywords: "push-уведомление, push-уведомления, push-уведомления node.js, push-уведомления ios"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0e9e7ab196eef790b74074be319cd8122cf3ff5c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-ios-apps"></a>Начало работы с Центрами уведомлений Azure для приложений iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Обзор
> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).
> 
> 

В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS. Вы создадите пустое приложение iOS, получающее push-уведомления с помощью [службы push-уведомлений Apple (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

По завершении вы сможете рассылать push-уведомления на все устройства, на которых запущено ваше приложение, с помощью концентратора уведомлений.

## <a name="before-you-begin"></a>Перед началом работы
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Полный код для этого учебника можно найти на портале [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>предварительным требованиям
Для работы с данным учебником требуется следующее:

* [платформа обмена сообщениями Microsoft Azure];
* последняя версия [Xcode];
* устройство с iOS 10 (или более поздней версии);
* [программа для разработчиков на платформе Apple](https://developer.apple.com/programs/).
  
  > [!NOTE]
  > В соответствии с требованиями к настройкам push-уведомлений необходимо развернуть push-уведомления и протестировать их на физическом устройстве под управлением iOS (iPhone или iPad), а не в эмуляторе iOS.
  > 
  > 

Изучение этого руководства является необходимым условием для работы со всеми другими руководствами, посвященными Центрам уведомлений для приложений iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Настройка push-уведомлений iOS в центре уведомлений 
В этом разделе приведены пошаговые инструкции по созданию центра уведомлений и настройке аутентификации с помощью службы APNS, использующей раннее созданный вами сертификат push-уведомлений (файл с расширением **P12**). Если вы хотите использовать уже созданный центр уведомлений, перейдите к шагу 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>В разделе <b>Notification Services</b> выберите <b>Apple (APNS)</b>. Выберите вкладку <b>Сертификат</b>, щелкните значок файла и выберите файл <b>P12</b>, экспортированный ранее. Кроме того, необходимо указать правильный пароль.</p>

<p>Выберите режим <b>песочницы</b>, так как это необходимо для разработки. Используйте <b>рабочую среду</b>, только если push-уведомления нужно отправлять пользователям, выполнившим покупку приложения в магазине.</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![Настройка APNS на портале Azure][6]

&emsp;&emsp;&emsp;&emsp;![Настройка сертификации APNS на портале Azure][7]

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
   
5. Загрузите [платформа обмена сообщениями Microsoft Azure] и распакуйте файлы. В XCode щелкните проект правой кнопкой мыши и выберите параметр **Add Files to** (Добавить файлы в), чтобы добавить папку **WindowsAzureMessaging.framework** в проект XCode. Нажмите кнопку **Options** (Параметры), установите флажок **Copy items if needed** (Копировать элементы при необходимости) и нажмите кнопку **Add** (Добавить).

    ![Распаковка пакета SDK Azure][10]

6. Добавьте новый файл заголовка в проект с именем **HubInfo.h**. Этот файл будет содержать константы для центра уведомлений. Добавьте следующие определения и замените буквенные заполнители строк *именем центра* и значением *DefaultListenSharedAccessSignature*, записанным ранее.

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
Используя параметр *Тестовая отправка* на [портала Azure], вы можете проверить, получает ли приложение уведомления. Этот параметр отправит тестовое push-уведомление на устройство.

![Портал Azure — тестовая отправка][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="checking-if-your-app-can-receive-push-notifications"></a>Проверка того, может ли ваше приложение получать push-уведомления
Для тестирования push-уведомлений в iOS необходимо развернуть приложение на физическом устройстве под управлением iOS. Отправка push-уведомлений Apple через эмулятор iOS невозможна.

1. Запустите приложение и убедитесь, что оно успешно зарегистрировано, а затем нажмите кнопку **ОК**.
   
    ![Проверка регистрации push-уведомления приложения iOS][33]
2. Отправьте тестовое push-уведомление с [портала Azure], как описано выше. 

3. Push-уведомление будет отправлено на все устройства, зарегистрированные для получения уведомлений от того или иного центра уведомлений.
   
    ![Проверка получения push-уведомления приложения iOS][35]

## <a name="next-steps"></a>Дополнительная информация
В этом простом примере мы отправили push-уведомления на все зарегистрированные устройства iOS. Теперь рекомендуем перейти к руководству [Уведомление пользователей iOS через центры уведомлений с помощью серверной части .NET]. С помощью этого руководства вы создадите серверную часть для отправки push-уведомлений с помощью тегов. 

Дополнительные сведения о разделении пользователей по группам интересов см. в статье [Использование концентраторов уведомлений для передачи экстренных новостей]. 

Общие сведения о Центрах уведомлений см. [здесь].

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
[платформа обмена сообщениями Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[здесь]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Уведомление пользователей iOS через центры уведомлений с помощью серверной части .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[портала Azure]: https://portal.azure.com
