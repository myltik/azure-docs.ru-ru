<properties 
	pageTitle="Начало работы с Azure Mobile Engagement для iOS" 
	description="Сведения об использовании Azure Mobile Engagement с аналитикой и push-уведомлениями в iOS" 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# Начало работы с Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Магазин Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

В этом разделе показано, как использовать Azure Mobile Engagement для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложение iOS. 
В этом учебнике вы создадите пустое приложение iOS, которое собирает основные данные и получает push-уведомления с помощью Apple Push Notification System (APNS). По завершении вы сможете передать push-уведомления на все устройства или всем целевым пользователям на основе свойств устройств.

В этом учебнике показан простой сценарий трансляции с использованием Mobile Engagement. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании Mobile Engagement для охвата определенных пользователей и групп устройств. 

Для работы с данным учебником требуется следующее:

+ Xcode, который можно установить из MAC App Store
+ [Mobile Engagement iOS SDK]
+ Сертификат push-уведомлений (P12), который можно получить в Центре разработки для Apple

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Mobile Engagement для приложений iOS. 

<div class="dev-callout"><strong>Примечание</strong> <p>Для работы с этим учебником требуется активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Настройка Mobile Engagement для приложения

1. Войдите на [Портал управления Azure], а затем нажмите кнопку **+СОЗДАТЬ** в нижней части экрана.

2. Щелкните **Службы приложений**, **Mobile Engagement**, а затем - **Создать**.

   	![][7]

3. Во всплывающем окне нужно заполнить несколько полей.
 
   	![][8]

	1. *Имя приложения* - введите имя приложения. Вы можете использовать любой символ
	2. *Платформа* - выберите целевую платформу для этого приложения (если приложение предназначено для нескольких платформ, повторите действия в этом учебнике для каждой платформы)
	3. *Имя ресурса приложения* - это имя, которое будет использоваться для доступа к приложению через API и URL-адреса. Рекомендуется использовать только обычные символы URL-адреса: автоматические созданное имя предоставит надежную основу. Также рекомендуется добавить имя платформы, чтобы избежать конфликтов, так как это имя должно быть уникальным
	4. *Расположение* - выберите центр обработки данных, в котором будет размещаться это приложение (и, что более важно, его коллекция - см. ниже)
	5. *Коллекция* - если вы уже создали приложение, выберите ранее созданную коллекцию; в противном случае щелкните "Создать коллекцию"


	Закончив, нажмите кнопку "Проверить", чтобы завершить создание приложения

4. Теперь выберите созданное приложение во вкладке "Приложение".
 
   	![][9]

5. Затем щелкните "Сведения о подключении", чтобы отобразить параметры подключения для размещения интеграции SDK.
 
   	![][10]

6. Наконец, выпишите из кода приложения "Строку подключения", которая потребуется для идентификации этого приложения.

   	![][11]

	>[AZURE.TIP] Для удобства можно щелкнуть значок "копировать" справа от строки подключения, чтобы копировать его в буфер обмена.

##<a id="connecting-app"></a>Подключение приложения к внутреннему серверу Mobile Engagement

В этом учебнике представлена "базовая интеграция", которая является минимальным набором, необходимым для сбора данных и отправки push-уведомлений. Полную документацию по интеграции можно найти в [Документации по Mobile Engagement iOS SDK]

Создадим основное приложение с помощью Android Studio, чтобы продемонстрировать интеграцию

###Создание проекта iOS

Этот шаг можно пропустить, если у вас уже есть приложение и вы знакомы с разработкой для iOS

1. Запустите Xcode и щелкните во всплывающем окне "Создать проект Xcode"

   	![][12]

2. Укажите имя приложения, компании и идентификатор. Запишите их, так как они потребуются вам позже, затем нажмите кнопку "Далее"

   	![][13]

3. Теперь выберите "Приложение с одним представлением" и нажмите кнопку "Далее"

   	![][14]


Xcode создаст демо-версию приложения, до которой мы интегрируем Mobile Engagement

###Включите библиотеку SDK в проект

Скачайте и интегрируйте библиотеку SDK

1. Скачайте [Mobile Engagement iOS SDK]
2. Извлеките файл .tar.gz в папку на компьютере
3. Щелкните правой кнопкой мыши проект и выберите "Добавить файлы в..."

	![][17]

4. Перейдите в папку с извлеченным SDK и выберите папку  `EngagementSDK`, а затем нажмите кнопку "ОК"

	![][18]

5. Откройте вкладку  `Этапы сборки` и добавьте в меню  `Связать двоичный файл с библиотеками` платформы, как показано ниже:

	![][19]


###Подключите приложение к внутреннему серверу Mobile Engagement с помощью строки подключения

1. Скопируйте следующую строку кода вверху файла реализации "Делегат приложения"

		#import "EngagementAgent.h"

2. Перейдите на портал Azure на странице  *Сведения о подключении* приложения и скопируйте строку подключения

	![][11]

3. Вставьте ее в делегат  `didFinishLaunchingWithOptions`		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##Отправьте экран в Mobile Engagement

