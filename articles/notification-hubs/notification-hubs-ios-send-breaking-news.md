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
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>

# Использование концентраторов уведомлений для передачи экстренных новостей
<div class="dev-center-tutorial-selector sublanding">
	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
	<a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

##Обзор

В этом разделе показано, как использовать центры уведомлений Azure для рассылки уведомлений об экстренных новостях в приложение iOS. По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Данный сценарий является общеупотребимым шаблоном для многих приложений, где требуется отправлять уведомления группам пользователей, ранее проявивших к ним интерес, например, программы чтения RSS, приложений для музыкальных фанатов и т. д.

Широковещательные сценарии реализуются путем включения одного или нескольких _тегов_ при создании регистрации в центре уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительные сведения о тегах см. в статье [Общие сведения о концентраторах уведомлений].


##Предварительные требования

Материал данного раздела основан на приложении, созданном в разделе [Приступая к работе с центрами уведомлений][get-started]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с центрами уведомлений][get-started].

##Добавление возможности выбора категорий в приложение

Прежде всего необходимо добавить элементы пользовательского интерфейса для имеющейся раскадровки, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в центре уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

2. В вашем MainStoryboard_iPhone.storyboard добавьте следующие компоненты из библиотеки объектов:
	+ метка с текстом "Экстренные новости";
	+ метки с текстами категории "Мир", "Политика", "Бизнес", "Технология", "Наука", "Спорт";
	+ шесть переключателей по категориям;
	+ кнопка с надписью "Подписка".

	Раскадровка должна выглядеть следующим образом:

	![][3]

3. В редакторе помощника создайте выходы для всех переключателей и назовите их WorldSwitch, PoliticsSwitch, BusinessSwitch, TechnologySwitch, ScienceSwitch, SportsSwitch.

	![][4]

4. Создайте действие для кнопки с названием "Подписка". Файл BreakingNewsViewController.h должен содержать следующее:

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

5. Создайте новый класс с именем `Notifications`. Скопируйте следующий код в интерфейсную часть файла Notifications.h:

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
		- (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

6. Добавьте следующую директиву импорта в Notifications.m:

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

7. Скопируйте следующий код в реализационную часть файла Notifications.m:

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}

		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}

	Этот класс использует локальное хранилище для хранения категорий новостей, которые данное устройство должно получать. Он также содержит методы для регистрации этих категорий.

4. В приведенном выше коде замените заполнители `<hub name>` и `<connection string with listen access>` именем концентратора уведомлений и строкой подключения для полученного ранее элемента *DefaultListenSharedAccessSignature*.

	> [AZURE.NOTE] Так как учетные данные, которые распространяются с помощью клиентского приложения, обычно небезопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Для отправки уведомлений и изменения существующих регистраций используется ключ полного доступа в защищенной серверной службе.

8. В файле BreakingNewsAppDelegate.h добавьте следующее свойство:

		@property (nonatomic) Notifications* notifications;

	Это создает единственный экземпляр класса уведомлений в AppDelegate.

9. В методе **didFinishLaunchingWithOptions** в файле BreakingNewsAppDelegate.m добавьте следующий код перед **registerForRemoteNotificationTypes**:

		self.notifications = [[Notifications alloc] init];

	Инициализирует одноэлементное уведомление.

10. В методе **didRegisterForRemoteNotificationsWithDeviceToken** в файле BreakingNewsAppDelegate.m удалите вызов **registerNativeWithDeviceToken** и добавьте следующий код:

		self.notifications.deviceToken = deviceToken;

	Обратите внимание, что на данном этапе не должно быть никакого другого кода в методе **didRegisterForRemoteNotificationsWithDeviceToken**.

11.	Добавьте следующий метод в BreakingNewsAppDelegate.m:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		    [alert show];
	    }

	Этот метод обрабатывает уведомления, полученные при запуске приложения, отображая простой **UIAlert**.

