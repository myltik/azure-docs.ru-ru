---
title: Пакет SDK для Служб мобильного взаимодействия Azure (iOS) | Документация Майкрософт
description: Последние обновления и указания для пакета SDK для iOS для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3a03bbd6-bcf8-436c-9775-5a8188629252
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 6c9d2ddeacd1d05142019f22eedf4eb3bb4f06cd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="ios-sdk-for-azure-mobile-engagement"></a>Пакет SDK для Служб мобильного взаимодействия Azure (iOS)
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Начните с этой статьи, чтобы получить подробную информацию об интеграции Служб мобильного взаимодействия Azure в приложения iOS. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-ios-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-ios-sdk-content.md)

## <a name="integration-procedures"></a>Процедуры по интеграции
1. Начните с этого раздела: [Как интегрировать Службы мобильного взаимодействия в приложения iOS](mobile-engagement-ios-integrate-engagement.md)
2. Сведения об уведомлениях: [Как интегрировать Reach (Notifications) в приложение iOS](mobile-engagement-ios-integrate-engagement-reach.md)
3. Реализация плана добавления тегов: [Как использовать API для расширенного добавления тегов Служб мобильного взаимодействия в приложении iOS](mobile-engagement-ios-use-engagement-api.md)

## <a name="release-notes"></a>Заметки о выпуске
### <a name="410-07172017"></a>4.1.0 (17.07.2017)
* Исправлена проблема с исчезающими на заднем фоне эмблемами.
* Исправлена проблема с предупреждениями в XCode 9 о невызванных в основной очереди API-интерфейсах.
* Исправлена утечка памяти при опросах охвата.
* Прекращена поддержка iOS 6.X. Начиная с этой версии целевое устройство для развертывания приложения должно быть с версией не ниже iOS 7.

Сведения о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-ios-release-notes.md).

## <a name="upgrade-procedures"></a>Процедуры обновления
Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобится выполнить ряд процедур. См. полную версию статьи [Процедуры обновления](mobile-engagement-ios-upgrade-procedure.md).

Для каждой новой версии пакета SDK необходимо сначала заменить (удалить и повторно импортировать в проект Хcode) папки EngagementSDK и EngagementReach.

### <a name="from-300-to-400"></a>С версии 3.0.0 до версии 4.0.0
### <a name="xcode-8"></a>XCode 8
Версия XCode 8 является обязательной, начиная с пакета SDK версии 4.0.0.

> [!NOTE]
> Если вам крайне важно оставить версию XCode 7, то можете воспользоваться [пакетом SDK Служб взаимодействия для iOS версии 3.2.4](https://aka.ms/r6oouh). В предыдущей версии на устройствах iOS 10 выявлена ошибка в модуле обработки рекламных кампаний: не приводятся в действие системные уведомления. Для исправления этой ошибки понадобится реализовать в делегате приложения устаревшую версию API `application:didReceiveRemoteNotification:` :
>
>

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Данный способ не является рекомендуемым** , так как это поведение может измениться с любым последующим (даже незначительным) обновлением версии iOS из-за того, что этот интерфейс API для iOS устарел. Рекомендуется перейти на XCode 8 как можно скорее.
>
>

#### <a name="usernotifications-framework"></a>Платформа UserNotifications
В этапы сборки необходимо добавить платформу `UserNotifications` .

В обозревателе проектов откройте область проектов и выберите правильную цель. Затем откройте вкладку **Build phases** (Этапы сборки) и в меню **Link Binary With Libraries** (Связать двоичные объекты с библиотеками) добавьте платформу `UserNotifications.framework`, задав значение ссылки `Optional`.

#### <a name="application-push-capability"></a>Функция push-уведомлений приложения
XCode 8 может выполнить сброс настроек push-уведомлений приложения. Проверьте настройки этой функции на вкладке `capability` выбранной цели.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Добавление нового кода регистрации уведомления в iOS 10
Старый фрагмент кода для регистрации приложения для получения уведомлений по-прежнему функционирует, но он использует устаревшие интерфейсы API при работе в iOS 10.

Импортируйте платформу `User Notification` :

        #import <UserNotifications/UserNotifications.h>

В методе `application:didFinishLaunchingWithOptions` делегата приложения замените:

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

на:

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

#### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Разрешение конфликтов делегата UNUserNotificationCenter

*Если приложения или библиотеки сторонних производителей не реализуют `UNUserNotificationCenterDelegate`, то эту часть можно пропустить.*

Делегат `UNUserNotificationCenter` используется пакетом SDK для отслеживания жизненного цикла уведомлений служб Engagement на устройствах, работающих под управлением iOS 10 или более поздней версии. Пакет SDK содержит собственную реализацию протокола `UNUserNotificationCenterDelegate`, однако в приложении может быть только один делегат `UNUserNotificationCenter`. Любой другой делегат, добавляемый в объект `UNUserNotificationCenter`, будет конфликтовать со службой Engagement. Если пакет SDK обнаружит ваш или другой делегат, он не будет применять собственную реализацию, чтобы дать вам возможность самому разрешить конфликт. Для разрешения конфликтов вам потребуется добавить приложение логики Engagement в собственный делегат.

Это достигается двумя способами.

Первый способ. Просто переадресуйте вызовы делегата в пакет SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Второй способ. Воспользуйтесь наследованием из класса `AEUserNotificationHandler`:

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Можно определить, поступают ли уведомления из Служб взаимодействия, передав словарь `userInfo` в метод класса `isEngagementPushPayload:` агента.

Убедитесь, что в делегате приложения делегат объекта `UNUserNotificationCenter` имеет значение вашего делегата в методе `application:willFinishLaunchingWithOptions:` или `application:didFinishLaunchingWithOptions:`.
Например, если вы воспользовались первым способом:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }
