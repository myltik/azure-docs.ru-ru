<properties
	pageTitle="Начало работы с Azure Mobile Engagement для iOS в Objective C"
	description="Узнайте, как использовать Azure Mobile Engagement с данными аналитики и push-уведомлениями для приложений iOS."
	services="mobile-engagement"
	documentationCenter="ios"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

# Начало работы с Azure Mobile Engagement для приложений iOS в Objective C

> [AZURE.SELECTOR]
- [Универсальные приложения Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

В этом разделе показано, как использовать Azure Mobile Engagement для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложение iOS. В этом учебнике вы создадите пустое приложение iOS, которое собирает основные данные и получает push-уведомления с помощью Apple Push Notification System (APNS).

Для работы с данным учебником требуется следующее:

+ XCode 6 или XCode 7, которые можно установить из Mac App Store
+ [Mobile Engagement iOS SDK]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Mobile Engagement для приложений iOS.

> [AZURE.IMPORTANT] Выполнение этого учебника является необходимым условием для работы со всеми остальными учебниками, посвященными Mobile Engagement для приложений iOS. Для его выполнения необходимо иметь активную учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-RU%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения iOS

[AZURE.INCLUDE [Создание приложения Mobile Engagement на портале](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции можно найти в разделе [Интеграция пакета Mobile Engagement iOS SDK](../mobile-engagement-ios-sdk-overview/).

Мы создадим базовое приложение при помощи XCode, чтобы продемонстрировать интеграцию.

###Создание проекта iOS

[AZURE.INCLUDE [Создание проекта для iOS](../../includes/mobile-engagement-create-new-ios-app.md)]

###Подключение приложения к серверной части Mobile Engagement

1. Скачайте пакет [Mobile Engagement iOS SDK].
2. Извлеките файл .tar.gz в папку на компьютере.
3. Щелкните правой кнопкой мыши проект и выберите пункт **Добавить файлы в**.

	![][1]

4. Откройте папку с извлеченным пакетом SDK и выберите папку `EngagementSDK`, а затем нажмите кнопку **OK**.

	![][2]

5. Откройте вкладку **Этапы сборки** и в меню **Связывание двоичных объектов с библиотеками** добавьте указанные ниже инфраструктуры.

	![][3]

6. Для **XCode 7** добавьте `libxml2.tbd` вместо `libxml2.dylib`.

7. Вернитесь на страницу **Сведения о подключении** портала Azure для вашего приложения и скопируйте строку подключения.

	![][4]

8. В файле **AppDelegate.m** добавьте указанную ниже строку кода.

		#import "EngagementAgent.h"

9. Теперь вставьте строку подключения в делегат `didFinishLaunchingWithOptions`.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
			//[EngagementAgent setTestLogEnabled:YES];
   
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

10. `setTestLogEnabled` — это необязательный оператор, который включает журналы пакета SDK и позволяет выявить проблемы.

##<a id="monitor"></a>Включение мониторинга в режиме реального времени

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

1. Откройте файл **ViewController.h** и импортируйте **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Теперь измените суперкласс интерфейса **ViewController** на `EngagementViewController`.

	`@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени

[AZURE.INCLUDE [Подключение приложения с мониторингом в реальном времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

### Включение приложения для получения автоматических push-уведомлений

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### Добавьте библиотеку охвата в проект

1. Щелкните проект правой кнопкой мыши.
2. Выберите пункт **Добавить файл в**.
3. Перейдите в папку с извлеченным пакетом SDK.
4. Выберите папку `EngagementReach`
5. Щелкните **Добавить**.

### Изменение делегата приложения

1. В файле **AppDeletegate.m** импортируйте модуль Engagement Reach.

		#import "AEReachModule.h"

2. В методе `application:didFinishLaunchingWithOptions` создайте модуль обработки рекламных кампаний и передайте его в существующую строку инициализации Engagement.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###Разрешите приложению получать push-уведомления APNS

1. Добавьте следующую строку в метод `application:didFinishLaunchingWithOptions`:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

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

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

<!---HONumber=AcomDC_0302_2016-->