<properties
	pageTitle="Учебник по передаче экстренных новостей в центрах уведомлений: iOS"
	description="Узнайте, как использовать центры уведомлений Azure Service Bus для отправки уведомлений об экстренных новостях на устройства iOS."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="wesmc"/>

# Использование концентраторов уведомлений для передачи экстренных новостей

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Обзор

В этом разделе показано, как использовать центры уведомлений Azure для рассылки уведомлений об экстренных новостях в приложение iOS. По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Данный сценарий является общеупотребимым шаблоном для многих приложений, где требуется отправлять уведомления группам пользователей, ранее проявивших к ним интерес, например, программы чтения RSS, приложений для музыкальных фанатов и т. д.

Широковещательные сценарии реализуются путем включения одного или нескольких _тегов_ при создании регистрации в концентраторе уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительную информацию о тегах см. в разделе [Маршрутизация и выражения тегов](notification-hubs-routing-tag-expressions.md).


##Предварительные требования

Материал данного раздела основан на приложении, созданном в разделе [Приступая к работе с концентраторами уведомлений][get-started]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с концентраторами уведомлений][get-started].

##Добавление возможности выбора категорий в приложение

Прежде всего необходимо добавить элементы пользовательского интерфейса для имеющейся раскадровки, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в концентраторе уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

1. В вашем MainStoryboard\_iPhone.storyboard добавьте следующие компоненты из библиотеки объектов:
	+ метка с текстом "Экстренные новости";
	+ метки с текстами категории "Мир", "Политика", "Бизнес", "Технология", "Наука", "Спорт";
	+ Шесть переключателей, по одному в каждой категории. Задайте для каждого переключателя **Состояние** по умолчанию **Откл.**.
	+ Одна кнопка с надписью «Подписка».

	Раскадровка должна выглядеть следующим образом:

	![][3]

2. В редакторе помощника создайте выходы для всех переключателей и назовите их WorldSwitch, PoliticsSwitch, BusinessSwitch, TechnologySwitch, ScienceSwitch, SportsSwitch.


3. Создайте действие для кнопки с названием "Подписка". Файл BreakingNewsViewController.h должен содержать следующее:

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

4. Создайте новый класс **Cocoa Touch Class** с именем `Notifications`. Скопируйте следующий код в интерфейсную часть файла Notifications.h:

		@property NSData* deviceToken;

		- (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
					completion:(void (^)(NSError* error))completion;

		- (NSSet*)retrieveCategories;

		- (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Добавьте следующую директиву импорта в Notifications.m:

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Скопируйте следующий код в раздел файла Notifications.m, в котором запрограммирована реализация.

	    SBNotificationHub* hub;

		- (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

		    hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
										notificationHubPath:hubName];

			return self;
		}

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}


		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}


		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
		{
		   //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

			NSString* templateBodyAPNS = @"{"aps":{"alert":"$(messageParam)"}}";

			[hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
				jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
		}



	Этот класс использует локальное хранилище для хранения и извлечения категорий новостей, которые данное устройство должно получать. Он также содержит метод регистрации этих категорий с помощью [шаблонной](notification-hubs-templates.md) регистрации.

7. В файле AppDelegate.h добавьте оператор импорта для Notifications.h и добавьте свойство для экземпляра класса уведомлений:

		#import "Notifications.h"

		@property (nonatomic) Notifications* notifications;
	

8. В методе **didFinishLaunchingWithOptions** в файле AppDelegate.m добавьте в начало метода код для инициализации экземпляра уведомления.
 
	В `HUBNAME` и `HUBLISTENACCESS` (определены в hubinfo.h) заполнители `<hub name>` и `<connection string with listen access>` уже должны быть заменены именем центра уведомлений и строкой подключения для *DefaultListenSharedAccessSignature*, полученными ранее.

		self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

	> [AZURE.NOTE]Так как учетные данные, которые распространяются с помощью клиентского приложения, обычно небезопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Полный ключ доступа используется в защищенной серверной службе для отправки уведомлений и смены существующих регистраций.


9. В методе **didRegisterForRemoteNotificationsWithDeviceToken** в файле AppDelegate.m замените код метода на указанный ниже код, чтобы переместить маркер устройства в класс уведомлений. Класс уведомлений выполнит регистрацию получения уведомлений с категориями. Если пользователь меняет выбранные категории, для их обновления будет вызван метод `subscribeWithCategories` в ответ на действие кнопки **подписка**.

	> [AZURE.NOTE]Поскольку маркер устройства, назначенный службой push-уведомлений Apple (APNS), может измениться в любое время, следует регулярно производить регистрацию для использования уведомлений, чтобы предотвратить сбои уведомлений. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для приложений, которые запускаются чаще одного раза в день, в целях экономии пропускной способности регистрацию можно пропустить, если с момента предыдущей регистрации прошло менее одного дня.

		self.notifications.deviceToken = deviceToken;

		// Retrieves the categories from local storage and requests a registration for these categories
		// each time the app starts and performs a registration.

	    NSSet* categories = [self.notifications retrieveCategories];
	    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];


	Обратите внимание, что на данном этапе не должно быть никакого другого кода в методе **didRegisterForRemoteNotificationsWithDeviceToken**.

10.	По завершению работы с учебником [Приступая к работе с центрами уведомлений][get-started] в файле AppDelegate.m уже должны присутствовать указанные методы. В противном случае добавьте их.

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
	    }

	Этот метод обрабатывает уведомления, полученные при запуске приложения, отображая простой **UIAlert**.

