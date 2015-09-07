<properties
	pageTitle="Начало работы с Azure Mobile Engagement для iOS в Objective C"
	description="Узнайте, как использовать Azure Mobile Engagement с данными аналитики и push-уведомлениями для приложений iOS."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

# Начало работы с Azure Mobile Engagement для приложений iOS в Objective C

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

В этом разделе показано, как использовать Azure Mobile Engagement для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложение iOS. В этом учебнике вы создадите пустое приложение iOS, которое собирает основные данные и получает push-уведомления с помощью Apple Push Notification System (APNS). Ознакомившись с этим руководством, вы сможете рассылать push-уведомления на все устройства или конкретным пользователям в зависимости от свойств их устройств.

В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании Mobile Engagement для охвата определенных пользователей и групп устройств.

Для работы с данным учебником требуется следующее:

+ Xcode, который можно установить из MAC App Store
+ [Mobile Engagement iOS SDK]
+ Сертификат push-уведомлений (P12), который можно получить в центре разработки для Apple

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Mobile Engagement для приложений iOS.

> [AZURE.IMPORTANT]Выполнение этого учебника является необходимым условием для работы со всеми остальными учебниками, посвященными Mobile Engagement для приложений iOS. Для его выполнения необходимо иметь активную учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-RU%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения

1. Войдите на портал Azure, затем нажмите **+СОЗДАТЬ** в нижней части экрана.

2. Щелкните **Службы приложений**, **Mobile Engagement**, а затем — **Создать**.

	![][7]

3. Во всплывающем окне введите следующие сведения:

   ![][8]

   - **Имя приложения**. Введите имя своего приложения. Вы можете использовать любые символы.
   - **Платформа**: выберите целевую платформу для этого приложения (**iOS**) (если приложение предназначено для нескольких платформ, повторите операции из этого учебника для каждой из них).
   - **Имя ресурса приложения**. Это имя, по которому приложение можно будет открывать через API и по URL-адресам. Необходимо использовать только традиционные символы для URL-адреса. Автоматически созданное имя должно быть основой имени. Рекомендуем также добавить имя платформы, чтобы избежать конфликта имен, так как это имя должно быть уникальным.
   - **Расположение**. Выберите центр данных, в котором будет размещено это приложение и его коллекция.
   - **Коллекция**. Если вы уже создали приложение, щелкните **Коллекция**, чтобы выбрать созданную ранее, а если нет, щелкните элемент **Новая коллекция**.
   - **Имя коллекции**. Указывает на выбранную группу приложений. Если задать это имя, все приложения будут помещены в одну группу, что позволит выполнять сводные вычисления метрик. Здесь следует указать имя вашей компании или отдела, если применимо.

4. На вкладке **Приложения** выберите только что созданное приложение.

5. Щелкните **Информация о подключении**, чтобы отобразить параметры подключения, которые следует использовать при интеграции пакета SDK в вашем мобильном приложении.

	![][10]

6. Скопируйте **строку подключения**. Это необходимо, чтобы обозначить это приложение в коде приложения и подключиться к Mobile Engagement из вашего приложения Windows Phone.

	![][11]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции можно найти в документации по пакету SDK iOS для Mobile Engagement.

Мы создадим базовое приложение при помощи XCode, чтобы продемонстрировать интеграцию.

###Создание проекта iOS

Этот шаг можно пропустить, если приложение у вас уже есть и вы знакомы с разработкой для iOS.

1. Запустите Xcode и во всплывающем окне щелкните элемент **Create a new Xcode project** (Создать проект Xcode).

	![][12]

2. Выберите пункт **Single View Application** (Приложение с одним представлением) и нажмите кнопку **Next** (Далее).

	![][14]

3. Заполните поля **Product Name** (Название продукта), **Organization Name** (Название организации) и **Organization Identifier** (Идентификатор организации). Убедитесь, что в поле **Language** (Язык) выбран элемент **Objective-C**.

	![][13]

Xcode создаст демоверсию приложения, в которую мы интегрируем Mobile Engagement.

###Подключение приложения к серверной части Mobile Engagement

1. Скачайте [пакет SDK iOS для Mobile Engagement].
2. Извлеките файл .tar.gz в папку на компьютере.
3. Щелкните правой кнопкой мыши проект и выберите пункт **Add files to** (Добавить файлы в).

	![][17]

4. Перейдите в папку с извлеченным пакетом SDK и выберите папку `EngagementSDK`, а затем нажмите кнопку **OK** (ОК).

	![][18]

5. Откройте вкладку **Build Phases** (Этапы сборки) и в меню **Link Binary With Libraries** (Связать двоичные объекты с библиотеками) добавьте следующие инфраструктуры:

	![][19]

6. Вернитесь на страницу **Сведения о подключении** портала Azure для вашего приложения и скопируйте строку подключения.

	![][11]

