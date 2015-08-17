<properties
	pageTitle="Начало работы с Azure Mobile Engagement для iOS в Swift"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для приложений iOS."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr" />

# Начало работы с Azure Mobile Engagement для приложений iOS в Swift

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

В этом разделе показано, как использовать Azure Mobile Engagement для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложение iOS. В этом учебнике вы создадите пустое приложение iOS, которое собирает основные данные и получает push-уведомления с помощью Apple Push Notification System (APNS). По завершении вы сможете рассылать push-уведомления на все устройства или взять за цель конкретных пользователей на основе свойств их устройств.

В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании Mobile Engagement для охвата определенных пользователей и групп устройств.

Для работы с данным учебником требуется следующее:

+ Xcode, который можно установить из MAC App Store
+ [Mobile Engagement iOS SDK]
+ Сертификат push-уведомлений (P12), который можно получить в центре разработки для Apple

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Mobile Engagement для приложений iOS.

> [AZURE.IMPORTANT]Выполнение этого учебника является необходимым условием для работы со всеми остальными учебниками, посвященными Mobile Engagement для приложений iOS. Для его выполнения необходимо иметь активную учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения

1. Выполните вход на портал управления Azure, затем нажмите **+NEW** в нижней части экрана.

2. Щелкните **Службы приложений**, **Mobile Engagement**, а затем **Создать**.

	![][7]

3. Во всплывающем окне введите следующую информацию:

	![][8]

	- **Имя приложения** — введите имя своего приложения. Вы можете использовать любые символы.
	- **Платформа**: выберите целевую платформу для этого приложения (**iOS**) (если приложение предназначено для нескольких платформ, повторите операции из этого учебника для каждой из них).
	- **Имя ресурса приложения**. Это имя, по которому приложение можно будет открывать через API и по URL-адресам. Необходимо использовать только традиционные символы для URL-адреса. Автоматически созданное имя должно обеспечить достаточно прочную основу. Также рекомендуется добавить имя платформы, чтобы избежать конфликта имен, так как это имя должно быть уникальным.
	- **Местоположение**. Выберите центр обработки данных, в котором будет размещено это приложение (и, что еще важнее, его коллекция).
	- **Коллекция**. Если вы уже создали приложение, то выберите созданную ранее коллекцию, а если нет, то выберите "Создать коллекцию".
	- **Имя коллекции**. Указывает на выбранную группу приложений. Оно также обеспечит помещение всех ваших приложений в одну группу, что позволит выполнять сводное вычисление метрик. Здесь следует указать имя вашей компании или отдела, если применимо.

4. На вкладке **Приложения** выберите только что созданное приложение.

5. Щелкните **Информация о подключении**, чтобы отобразить параметры подключения, которые следует использовать при интеграции пакета SDK в вашем мобильном приложении.

	![][10]

6. Скопируйте **строку подключения**. Это требуется для того, чтобы обозначить данное приложение в коде приложения и подключиться к Mobile Engagement, используя для этого телефонное приложение.

	![][11]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом учебнике описаны действия по базовой интеграции, т. е. минимум, требуемый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции можно найти в [документации по iOS SDK для Mobile Engagement].

Создадим базовое приложение при помощи XCode, чтобы продемонстрировать интеграцию.

###Создание проекта iOS

Этот шаг можно пропустить, если приложение уже имеется и вы знакомы с разработкой для iOS.

1. Запустите Xcode и щелкните во всплывающем окне **Создать проект Xcode**.

	![][12]

2. Теперь выберите **Приложение с одним представлением** и нажмите кнопку "Далее".

	![][14]

3. Заполните поля **Название продукта**, **Название организации** и **Идентификатор организации**. Убедитесь, что выбран **Swift** на этом языке.

	![][40]

Xcode создаст демоверсию приложения, в которую интегрируется Mobile Engagement.

###Подключите приложение к серверной части Mobile Engagement.

1. Скачайте [Mobile Engagement iOS SDK].
2. Извлеките файл TAR.gz в папку на компьютере.
3. Щелкните правой кнопкой мыши проект и выберите "Добавить файлы в...".

	![][17]

4. Перейдите в папку с извлеченным SDK и выберите папку `EngagementSDK`, а затем нажмите кнопку "ОК".

	![][18]

5. Откройте вкладку `Build Phases` и в меню `Link Binary With Libraries` добавьте среды, как это показано ниже.

	![][19]

6. Создайте связующий заголовок, чтобы получить возможность использовать Objective C API в SDK, выбрав "Файл > Создать > Файл > iOS > Источник > Файл заголовка".

	![][41]

7. Измените файл связующего заголовка так, чтобы в нем показывался код AzME Objective-C для созданного кода Swift, и добавьте следующие объекты импорта:

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

8. В разделе "Параметры сборки" убедитесь, что для настройки сборки связующего заголовка Objective-C в разделе "Компилятор Swift — создание кода" указан путь к этому заголовку. Ниже приведен пример пути: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (в зависимости от пути)**

