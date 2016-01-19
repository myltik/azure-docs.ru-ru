<properties
	pageTitle="Начало работы с Azure Mobile Engagement для iOS в Swift"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для приложений iOS."
	services="mobile-engagement"
	documentationCenter="ios"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Начало работы с Azure Mobile Engagement для приложений iOS в Swift

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

В этом разделе показано, как использовать Azure Mobile Engagement для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложение iOS. В этом учебнике вы создадите пустое приложение iOS, которое собирает основные данные и получает push-уведомления с помощью Apple Push Notification System (APNS).

Для работы с данным учебником требуется следующее:

+ XCode 6 или XCode 7, которые можно установить из Mac App Store
+ [Mobile Engagement iOS SDK]
+ Сертификат push-уведомлений (P12), который можно получить в центре разработки для Apple

> [AZURE.NOTE]В этом учебнике используется Swift версии 2.0.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Mobile Engagement для приложений iOS.

> [AZURE.IMPORTANT]Выполнение этого учебника является необходимым условием для работы со всеми остальными учебниками, посвященными Mobile Engagement для приложений iOS. Для его выполнения необходимо иметь активную учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-RU%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения iOS

[AZURE.INCLUDE [Создание приложения Mobile Engagement на портале](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции можно найти в разделе [Интеграция пакета Mobile Engagement iOS SDK](../mobile-engagement-ios-sdk-overview/).

Создадим базовое приложение при помощи XCode, чтобы продемонстрировать интеграцию.

###Создание проекта iOS

[AZURE.INCLUDE [Создание проекта iOS](../../includes/mobile-engagement-create-new-ios-app.md)]

###Подключите приложение к серверной части Mobile Engagement.

1. Скачайте [Mobile Engagement iOS SDK].
2. Извлеките файл TAR.gz в папку на компьютере.
3. Щелкните правой кнопкой мыши проект и выберите "Добавить файлы в...".

	![][1]

4. Перейдите в папку с извлеченным SDK и выберите папку `EngagementSDK`, а затем нажмите кнопку "ОК".

	![][2]

5. Откройте вкладку `Build Phases` и в меню `Link Binary With Libraries` добавьте среды, как это показано ниже.

	![][3]

6. Для **XCode 7** добавьте `libxml2.tbd` вместо `libxml2.dylib`.

7. Создайте связующий заголовок, чтобы получить возможность использовать Objective C API в SDK, выбрав "Файл > Создать > Файл > iOS > Источник > Файл заголовка".

	![][4]

8. Измените промежуточный файл заголовков так, чтобы в нем отображался код Mobile Engagement Objective-C для вашего кода Swift, и добавьте следующие объекты импорта:

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

9. В разделе "Параметры сборки" убедитесь, что для настройки сборки связующего заголовка Objective-C в разделе "Компилятор Swift — создание кода" указан путь к этому заголовку. Ниже приведен пример пути: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (в зависимости от пути)**

	![][6]

10. Вернитесь на страницу портала Azure *Информация о подключении* для вашего приложения и скопируйте строку подключения.

	![][5]

11. Теперь вставьте строку подключения в делегат `didFinishLaunchingWithOptions`.

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Включение мониторинга в реальном времени

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

1. Откройте файл **ViewController.swift** и замените базовый класс **ViewController** на **EngagementViewController**:

	`class ViewController : EngagementViewController {`

##<a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени

[AZURE.INCLUDE [Подключение приложения с возможностью его отслеживания в режиме реального времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах описаны действия по настройке приложения для приема уведомлений и сообщений.

### Включение приложения для получения автоматических push-уведомлений

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### Добавьте библиотеку охвата в проект

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

### Изменение делегата приложения

1. В `didFinishLaunchingWithOptions` создайте модуль обработки рекламных кампаний и передайте его в существующую строку инициализации Engagement:

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###Разрешите приложению получать push-уведомления APNS
1. Добавьте следующую строку в метод `didFinishLaunchingWithOptions`:

		/* Ask user to receive push notifications */
		if #available(iOS 8.0, *)
		{
		   let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
		   application.registerUserNotificationSettings(settings)
		   application.registerForRemoteNotifications()
		}
		else
		{
		   application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
		}

2. Добавьте метод `didRegisterForRemoteNotificationsWithDeviceToken` следующим образом:

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. Добавьте метод `didReceiveRemoteNotification:fetchCompletionHandler:` следующим образом:

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png

<!---HONumber=AcomDC_0114_2016-->