9. В BreakingNewsViewController.m скопируйте следующий код в созданный при помощи XCode метод **subscribe**:

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Этот метод создает список категорий **NSMutableArray** и использует класс **Notifications** для хранения списка в локальном хранилище и регистрации соответствующих тегов в центре уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

Ваше приложение теперь может сохранять набор категорий в локальном хранилище на устройстве и регистрироваться в центре уведомлений всякий раз, когда пользователь изменяет выбранные категории.

##Регистрация для использования уведомлений

Эти действия позволяют зарегистрироваться в центре уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище.

> [AZURE.NOTE] Поскольку маркер устройства, назначенный службой push-уведомлений Apple (APNS), может измениться в любое время, следует регулярно производить регистрацию для использования уведомлений, чтобы предотвратить сбои уведомлений. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для приложений, которые запускаются чаще одного раза в день, в целях экономии пропускной способности регистрацию можно пропустить, если с момента предыдущей регистрации прошло менее одного дня.

1. Добавьте следующий метод в интерфейсную часть файла Notifications.h:

		- (NSSet*)retrieveCategories;

	Этот код извлекает категории в классе уведомлений.

2. Добавьте соответствующую реализацию в файл Notifications.m:

		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}

2. Добавьте в метод **didRegisterForRemoteNotificationsWithDeviceToken** следующий код:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];
	    [notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

	Это гарантирует, что при каждом запуске приложения оно извлекает категории из локального хранилища и запрашивает для них регистрацию.

3. В BreakingNewsViewController.h добавьте следующий код в метод **viewDidLoad**:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

	При этом пользовательский интерфейс при запуске обновляется в зависимости от состояния ранее сохраненных категорий.

Теперь приложение готово и может сохранять набор категорий в локальном хранилище устройств и использовать его для регистрации в центре уведомлений каждый раз, когда пользователь изменяет выбранные категории. А сейчас определим серверную часть, которая может отправлять уведомления категорий в это приложение.

##Отправка уведомлений из серверной части

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##Запуск приложения и создание уведомлений

1. Нажмите клавишу "Запуск" для построения проекта, после чего запустите приложение.

	![][2]

	Обратите внимание, что в пользовательском интерфейсе присутствует набор переключателей, позволяющий выбрать категории для подписки.

2. Включите переключатель для одной или нескольких категорий, а затем нажмите кнопку **Подписаться**.

	Если выбран метод **Subscribe**, приложение преобразует выбранные категории в теги и запрашивает у центра уведомлений новую регистрацию устройств для выбранных тегов.

4. Отправьте новое уведомление из серверной части одним из следующих способов:

	+ **Консольное приложение:** запустите консольное приложение.

	+ **Java/PHP:** запустите свое приложение или сценарий.

5. Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.

## Дальнейшие действия

В этом учебнике мы рассмотрели, как производить рассылку экстренных новостей по категориям. Рекомендуем вам также изучить один из указанных ниже учебников. В них рассматриваются более сложные сценарии использования концентраторов уведомлений.

+ **[Использование концентраторов уведомлений для передачи локализованных экстренных новостей]**

	Узнайте, как расширить возможности приложения экстренных новостей для отправки локализованных уведомлений.

+ **[Уведомление пользователей с помощью центров уведомлений]**

	Узнайте, как рассылать push-уведомления определенным пользователям, прошедшим проверку подлинности. Это хорошее решение для отправки уведомлений только определенным пользователям.



<!-- Images. -->
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png







<!-- URLs. -->
[Практическое руководство. Использование концентраторов уведомлений Service Bus (приложения iOS)]: http://msdn.microsoft.com/library/jj927168.aspx
[Использование концентраторов уведомлений для вещания локализованных экстренных новостей]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Использование концентраторов уведомлений для передачи локализованных экстренных новостей]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Мобильная служба]: /develop/mobile/tutorials/get-started
[Уведомление пользователей с помощью центров уведомлений]: /manage/services/notification-hubs/notify-users/

[Портал управления Azure]: https://manage.windowsazure.com/
[Руководство по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Общие сведения о концентраторах уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию центров уведомлений для iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/

<!--HONumber=49--> 