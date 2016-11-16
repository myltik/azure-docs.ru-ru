---
title: "Начало работы со службой мобильного взаимодействия Azure для iOS в Swift | Документация Майкрософт"
description: "Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для приложений iOS."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1011b9823333e79a52cd2d187df4f8d063b1f799


---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Начало работы с Azure Mobile Engagement для приложений iOS в Swift
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как использовать Azure Mobile Engagement для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложение iOS.
В этом учебнике вы создадите пустое приложение iOS, которое собирает основные данные и получает push-уведомления с помощью Apple Push Notification System (APNS).

Для работы с данным учебником требуется следующее:

* XCode 8, который можно установить из MAC App Store;
* [Mobile Engagement iOS SDK]
* Сертификат push-уведомлений (P12), который можно получить в центре разработки для Apple

> [!NOTE]
> В этом учебнике используется Swift версии 3.0. 
> 
> 

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Mobile Engagement для приложений iOS.

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).
> 
> 

## <a name="a-idsetupazmeasetup-mobile-engagement-for-your-ios-app"></a><a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnectingappaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement
В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции можно найти в статье [Интеграция пакета Android SDK с Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md).

Создадим базовое приложение при помощи XCode, чтобы продемонстрировать интеграцию.

### <a name="create-a-new-ios-project"></a>Создание проекта iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Подключение приложения к серверной части Mobile Engagement
1. Скачайте [Mobile Engagement iOS SDK]
2. Извлеките файл TAR.gz в папку на компьютере.
3. Щелкните правой кнопкой мыши проект и выберите "Добавить файлы в...".
   
    ![][1]
4. Перейдите в папку с извлеченным SDK и выберите папку `EngagementSDK`, а затем нажмите кнопку "ОК".
   
    ![][2]
5. Откройте вкладку `Build Phases` и в меню `Link Binary With Libraries` добавьте среды, как это показано ниже.
   
    ![][3]
6. Создайте связующий заголовок, чтобы получить возможность использовать Objective C API в SDK, выбрав "Файл > Создать > Файл > iOS > Источник > Файл заголовка".
   
    ![][4]
7. Измените промежуточный файл заголовков так, чтобы в нем отображался код Mobile Engagement Objective-C для вашего кода Swift, и добавьте следующие объекты импорта:
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. В разделе "Параметры сборки" убедитесь, что для настройки сборки связующего заголовка Objective-C в разделе "Компилятор Swift — создание кода" указан путь к этому заголовку. Ниже приведен пример пути: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (в зависимости от пути)**
   
   ![][6]
9. Вернитесь на страницу портала Azure *Информация о подключении* для вашего приложения и скопируйте строку подключения.
   
   ![][5]
10. Теперь вставьте строку подключения в делегат `didFinishLaunchingWithOptions` .
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a name="a-idmonitoraenabling-realtime-monitoring"></a><a id="monitor"></a>Включение мониторинга в реальном времени
Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

1. Откройте файл **ViewController.swift** и замените базовый класс **ViewController** классом **EngagementViewController**:
   
    `class ViewController : EngagementViewController {`

## <a name="a-idmonitoraconnect-app-with-realtime-monitoring"></a><a id="monitor"></a>Подключение приложения с возможностью его отслеживания в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="a-idintegratepushaenabling-push-notifications-and-inapp-messaging"></a><a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении
Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
В следующих разделах описаны действия по настройке приложения для приема уведомлений и сообщений.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Включение приложения для получения автоматических push-уведомлений
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Добавьте библиотеку охвата в проект
1. Щелкните правой кнопкой мыши проект
2. Выберите `Add file to ...`
3. Перейдите в папку с извлеченным SDK
4. Выберите папку `EngagementReach`
5. Нажмите "Добавить"
6. Измените промежуточный файл заголовков так, чтобы в нем отображались заголовки Mobile Engagement Objective-C Reach, и добавьте следующие объекты импорта:
   
        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Изменение делегата приложения
1. В `didFinishLaunchingWithOptions` создайте модуль обработки рекламных кампаний и передайте его в существующую строку инициализации Engagement:
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Разрешите приложению получать push-уведомления APNS
1. Добавьте следующую строку в метод `didFinishLaunchingWithOptions`:
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. Добавьте метод `didRegisterForRemoteNotificationsWithDeviceToken` следующим образом:
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. Добавьте метод `didReceiveRemoteNotification:fetchCompletionHandler:` следующим образом:
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png



<!--HONumber=Nov16_HO2-->


