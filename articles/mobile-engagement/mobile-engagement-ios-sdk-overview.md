<properties
	pageTitle="Обзор веб-пакета SDK для Azure Mobile Engagement | Microsoft Azure"
	description="Последние обновления и указания для пакета SDK для iOS для Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

#Пакет SDK для Azure Mobile Engagement (iOS)

Начните с этой статьи, чтобы получить подробную информацию об интеграции Azure Mobile Engagement в приложение iOS. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-ios-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-ios-sdk-content.md).

##Процедуры по интеграции
1. Начните с этого раздела: [Как интегрировать Mobile Engagement в приложение iOS](mobile-engagement-ios-integrate-engagement.md)

2. Сведения об уведомлениях: [Как интегрировать Reach (Notifications) в приложение iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Реализация плана добавления тегов: [Как использовать API для расширенного добавления тегов Mobile Engagement в приложении iOS](mobile-engagement-ios-use-engagement-api.md)


##Заметки о выпуске

### 4\.0.0 (12.09.2016)

-   Исправлена ошибка, из-за которой не приводились в действие уведомления на устройствах iOS 10.
-   Устаревшая версия XCode 7 заменена новой версией.

Информацию о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-ios-release-notes.md).

##Процедуры обновления

Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить ряд процедур. См. полную версию статьи [Процедуры обновления](mobile-engagement-ios-upgrade-procedure.md).

Для каждой новой версии пакета SDK необходимо сначала заменить (удалить и повторно импортировать в проект Хcode) папки EngagementSDK и EngagementReach.

###С версии 3.0.0 до версии 4.0.0

### XCode 8
Версия XCode 8 является обязательной, начиная с пакета SDK версии 4.0.0.

> [AZURE.NOTE] Если вам крайне важно оставить версию XCode 7, то можете воспользоваться [пакетом SDK Engagement для iOS версии 3.2.4](https://aka.ms/r6oouh). В предыдущей версии выявлена ошибка в модуле обработки рекламных кампаний при выполнении на устройствах iOS 10: не приводятся в действие системные уведомления. Для исправления этой ошибки понадобится реализовать в делегате приложения устаревшую версию API `application:didReceiveRemoteNotification:`:

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **Данный способ не является рекомендуемым**, так как это поведение может измениться с любым последующим (даже незначительным) обновлением версии iOS из-за того, что этот интерфейс API для iOS устарел. Рекомендуется перейти на XCode 8 как можно скорее.

#### Платформа UserNotifications
В этапы сборки необходимо добавить платформу `UserNotifications`.

В обозревателе проектов откройте область проектов и выберите правильную цель. Затем откройте вкладку **Build phases** (Этапы сборки) и в меню **Link Binary With Libraries** (Связать двоичные объекты с библиотеками) добавьте платформу `UserNotifications.framework`, задав значение ссылки `Optional`.

#### Функция push-уведомлений приложения
XCode 8 может выполнить сброс настроек push-уведомлений приложения. Проверьте настройки этой функции на вкладке `capability` выбранной цели.

#### Если у вас уже есть собственная реализация UNUserNotificationCenterDelegate

Пакет SDK имеет собственную реализацию протокола UNUserNotificationCenterDelegate. Он используется пакетом SDK для отслеживания жизненного цикла уведомлений Служб Взаимодействия на устройствах, работающих под управлением iOS 10 или более поздней версии. Если пакет SDK обнаружит ваш делегат, он не будет применять собственную реализацию, так как в приложении может быть только один делегат UNUserNotificationCenter. Это означает, что вам потребуется добавить логику Служб Взаимодействия в собственный делегат.

Это достигается двумя способами.

Просто переадресуйте вызовы делегата в пакет SDK:

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

Или воспользуйтесь наследованием из класса `AEUserNotificationHandler`:

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

> [AZURE.NOTE] Можно определить, поступают ли уведомления из Служб Взаимодействия, передав словарь `userInfo` в метод класса `isEngagementPushPayload:` агента.

<!---HONumber=AcomDC_0921_2016-->