<properties
	pageTitle="Службы Мобильного Взаимодействия Azure: интеграция пакета SDK Reach для iOS | Microsoft Azure"
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

#Как интегрировать рекламные кампании Engagement в iOS

Перед выполнением этого руководства вам следует выполнить процедуры по интеграции, описанные в статье [Интеграция службы Engagement в iOS](mobile-engagement-ios-integrate-engagement.md).

Также требуется установка XCode 8. Если вам крайне важно оставить версию XCode 7, то можете воспользоваться [пакетом SDK Engagement для iOS версии 3.2.4](https://aka.ms/r6oouh). В предыдущей версии выявлена ошибка при выполнении на устройствах iOS 10: не приводятся в действие системные уведомления. Для исправления этой ошибки понадобится реализовать в делегате приложения устаревшую версию API `application:didReceiveRemoteNotification:`:

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **Данный способ не является рекомендуемым**, так как это поведение может измениться с любым последующим (даже незначительным) обновлением версии iOS из-за того, что этот интерфейс API для iOS устарел. Рекомендуется перейти на XCode 8 как можно скорее.

### Включение приложения для получения автоматических push-уведомлений

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##Этапы интеграции

### Внедрение пакета SDK для Engagement Reach в проект iOS

-   Добавьте пакет SDK для рекламных кампаний в проект Xcode. В Xcode щелкните **Проект > Добавить в проект** и выберите папку `EngagementReach`.

### Изменение делегата приложения

-   Импортируйте модуль обработки рекламных кампаний службы Engagement в верхней части файла реализации:

		[...]
		#import "AEReachModule.h"

-   Создайте внутри метода `applicationDidFinishLaunching:` или `application:didFinishLaunchingWithOptions:` модуль обработки рекламных кампаний и передайте его в существующую строку инициализации службы Engagement:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
		  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
		  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
		  [...]

		  return YES;
		}

-   Измените строку **icon.png** на имя изображения, которое вы хотите использовать в качестве значка уведомления.
-   Если вы хотите использовать параметр *Обновить значение индикатора событий* в рекламных кампаниях или применять кампании системных push-уведомлений </SaaS/Reach API/Campaign format/Native Push>, следует разрешить модулю обработки рекламных кампаний управлять значком индикатора событий (он будет автоматически очищать индикатор событий приложения и сбрасывать значение, хранимое службой Engagement, при каждом запуске приложения или его переключении на передний план). Для этого после инициализации модуля обработки рекламных кампаний необходимо добавить следующую строку:

		[reach setAutoBadgeEnabled:YES];

-   Чтобы обрабатывать отправку данных рекламных кампаний, делегат приложения должен соответствовать протоколу `AEReachDataPushDelegate`. После инициализации модуля обработки рекламных кампаний добавьте следующую строку:

		[reach setDataPushDelegate:self];

