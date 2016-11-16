---
title: "Начало работы со службой мобильного взаимодействия Azure для iOS в Objective C | Документация Майкрософт"
description: "Узнайте, как использовать Azure Mobile Engagement с данными аналитики и push-уведомлениями для приложений iOS."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/05/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1dc9885e4cdbad1153ac476e3f0c0068ec391374


---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Начало работы с Azure Mobile Engagement для приложений iOS в Objective C
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как использовать Azure Mobile Engagement для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложение iOS.
В этом учебнике вы создадите пустое приложение iOS, которое собирает основные данные и получает push-уведомления с помощью Apple Push Notification System (APNS).

Для работы с данным учебником требуется следующее:

* XCode 8, который можно установить из MAC App Store;
* [Mobile Engagement iOS SDK]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Mobile Engagement для приложений iOS.

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).
> 
> 

## <a name="a-idsetupazmeasetup-mobile-engagement-for-your-ios-app"></a><a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnectingappaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement
В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции можно найти в статье [Интеграция пакета Android SDK с Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md).

Мы создадим базовое приложение при помощи XCode, чтобы продемонстрировать интеграцию.

### <a name="create-a-new-ios-project"></a>Создание проекта iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Подключение приложения к серверной части Mobile Engagement
1. Скачайте пакет [Mobile Engagement iOS SDK].
2. Извлеките файл .tar.gz в папку на компьютере.
3. Щелкните правой кнопкой мыши проект и выберите пункт **Добавить файлы в**.
   
    ![][1]
4. Откройте папку с извлеченным пакетом SDK и выберите папку `EngagementSDK` , а затем нажмите кнопку **OK**.
   
    ![][2]
5. Откройте вкладку **Build Phases** (Этапы сборки) и в меню **Link Binary With Libraries** (Связывание двоичных объектов с библиотеками) добавьте указанные ниже платформы.
   
    ![][3]
6. Вернитесь на страницу **Сведения о подключении** портала Azure для вашего приложения и скопируйте строку подключения.
   
    ![][4]
7. В файле **AppDelegate.m** добавьте указанную ниже строку кода.
   
        #import "EngagementAgent.h"
8. Теперь вставьте строку подключения в делегат `didFinishLaunchingWithOptions` .
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
              [...]   
              [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
              [...]
        }
9. `setTestLogEnabled` — это необязательный оператор, который включает журналы пакета SDK и позволяет выявить проблемы. 

## <a name="a-idmonitoraenable-realtime-monitoring"></a><a id="monitor"></a>Включение мониторинга в режиме реального времени
Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

1. Откройте файл **ViewController.h** и импортируйте **EngagementViewController.h**:
   
    `# import "EngagementViewController.h"`
2. Теперь измените суперкласс интерфейса **ViewController** на `EngagementViewController`.
   
    `@interface ViewController : EngagementViewController`

## <a name="a-idmonitoraconnect-app-with-realtime-monitoring"></a><a id="monitor"></a>Подключение приложения с возможностью его отслеживания в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegratepushaenable-push-notifications-and-inapp-messaging"></a><a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении
Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Включение приложения для получения автоматических push-уведомлений
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Добавьте библиотеку охвата в проект
1. Щелкните проект правой кнопкой мыши.
2. Выберите пункт **Добавить файл в**.
3. Перейдите в папку с извлеченным пакетом SDK.
4. Выберите папку `EngagementReach`
5. Щелкните **Добавить**.

### <a name="modify-your-application-delegate"></a>Изменение делегата приложения
1. В файле **AppDeletegate.m** импортируйте модуль Engagement Reach.
   
        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>
2. В методе `application:didFinishLaunchingWithOptions` создайте модуль обработки рекламных кампаний и передайте его в существующую строку инициализации Engagement.
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Разрешите приложению получать push-уведомления APNS
1. Добавьте следующую строку в метод `application:didFinishLaunchingWithOptions`:
   
        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }
2. Добавьте метод `application:didRegisterForRemoteNotificationsWithDeviceToken` следующим образом:
   
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
             [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }
3. Добавьте метод `didFailToRegisterForRemoteNotificationsWithError` следующим образом:
   
        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
   
           NSLog(@"Failed to get token, error: %@", error);
        }
4. Добавьте метод `didReceiveRemoteNotification:fetchCompletionHandler` следующим образом:
   
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png




<!--HONumber=Nov16_HO2-->