11. В файле ViewController.m добавьте оператор импорта для AppDelegate.h и скопируйте предложенный код в созданный с помощью XCode метод **подписки**. Этот код обновляет регистрацию уведомлений для использования тегов новой категории, которые пользователь выбрал в пользовательском интерфейсе.

		```
		#import "Notifications.h"
		```

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Этот метод создает список категорий **NSMutableArray** и использует класс **Notifications** для хранения списка в локальном хранилище и регистрации соответствующих тегов в концентраторе уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

12. В файле ViewController.m в метод **viewDidLoad** добавьте предложенный код, чтобы задать пользовательский интерфейс на основе ранее сохраненных категорий.


		// This updates the UI on startup based on the status of previously saved categories.

		Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



Теперь приложение может сохранять набор категорий в локальном хранилище устройства и использовать его для регистрации в концентраторе уведомлений всякий раз при запуске приложения. Пользователь может изменить выбранные категории во время выполнения и щелкнуть метод **подписки** для обновления регистрации устройства. Далее вы сможете обновить приложение для отправки уведомлений об экстренных новостях непосредственно в состав самого приложения.


##(Необязательно.) Отправка уведомлений с тегами

Если у вас нет доступа к Visual Studio, можно перейти к следующему разделу и отправлять уведомления из самого приложения. Вы также можете отправлять правильные шаблонные уведомления с [классического портала Azure] с помощью вкладки «Отладка» для центра уведомлений.

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##(Необязательно.) Отправка уведомлений с устройства

Как правило, уведомления отправляются серверной службой, но вы можете отправлять уведомления об экстренных новостях непосредственно из приложения. Для этого мы обновим метод `SendNotificationRESTAPI`, определенный в учебнике [Приступая к работе с Центрами уведомлений][get-started].


1. В файле ViewController.m обновите метод `SendNotificationRESTAPI`, как показано ниже, чтобы он принимал параметр для тега категории и отправлял правильное [шаблонное](notification-hubs-templates.md) уведомление.

		- (void)SendNotificationRESTAPI:(NSString*)categoryTag
		{
		    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
									 defaultSessionConfiguration] delegate:nil delegateQueue:nil];

		    NSString *json;

		    // Construct the messages REST endpoint
		    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
		                                       HUBNAME, API_VERSION]];

		    // Generated the token to be used in the authorization header.
		    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

		    //Create the request to add the template notification message to the hub
		    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
		    [request setHTTPMethod:@"POST"];

		    // Add the category as a tag
		    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

			// Template notification
	        json = [NSString stringWithFormat:@"{"messageParam":"Breaking %@ News : %@"}",
	                categoryTag, self.notificationMessage.text];

	        // Signify template notification format
	        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

			// JSON Content-Type
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

		    //Authenticate the notification message POST request with the SaS token
		    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

		    //Add the notification message body
		    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

		    // Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
		               completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
	           {
	           NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
	               if (error || httpResponse.statusCode != 200)
	               {
	                   NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
	               }
	               if (data != NULL)
	               {
	                   //xmlParser = [[NSXMLParser alloc] initWithData:data];
	                   //[xmlParser setDelegate:self];
	                   //[xmlParser parse];
	               }
	           }];

		    [dataTask resume];
		}



2. В файле ViewController.m обновите действие **Отправить уведомление**, как показано в предложенном коде. Таким образом, он будет отправлять уведомления, используя отдельно каждый тег, и отправлять их на несколько платформ.



		- (IBAction)SendNotificationMessage:(id)sender
		{
		    self.sendResults.text = @"";

		    NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
									@"Technology", @"Science", @"Sports", nil];

		    // Lets send the message as breaking news for each category to WNS, GCM, and APNS
			// using a template.
		    for(NSString* category in categories)
		    {
		        [self SendNotificationRESTAPI:category];
		    }
		}



3. Повторно создайте проект и убедитесь, что у вас не возникли ошибки сборки.


##Запуск приложения и создание уведомлений

1. Нажмите кнопку Запуск для построения проекта, после чего запустите приложение. Чтобы подписаться на некоторые экстренные новости, нажмите кнопку **Подписаться**. В появившемся диалоговом окне отобразятся те уведомления, на которые была настроена подписка.

	![][1]

	Если выбрано **Подписка**, приложение преобразует выбранные категории в теги и запрашивает у концентратора уведомлений новую регистрацию устройств для выбранных тегов.

2. Введите сообщение, отправляемое в качестве экстренных новостей, и нажмите кнопку **Отправить уведомление**. Можно также запустить консольное приложение .NET для создания уведомлений.

	![][2]


3. Каждое устройство с подпиской на экстренные новости будет получать отправленные вами уведомления об экстренных новостях.



## Дальнейшие действия

В этом учебнике мы рассмотрели, как производить рассылку экстренных новостей по категориям. Рекомендуем вам также изучить один из указанных ниже учебников. В них рассматриваются более сложные сценарии использования концентраторов уведомлений.

+ **[Использование центров уведомлений для передачи локализованных экстренных новостей]**

	Как расширить возможности приложения экстренных новостей для отправки локализованных уведомлений.





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Использование центров уведомлений для передачи локализованных экстренных новостей]: notification-hubs-ios-send-localized-breaking-news.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[классического портала Azure]: https://manage.windowsazure.com

<!---HONumber=AcomDC_1217_2015-->