7. Откройте файл реализации делегата приложения и добавьте следующую строку кода.

		#import "EngagementAgent.h"

8. Теперь вставьте строку подключения в делегат `didFinishLaunchingWithOptions`.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##<a id="monitor"></a>Включение мониторинга в реальном времени

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

- Откройте файл **ViewController.h**, импортируйте файл **EngagementViewController.h** и замените суперкласс интерфейса **ViewController** на **EngagementViewController**, как показано ниже:

![][22]

###Подключение приложения с помощью мониторинга в реальном времени

В этом разделе показано, как настроить приложение, чтобы оно подключалось к серверной части Mobile Engagement с помощью функции мониторинга в реальном времени.

1. Перейдите на портал Mobile Engagement

	На портале Azure войдите в приложение, которое используется для этого проекта, и нажмите кнопку **Выполнить охват** внизу страницы.

	![][26]

2. Вы перейдете на страницу **Параметры** на портале Mobile Engagement для приложения. Откройте вкладку **Монитор**:

	![][30]

3. Здесь будут показаны все устройства в режиме реального времени, на которых будет запущено приложение.

	![][31]

4. Вернитесь в Xcode, запустите приложение в симуляторе или на подключенном устройстве.

5. Если вы выполнили все приведенные выше действия, в разделе "Монитор" отобразится один сеанс.

**Поздравляем!** Вы успешно прошли первый шаг учебника и создали приложение, которое подключается к внутреннему серверу Mobile Engagement и уже отправляет данные.

6. Нажмите кнопку **Home** (Домашняя страница) в симуляторе, чтобы вернуть 0 сеансов на мониторе, как показано выше.

	![][33]

##<a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

### Включение приложения для получения автоматических push-уведомлений

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]


### Добавьте библиотеку охвата в проект

1. Щелкните проект правой кнопкой мыши.
2. Выберите пункт **Add file to** (Добавить файл в).
3. Перейдите в папку с извлеченным пакетом SDK.
4. Выберите папку `EngagementReach`.
5. Щелкните **Добавить**.

### Изменение делегата приложения

1. Импортируйте модуль обработки рекламных кампаний службы Engagement в верхней части файла реализации.

		#import "AEReachModule.h"

2. В методе **application:didFinishLaunchingWithOptions** создайте модуль обработки рекламных кампаний и передайте его в существующую строку инициализации Mobile Engagement:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###Разрешите приложению получать push-уведомления APNS

1. Добавьте следующую строку в метод **application:didFinishLaunchingWithOptions**:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Добавьте метод **application:didRegisterForRemoteNotificationsWithDeviceToken** следующим образом:

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Добавьте метод **didReceiveRemoteNotification:fetchCompletionHandler** следующим образом:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

###Предоставление доступа к сертификату push-уведомлений для Mobile Engagement

Чтобы разрешить Mobile Engagement отправлять push-уведомления от вашего имени, необходимо предоставить доступ к сертификату. Это можно сделать путем настройки и ввода сертификата на портале Mobile Engagement. Убедитесь, что получили сертификат P12, как описано в документации Apple.

1. Перейдите на портал Mobile Engagement. Убедитесь, что вы используете требуемое приложение для этого проекта, и нажмите кнопку **Использовать** внизу.

	![][26]

2. Вы перейдете на страницу **Параметры** на портале Mobile Engagement. Выберите здесь раздел **Системное push-уведомление**, чтобы отправить сертификат P12:

	![][27]

3. Выберите сертификат P12, отправьте его и введите пароль:

	![][28]

4. Теперь добавьте профиль подготовки и создайте приложение для целевого устройства.

Все готово. Теперь убедимся, что базовая интеграция выполнена правильно.

##<a id="send"></a>Отправка уведомления в приложение

Теперь мы создадим простую кампанию push-уведомлений, которая отправит push-уведомление в приложение.

1. Перейдите на вкладку **Обработка рекламных кампаний** на портале Mobile Engagement.

2. Щелкните **Создать объявление**, чтобы создать кампанию push-уведомлений.

	![][35]

3. Настройте первое поле кампании.

	![][36]

	- 	Присвойте кампании любое имя.
	- 	Установите для параметра **Время доставки** значение **Только за пределами приложения** — это простой тип push-уведомлений Apple с произвольным текстом.
	- 	В тексте уведомления введите сначала заголовок в первой строке push-уведомления.
	- 	Затем введите сообщение во второй строке.


4. Прокрутите окно вниз и в разделе **Содержимое** выберите пункт "Только уведомления".

	![][37]

5. Вы настроили простейшую базовую кампанию. Теперь прокрутите окно вниз еще раз, создайте кампанию и сохраните ее. ![][38]

6. Наконец активируйте свою кампанию. ![][39]

7. На устройстве должно появиться push-уведомление!

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[пакет SDK iOS для Mobile Engagement]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->