Чтобы начать отправку данных и убедиться в активности пользователей необходимо отправить по крайней мере один экран (действие) на внутренний сервер Mobile Engagement. Этого можно достичь путем перегрузки класса  `UIViewController` с  `EngagementViewController`, предоставленным SDK.
Для этого откройте файл  `ViewController.h`, импортируйте  `EngagementViewController.h` и замените суперкласс интерфейса  `ViewController` на  `EngagementViewController`, как показано ниже:

![][22]

##<a id="monitor"></a>Проверка подключения приложения с мониторингом в режиме реального времени

В этом разделе показано, как убедиться, что приложение подключается к внутреннему серверу Mobile Engagement с помощью функции мониторинга Mobile Engagement в режиме реального времени.

1. Перейдите на портал Mobile Engagement

	На портале Azure убедитесь, что находитесь в приложении, которое используется для этого проекта, и нажмите кнопку "Взаимодействовать" внизу страницы:

	![][26]

2. Вы перейдете на страницу "Параметры" на портале Mobile Engagement для приложения. Откройте здесь вкладку "Монитор":

	![][30]

3. На вкладке "Монитор" будут показаны все устройства в режиме реального времени, которые запустят приложение:

	![][31]

4. Вернитесь в Xcode, запустите приложение в симуляторе или на подключенном устройстве

5. Если все получилось, на мониторе должен отобразиться один сеанс. 

**Поздравляем!** Вы успешно прошли первый шаг учебника и создали приложение, которое подключается к внутреннему серверу Mobile Engagement и уже отправляет данные

6. Нажмите кнопку "Домашняя страница" в симуляторе, чтобы вернуть ноль сеансов на мониторе, как показано выше

	![][33]



##<a id="integrate-push"></a>Включение push-уведомлений и сообщений в приложении

Mobile Engagement позволяет взаимодействовать и охватывать пользователей с помощью push-уведомлений и сообщений в приложении в контексте кампаний. Этот модуль называется "ОХВАТ" на портале Mobile Engagement.
В следующих сеансах приложение будет настроено для их получения.

### Добавьте библиотеку охвата в проект

1. Щелкните правой кнопкой мыши проект
2. Выберите `Добавить файл в...`
3. Перейдите в папку с извлеченным SDK
4. Выберите папку  `EngagementReach`
5. Нажмите "Добавить"

### Измените делегат приложения

1. Вверху файла реализации импортируйте модуль обработки рекламных кампаний Mobile Engagement

		#import "AEReachModule.h"
	
2. В  `application:didFinishLaunchingWithOptions` создайте модуль обработки рекламных кампаний и передайте его в существующую строку инициализации Mobile Engagement:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}	

###Разрешите приложению получать push-уведомления APNS

1. Добавьте следующую строку в метод  `application:didFinishLaunchingWithOptions`:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Добавьте метод  `application:didRegisterForRemoteNotificationsWithDeviceToken` следующим образом:
 
		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 		   [[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Добавьте метод  `didReceiveRemoteNotification` следующим образом:
4. 
		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###предоставьте доступ к сертификату push-уведомлений для Mobile Engagement

Чтобы разрешить Mobile Engagement отправлять push-уведомления от вашего имени, необходимо предоставить доступ к сертификату. Это можно сделать путем настройки и ввода сертификата на портале Mobile Engagement. Убедитесь, что получили сертификат P12, как описано в документации Apple.

1. Перейдите на портал Mobile Engagement

	На портале Azure убедитесь, что находитесь в приложении, которое используется для этого проекта, и нажмите кнопку "Взаимодействовать" внизу страницы:

	![][26]

2. Вы перейдете на страницу "Параметры на портале Mobile Engagement. Выберите здесь раздел "Системное push-уведомление", чтобы отправить  сертификат P12:

	![][27]

3. Выберите сертификат P12, отправьте его и введите пароль:

	![][28]

4. Теперь добавьте профиль подготовки и создайте приложение для целевого устройства

Настройка выполнена. Теперь убедимся, что базовая интеграция успешно выполнена

##<a id="send"></a>Отправка уведомления в приложение

Создадим простую кампанию push-уведомлений, которая отправит push-уведомление в приложение.

1. Перейдите на вкладку "Охват" на портале Mobile Engagement,

2. щелкните "Создать объявление", чтобы создать кампанию push-уведомлений.
	
	![][35]

3. Настройте первое поле кампании.

	![][36]

	1. Назовите кампанию любым именем.
	2. Выберите время доставки "Только за пределами приложения" - это простой тип push-уведомлений Apple с некоторым текстом.
	3. В тексте уведомления введите сначала заголовок в первой строке push-уведомления.
	4. Затем введите сообщение во второй строке.

4. Прокрутите вниз и в разделе "Содержимое" выберите "Только уведомления".

	![][37]

5. Вы завершили настройку самой простой основной кампании. Теперь снова прокрутите экран вниз и создайте кампанию, чтобы сохранить ее!
![][38]

6. Последний шаг - активация кампании
![][39]

7. На устройстве должно появиться push-уведомление!

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Документация по Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9874682
[Портал управления Azure]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