-   Затем можно реализовать методы `onDataPushStringReceived:` и `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` в делегате приложения:

		-(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
		{
		   NSLog(@"String data push message with category <%@> received: %@", category, body);
		   return YES;
		}

		-(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
		{
		   NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
		   // Do something useful with decodedBody like updating an image view
		   return YES;
		}

### Категория

При создании кампании отправки данных параметр «Категория» является необязательным. Он позволяет фильтровать отправленные данные. Это удобно, если вам нужно принудительно отправлять различные данные `Base64` и определять их тип перед анализом.

**Теперь приложение готово к получению и отображению содержимого рекламных кампаний!**

##Как получать объявления и опросы в любое время

С помощью службы push-уведомлений Apple служба Engagement может в любое время отправлять уведомления рекламных кампаний конечным пользователям.

Чтобы включить эту функциональную возможность, необходимо подготовить приложение для push-уведомлений Apple и изменить его делегат.

### Подготовка приложения для push-уведомлений Apple

Следуйте указаниям в руководстве [Как подготовить приложение для работы с push-уведомлениями Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6).

### Добавление необходимого клиентского кода

*На этом этапе для приложения должен быть зарегистрирован сертификат push-уведомлений Apple в интерфейсной части Engagement.*

Если он не зарегистрирован, необходимо зарегистрировать приложение для получения push-уведомлений. Добавьте следующую строку при запуске приложения (обычно в `application:didFinishLaunchingWithOptions:`):

	if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
	  	[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
	  	[application registerForRemoteNotifications];
	}
	else {
	  	[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
	}

Затем необходимо предоставить службе Engagement маркер устройства, возвращенный серверами Apple. Он указывается в делегате приложения в методе с именем `application:didRegisterForRemoteNotificationsWithDeviceToken:`:

	- (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
	{
	    [[EngagementAgent shared] registerDeviceToken:deviceToken];
	}

Наконец необходимо уведомить пакет SDK для службы Engagement о получении приложением удаленного уведомления. Для этого вызовите метод `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` в делегате приложения:

	- (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
	}

> [AZURE.NOTE] Описанный выше метод впервые появился в iOS 7. Если вы ориентируетесь на более старую платформу по сравнению с iOS 7, обязательно реализуйте метод `application:didReceiveRemoteNotification:` в делегате приложения и вызовите метод `applicationDidReceiveRemoteNotification` для EngagementAgent, передав nil вместо аргумента `handler`:

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] По умолчанию completionHandler управляется Engagement Reach. Если вы хотите вручную отреагировать на блок `handler`в коде, можно передать nil для аргумента `handler` и управлять блоком завершения самостоятельно. Список возможных значений см. в описании типа `UIBackgroundFetchResult`.


### Полный пример

Ниже приведен полный пример интеграции:

	#pragma mark -
	#pragma mark Application lifecycle

	- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
	{
	  /* Reach module */
	  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	  [reach setAutoBadgeEnabled:YES];

	  /* Engagement initialization */
	  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
	  [[EngagementAgent shared] setPushDelegate:self];

	  /* Views */
	  [window addSubview:[tabBarController view]];
	  [window makeKeyAndVisible];

	  [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
	  return YES;
	}

	- (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
	{
	  [[EngagementAgent shared] registerDeviceToken:deviceToken];
	}

	- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
	}

### Если у вас есть собственная реализация UNUserNotificationCenterDelegate

Пакет SDK также имеет собственную реализацию протокола UNUserNotificationCenterDelegate. Он используется пакетом SDK для отслеживания жизненного цикла уведомлений Служб Взаимодействия на устройствах, работающих под управлением iOS 10 или более поздней версии. Если пакет SDK обнаружит ваш делегат, он не будет применять собственную реализацию, так как в приложении может быть только один делегат UNUserNotificationCenter. Это означает, что вам потребуется добавить логику Служб Взаимодействия в собственный делегат.

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

##Как настраивать кампании

### Уведомления

Существует два типа уведомлений: системные уведомления и уведомления в приложении.

Системные уведомления обрабатываются iOS, и их невозможно настроить.

Уведомления в приложении состоят из представления, которое динамически добавляется в текущее окно приложения. Это называется наложением уведомлений. Наложения уведомления прекрасно подходят для быстрой интеграции, так как для них не требуется изменять представления в приложении.

#### Макет

Чтобы настроить внешний вид уведомлений в приложении, вы можете просто изменить файл `AENotificationView.xib` в соответствии со своими потребностями, но при этом следует сохранить значения тегов и типы существующих вложенных представлений.

По умолчанию уведомления в приложении представлены в нижней части экрана. Если нужно, чтобы они отображались в верхней части экрана, отредактируйте предоставляемый файл `AENotificationView.xib` и измените свойство `AutoSizing` главного представления, чтобы оно располагалось в верхней части суперпредставления.

#### Категории

При изменении предоставляемого макета изменяется внешний вид всех уведомлений. С помощью категорий можно определять различные типы внешнего вида (возможно, виды поведения) для уведомлений. Категорию можно указать при создании рекламной кампании. Учтите, что категории также позволяют настраивать объявления и опросы. Это описано далее в этом документе.

Чтобы зарегистрировать обработчик категорий для уведомлений, необходимо добавить вызов после инициализации модуля обработки рекламных кампаний.

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerNotifier:myNotifier forCategory:@"my_category"];
	...

`myNotifier` должен быть экземпляром объекта, который соответствует протоколу `AENotifier`.

Вы можете самостоятельно реализовать методы протокола или повторно реализовать существующий класс `AEDefaultNotifier`, который уже выполняет большую часть работы.

Например, если нужно переопределить представление уведомлений для определенной категории, можно использовать этот пример:

	#import "AEDefaultNotifier.h"
	#import "AENotificationView.h"
	@interface MyNotifier : AEDefaultNotifier
	@end

	@implementation MyNotifier

	-(NSString*)nibNameForCategory:(NSString*)category
	{
	  return "MyNotificationView";
	}

	@end

В этом простом примере категории предполагается, что в основном наборе приложений есть файл с именем `MyNotificationView.xib`. Если методу не удается найти соответствующий файл `.xib`, уведомление не будет отображаться и служба Engagement выведет сообщение н консоли.

Предоставленный NIB-файл должен подчиняться следующим правилам:

-   он должен содержать только одно представление;
-   вложенные представления должны относиться к тому же типу, что и вложенные представления в предоставленном NIB-файле с именем `AENotificationView.xib`;
-   вложенные представления должны содержать те же теги, что и вложенные представления в предоставленном NIB-файле с именем `AENotificationView.xib`.

> [AZURE.TIP] Просто скопируйте предоставленный NIB-файл с именем `AENotificationView.xib` и начните работу. Но будьте внимательны: представление в этом NIB-файле связано в классом `AENotificationView`. Этот класс переопределяет метод `layoutSubViews`, чтобы перемещать и изменять размер вложенных представлений в зависимости от контекста. Вы можете заменить его классом `UIView` или настраиваемым классом представления.

Если вам требуется расширенная настройка уведомлений (например, если вам нужно загружать представление непосредственно из кода), советуем просмотреть предоставленную документацию по исходному коду и классу для `Protocol ReferencesDefaultNotifier` и `AENotifier`.

Обратите внимание, что одно и то же средство уведомления можно использовать для нескольких категорий.

Кроме того, можно переопределить средство уведомления по умолчанию следующим образом:

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### Обработка уведомлений

При использовании категории по умолчанию некоторые методы жизненного цикла вызываются для объекта `AEReachContent`, чтобы предоставить статистику и обновить состояние кампании:

-   При отображении уведомления в приложении `AEReachModule` вызывает метод `displayNotification` (который предоставляет статистику), если `handleNotification:` возвращает `YES`.
-   При закрытии уведомления вызывается метод `exitNotification` и предоставляется статистика, после чего можно обрабатывать следующие кампании.
-   По щелчку уведомления вызывается метод `actionNotification`, предоставляется статистика и выполняется соответствующее действие.

Если ваша реализация `AENotifier` обходит поведение по умолчанию, необходимо вызывать эти методы жизненного цикла самостоятельно. В следующих примерах показаны некоторые случаи обхода поведения по умолчанию.

-   Вы не расширяли `AEDefaultNotifier`, например реализовали обработку категорий с нуля.
-   Вы переопределили `prepareNotificationView:forContent:`. Обязательно сопоставьте хотя бы `onNotificationActioned` или `onNotificationExited` с одним из элементов управления пользовательского интерфейса.

> [AZURE.WARNING] Если `handleNotification:` вызывает исключение, содержимое удаляется и вызывается `drop`. Затем это поведение регистрируется в статистике, после чего можно обрабатывать следующие кампании.

#### Включение уведомления в состав существующего представления

Наложения хорошо подходят для быстрой интеграции, но иногда могут быть неудобными или приводить к нежелательным побочным эффектам.

Если вы не удовлетворены системой наложения в некоторых представлениях, ее можно настроить.

Вы можете включить наш макет уведомлений в существующие представления. Это можно выполнить двумя способами:

1.  Добавьте представление уведомлений с помощью конструктора интерфейса:

	-   Откройте *конструктор интерфейса*.
	-   Разместите `UIView` размером 320 x 60 (или 768 x6 0 для iPad) там, где следует отображать уведомление.
	-   Задайте для параметра «Тег» значение **36822491**.

2.  Добавьте представление уведомлений программно. Просто добавьте следующий код после инициализации представления:

		UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
		notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
		[self.view addSubview:notificationView];

Макрос `NOTIFICATION_AREA_VIEW_TAG` можно найти в файле `AEDefaultNotifier.h`.

> [AZURE.NOTE] Средство уведомлений по умолчанию автоматически обнаруживает включение макета уведомлений в это представление и не будет добавлять наложение для него.

### Объявления и опросы

#### Макеты

Вы можете изменить файлы `AEDefaultAnnouncementView.xib` и `AEDefaultPollView.xib`, сохранив при этом значения тегов и типы существующих вложенных представлений.

#### Категории

##### Альтернативные макеты

Так же, как и категорию уведомлений, категорию кампании можно использовать для получения альтернативных макетов для объявлений и опросов.

Чтобы создать категорию для объявления, необходимо расширить **AEAnnouncementViewController** и зарегистрировать его после инициализации модуля обработки рекламных кампаний:

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] По каждому щелчку уведомления для объявления с категорией «my\_category» будет выполняться инициализация зарегистрированного контроллера представлений (в этом случае `MyCustomAnnouncementViewController`) путем вызова метода `initWithAnnouncement:`, а представление будет добавляться в текущее окно приложения.

В своей реализации класса `AEAnnouncementViewController` для инициализации вложенных представлений вам потребуется считать свойство `announcement`. Рассмотрите следующий пример, в котором две метки инициализируются с помощью свойств `title` и `body` класса `AEReachAnnouncement`:

	-(void)loadView
	{
	    [super loadView];

	    UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
	    titleLabel.font = [UIFont systemFontOfSize:32.0];
	    titleLabel.text = self.announcement.title;

	    UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
	    bodyLabel.font = [UIFont systemFontOfSize:24.0];
	    bodyLabel.text = self.announcement.body;

	    [self.view addSubview:titleLabel];
	    [self.view addSubview:bodyLabel];
	}

Если вместо того, чтобы загружать представления самостоятельно, вы хотите повторно использовать стандартный макет представления объявления по умолчанию, можно просто расширить предоставленный класс `AEDefaultAnnouncementViewController` с помощью пользовательского контролера представлений. Для этого продублируйте NIB-файл `AEDefaultAnnouncementView.xib` и переименуйте его, чтобы пользовательский контролер представлений мог загрузить его (для контроллера с именем `CustomAnnouncementViewController` NIB-файл нужно назвать `CustomAnnouncementView.xib`).

Чтобы заменить категорию объявлений по умолчанию, просто зарегистрируйте пользовательский контролер представлений для категории, определенной в `kAEReachDefaultCategory`:

	[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Опросы можно настроить так же:

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

В этом случае предоставленный `MyCustomPollViewController` должен расширять `AEPollViewController`. Или же можно расширить его с помощью контроллера по умолчанию: `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] Прежде чем закрыть контролер представлений, обязательно вызовите метод `action` (`submitAnswers:` для пользовательских контроллеров представлений опросов) или `exit`. В противном случае статистика не будет отправлена (т. е. не будет получена аналитика по кампании) и, что важнее, следующие кампании не будут получать уведомления до перезапуска процесса приложения.

##### Пример реализации

В этой реализации пользовательское представление объявлений загружается из внешнего XIB-файла.

Так же, как и для расширенной настройки уведомлений, мы советуем просмотреть исходный код в стандартной реализации.

`CustomAnnouncementViewController.h`

	//Interface
	@interface CustomAnnouncementViewController : AEAnnouncementViewController {
	  UILabel* titleLabel;
	  UITextView* descTextView;
	  UIWebView* htmlWebView;
	  UIButton* okButton;
	  UIButton* cancelButton;
	}

	@property (nonatomic, retain) IBOutlet UILabel* titleLabel;
	@property (nonatomic, retain) IBOutlet UITextView* descTextView;
	@property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
	@property (nonatomic, retain) IBOutlet UIButton* okButton;
	@property (nonatomic, retain) IBOutlet UIButton* cancelButton;

	-(IBAction)okButtonClicked:(id)sender;
	-(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

	//Implementation
	@implementation CustomAnnouncementViewController
	@synthesize titleLabel;
	@synthesize descTextView;
	@synthesize htmlWebView;
	@synthesize okButton;
	@synthesize cancelButton;

	-(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
	{
	  self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
	  if (self != nil) {
	    self.announcement = anAnnouncement;
	  }
	  return self;
	}

	- (void) dealloc
	{
	  [titleLabel release];
	  [descTextView release];
	  [htmlWebView release];
	  [okButton release];
	  [cancelButton release];
	  [super dealloc];
	}

	- (void)viewDidLoad {
	  [super viewDidLoad];

	  /* Init announcement title */
	  titleLabel.text = self.announcement.title;

	  /* Init announcement body */
	  if(self.announcement.type == AEAnnouncementTypeHtml)
	  {
	    titleLabel.hidden = YES;
	    htmlWebView.hidden = NO;
	    [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
	  }
	  else
	  {
	    titleLabel.hidden = NO;
	    htmlWebView.hidden = YES;
	    descTextView.text = self.announcement.body;
	  }

	  /* Set action button label */
	  if([self.announcement.actionLabel length] > 0)
	    [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

	  /* Set exit button label */
	  if([self.announcement.exitLabel length] > 0)
	    [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
	}

	#pragma mark Actions

	-(IBAction)okButtonClicked:(id)sender
	{
	    [self action];
	}

	-(IBAction)cancelButtonClicked:(id)sender
	{
	    [self exit];
	}

	@end

<!---HONumber=AcomDC_0921_2016-->