9. Вернитесь на страницу портала Azure *Информация о подключении* для вашего приложения и скопируйте строку подключения.

	![][11]

10. Теперь вставьте строку подключения в делегат `didFinishLaunchingWithOptions`.

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Включение мониторинга в реальном времени

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

- Для этого откройте файл `ViewController.h`, импортируйте `EngagementViewController.h` и замените суперкласс интерфейса `ViewController` на `EngagementViewController`.

###Убедитесь, что приложение подключено с использованием функции мониторинга в реальном времени

В этом разделе показано, как сделать так, чтобы приложение подключалось к серверной части Mobile Engagement с помощью функции мониторинга в реальном времени.

1. Перейдите на портал Mobile Engagement

	На портале Azure убедитесь, что находитесь в приложении, которое используется для этого проекта, и нажмите кнопку "Взаимодействовать" внизу страницы.

	![][26]

2. Вы перейдете на страницу "Параметры" на портале Mobile Engagement для приложения. Откройте здесь вкладку "Монитор":

	![][30]

3. На вкладке "Монитор" будут показаны все устройства в режиме реального времени, которые запустят приложение.

	![][31]

4. Вернитесь в Xcode, запустите приложение в симуляторе или на подключенном устройстве

5. Если вы выполнили все приведенные выше действия, в разделе "Монитор" отобразится один сеанс.

**Поздравляем!** Вы успешно прошли первый шаг учебника и создали приложение, которое подключается к внутреннему серверу Mobile Engagement и уже отправляет данные

6. Нажмите кнопку "Домашняя страница" в симуляторе, чтобы вернуть 0 сеансов на мониторе, как показано выше

	![][33]

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
6. Измените файл связующего заголовка так, чтобы в нем открывались заголовки рекламной кампании AzME Objective-C, добавьте следующие импортируемые объекты:

		/* Mobile Engagement Reach */
		#import "AE_TBXML.h"
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
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### Изменение делегата приложения

1. В `didFinishLaunchingWithOptions` создайте модуль обработки рекламных кампаний и передайте его в существующую строку инициализации Mobile Engagement:

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###Разрешите приложению получать push-уведомления APNS
1. Добавьте следующую строку в метод `didFinishLaunchingWithOptions`:

		if application.respondsToSelector("registerUserNotificationSettings:")
		{
			application.registerUserNotificationSettings(UIUserNotificationSettings(
			forTypes: (UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound),
			categories: nil))
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotificationTypes(UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound)
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

###Предоставьте доступ к сертификату push-уведомлений для Mobile Engagement

Чтобы разрешить Mobile Engagement отправлять push-уведомления от вашего имени, необходимо предоставить доступ к сертификату. Это можно сделать путем настройки и ввода сертификата на портале Mobile Engagement. Убедитесь, что получили сертификат P12, как описано в документации Apple.

1. Перейдите на портал Mobile Engagement. Убедитесь, что вы используете требуемое приложение для этого проекта, и нажмите кнопку "Использовать" внизу.

	![][26]

2. Вы перейдете на страницу "Параметры" на портале Mobile Engagement. Выберите здесь раздел "Системное push-уведомление", чтобы отправить сертификат P12:

	![][27]

3. Выберите сертификат P12, отправьте его и введите пароль:

	![][28]

4. Теперь добавьте профиль подготовки и создайте приложение для целевого устройства

Настройка выполнена, теперь убедимся, что базовая интеграция успешно выполнена

##<a id="send"></a>Отправка уведомления в приложение

Создадим простую кампанию push-уведомлений, которая отправит push-уведомление в приложение.

1. Перейдите на вкладку "Охват" на портале Mobile Engagement.

2. Щелкните **Создать объявление**, чтобы создать кампанию push-уведомлений.

	![][35]

3. Настройте первое поле кампании.

	![][36]

	- 	Присвойте кампании любое имя.
	- 	Выберите время доставки "Только за пределами приложения" — это простой тип push-уведомлений Apple с произвольным текстом.
	- 	В тексте уведомления введите сначала заголовок в первой строке push-уведомления.
	- 	Затем введите сообщение во второй строке.


4. Прокрутите вниз и в разделе "Содержимое" выберите "Только уведомления".

	![][37]

5. Вы закончили настройку самой простой кампании. Теперь снова выполните прокрутку вниз, создайте кампанию и сохраните ее. ![][38]

6. И наконец, активируйте свою кампанию ![][39]

7. На устройстве должно появиться push-уведомление!

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-swift-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-swift-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-swift-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-swift-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-swift-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-swift-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-swift-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-swift-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-swift-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-swift-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-swift-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-swift-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-swift-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-swift-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-swift-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-swift-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-ios-swift-get-started/SwiftSelection.png
[41]: ./media/mobile-engagement-ios-swift-get-started/AddHeaderFile.png

<!---HONumber=August15_HO6-->