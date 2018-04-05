---
title: Начало работы со Службами мобильного взаимодействия Azure для iOS в Objective C | Документация Майкрософт
description: Узнайте, как использовать Службы мобильного взаимодействия Azure с данными аналитики и push-уведомлениями для приложений iOS.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 7cf493e3a4878c64b0ea6a74b6e16329ce3d27b5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Начало работы со Службами мобильного взаимодействия Azure для приложений iOS в Objective C
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как использовать Службы мобильного взаимодействия Azure для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложение iOS.
В этом учебнике вы создадите пустое приложение iOS, которое собирает основные данные и получает push-уведомления с помощью Apple Push Notification System (APNS).

Для работы с данным учебником требуется следующее:

* XCode 8, который можно установить из MAC App Store;
* [SDK для Служб мобильного взаимодействия (iOS)]

Завершение изучения этого руководства является необходимым условием для работы со всеми другими руководствами, посвященными Службам мобильного взаимодействия для приложений iOS.

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).
>
>

## <a id="setup-azme"></a>Настройка Служб мобильного взаимодействия для вашего приложения iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции можно найти в статье [Интеграция пакета Android SDK со Службами мобильного взаимодействия Azure](mobile-engagement-ios-sdk-overview.md).

Мы создадим базовое приложение при помощи XCode, чтобы продемонстрировать интеграцию.

### <a name="create-a-new-ios-project"></a>Создание проекта iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
1. Скачайте пакет [SDK для Служб мобильного взаимодействия (iOS)].
2. Извлеките файл .tar.gz в папку на компьютере.
3. Щелкните правой кнопкой мыши проект и выберите пункт **Добавить файлы в**.

    ![][1]

4. Перейдите в папку, в которой вы извлекли пакет SDK, выберите папку `EngagementSDK`, в нижнем левом углу щелкните **Параметры** и убедитесь, что установлен флажок **Copy items if needed** (Копировать элементы при необходимости) и флажок для своей цели, а затем нажмите **OK**.

    ![][2]

5. Откройте вкладку **Build Phases** (Этапы сборки) и в меню **Link Binary With Libraries** (Связывание двоичных объектов с библиотеками) добавьте указанные ниже платформы.

    ![][3]

6. Вернитесь на страницу **Сведения о подключении** портала Azure для вашего приложения и скопируйте строку подключения.

    ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)
7. В файле **AppDelegate.m** добавьте указанную ниже строку кода.

    ```obj-c
    #import "EngagementAgent.h"
    ```
8. Теперь вставьте строку подключения в делегат `didFinishLaunchingWithOptions` .

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
    }
    ```

9. `setTestLogEnabled` — это необязательный оператор, который включает журналы пакета SDK и позволяет выявить проблемы.

## <a id="monitor"></a>Включение мониторинга в режиме реального времени
Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Служб мобильного взаимодействия.

1. Откройте файл **ViewController.h** и импортируйте **EngagementViewController.h**:

    ```obj-c
    #import "EngagementViewController.h"
    ```

2. Теперь измените суперкласс интерфейса **ViewController** на `EngagementViewController`.
 
    ```obj-c
   @interface ViewController : EngagementViewController
   ```

## <a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении
Службы мобильного взаимодействия позволяют взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Служб мобильного взаимодействия этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
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

    ```obj-c
    #import "AEReachModule.h"
    #import <UserNotifications/UserNotifications.h>
    ```

2. В методе `application:didFinishLaunchingWithOptions` создайте модуль обработки рекламных кампаний и передайте его в существующую строку инициализации Engagement.

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]
        return YES;
    }
    ```

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Разрешите приложению получать push-уведомления APNS
1. Добавьте следующую строку в метод `application:didFinishLaunchingWithOptions`:

    ```obj-c
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
    ```
2. Добавьте метод `application:didRegisterForRemoteNotificationsWithDeviceToken` следующим образом:

    ```obj-c
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
        NSLog(@"Registered Token: %@", deviceToken);
    }
    ```

3. Добавьте метод `didFailToRegisterForRemoteNotificationsWithError` следующим образом:

    ```obj-c
    - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
    {
        NSLog(@"Failed to get token, error: %@", error);
    }
    ```

4. Добавьте метод `didReceiveRemoteNotification:fetchCompletionHandler` следующим образом:

    ```obj-c
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }
    ```
    
[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SDK для Служб мобильного взаимодействия (iOS)]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
