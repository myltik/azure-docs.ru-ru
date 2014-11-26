<properties linkid="notification-hubs-ios-send-breaking-news" pageTitle="Notification Hubs Breaking News Tutorial - iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications to iOS devices." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Использование концентраторов уведомлений для передачи экстренных новостей

<div class="dev-center-tutorial-selector sublanding">

[Windows Universal][Windows Universal][Windows Phone][Windows Phone][iOS][iOS] [Android][Android]

</div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для рассылки уведомлений об экстренных новостях в приложение iOS. По завершении вы сможете зарегистрироваться в интересующих вас категориях экстренных новостей и получать push-уведомления только для этих категорий. Данный сценарий является общеупотребимым шаблоном для многих приложений, где требуется отправлять уведомления группам пользователей, ранее проявивших к ним интерес, например, программы чтения RSS, приложений для музыкальных фанатов и т. д.

Широковещательные сценарии реализуются путем включения одного или нескольких *тегов* при создании регистрации в концентраторе уведомлений. Если уведомления отправляются на тег, их получают все устройства, зарегистрированные для данного тега. Поскольку теги представляют собой обычные строки, их не нужно подготавливать заранее. Дополнительные сведения о тегах см. в разделе [Руководство по использованию концентраторов уведомлений][Руководство по использованию концентраторов уведомлений].

В этом учебнике рассматриваются следующие основные шаги для реализации данного сценария:

1.  [Добавление возможности выбора категорий в приложение][Добавление возможности выбора категорий в приложение]
2.  [Регистрация для использования уведомлений][Регистрация для использования уведомлений]
3.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]
4.  [Запуск приложения и создание уведомлений][Запуск приложения и создание уведомлений]

Материал данного раздела основан на приложении, созданном в разделе [Приступая к работе с концентраторами уведомлений][Приступая к работе с концентраторами уведомлений]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с концентраторами уведомлений][Приступая к работе с концентраторами уведомлений].

## <a name="adding-categories"></a>Добавление возможности выбора категорий в приложение

Прежде всего, необходимо добавить элементы пользовательского интерфейса для имеющейся раскадровки, позволяющие пользователю выбирать категории для регистрации. Выбранные пользователем категории хранятся на устройстве. При запуске приложения в концентраторе уведомлений создается регистрация устройства с выбранными категориями, представленными в форме тегов.

1.  В вашем MainStoryboard\_iPhone.storyboard добавьте следующие компоненты из библиотеки объектов:

    -   метка с текстом "Экстренные новости";
    -   метки с текстами категории "Мир", "Политика", "Бизнес", "Технология", "Наука", "Спорт";
    -   шесть переключателей по категориям;
    -   кнопка с надписью "Подписка".

    Раскадровка должна выглядеть следующим образом:

    ![][0]

2.  В редакторе помощника создайте выходы для всех переключателей и назовите их WorldSwitch, PoliticsSwitch, BusinessSwitch, TechnologySwitch, ScienceSwitch, SportsSwitch.

    ![][1]

3.  Создайте действие для кнопки с названием "Подписка". BreakingNewsViewController.h должна содержать следующие элементы:

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4.  Создайте новый класс под названием `Notifications`. Скопируйте следующий код в интерфейсную часть файла Notifications.h:

        @property NSData* deviceToken;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
        - (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

5.  Добавьте следующую директиву импорта в Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6.  Скопируйте следующий код в реализационную часть файла Notifications.m:

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

7.  В приведенном выше коде замените заполнители `<hub name>` и `<connection string with listen access>` именем концентратора уведомлений и строкой подключения для элемента *DefaultListenSharedAccessSignature*, полученными ранее.

    <div class="dev-callout">

    **Примечание.**
    Поскольку учетные данные, которые распространяются с помощью клиентского приложения, обычно небезопасны, с помощью вашего клиентского приложения следует распространять только ключ для доступа к прослушиванию. Доступ к прослушиванию позволяет приложению регистрироваться для использования уведомлений, однако при этом нельзя изменять имеющиеся регистрации и отправлять уведомления. Для отправки уведомлений и изменения существующих регистраций используется ключ полного доступа в защищенной серверной службе.

    </div>

8.  В файле BreakingNewsAppDelegate.h добавьте следующее свойство:

        @property (nonatomic) Notifications* notifications;

    Это создает единственный экземпляр класса уведомлений в AppDelegate.

9.  В методе **didFinishLaunchingWithOptions** в файле BreakingNewsAppDelegate.m добавьте следующий код перед **registerForRemoteNotificationTypes**:

        self.notifications = [[Notifications alloc] init];

    Инициализирует одноэлементное уведомление.

10. В методе **didRegisterForRemoteNotificationsWithDeviceToken** в файле BreakingNewsAppDelegate.m удалите вызов **registerNativeWithDeviceToken** и добавьте следующий код:

        self.notifications.deviceToken = deviceToken;

    Обратите внимание, что на данном этапе не должно быть никакого другого кода в методе **didRegisterForRemoteNotificationsWithDeviceToken**.

11. Добавьте следующий метод в BreakingNewsAppDelegate.m:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

    Этот метод обрабатывает уведомления, полученные при запуске приложения, отображая простой **UIAlert**.

12. В BreakingNewsViewController.m скопируйте следующий код в созданный при помощи XCode метод **подписки**:

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

    Этот метод создает список категорий **NSMutableArray** и использует класс **Notifications** для хранения списка в локальном хранилище и регистрации соответствующих тегов в концентраторе уведомлений. При изменении категорий регистрация создается заново с новыми категориями.

Ваше приложение теперь может сохранять набор категорий в локальном хранилище на устройстве и регистрироваться в концентраторе уведомлений всякий раз, когда пользователь изменяет выбранные категории.

## <a name="register"></a>Регистрация для использования уведомлений

Эти действия позволяют зарегистрироваться в концентраторе уведомлений при запуске с использованием категорий, сохраненных в локальном хранилище.

<div class="dev-callout">

**Примечание.**
Поскольку маркер устройства, назначенный службой push-уведомлений Apple (APNS), может измениться в любое время, следует регулярно производить регистрацию для использования уведомлений, чтобы предотвратить сбои уведомлений. В этом примере регистрация для использования уведомлений осуществляется при каждом запуске приложения. Для приложений, которые запускаются чаще одного раза в день, в целях экономии пропускной способности регистрацию можно пропустить, если с момента предыдущей регистрации прошло менее одного дня.

</div>

1.  Добавьте следующий метод в интерфейсную часть файла Notifications.h:

        - (NSSet*)retrieveCategories;

    Этот код извлекает категории в классе уведомлений.

2.  Добавьте соответствующую реализацию в файл Notifications.m:

        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }

3.  Добавьте следующий код в метод **didRegisterForRemoteNotificationsWithDeviceToken**:

        Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];
        [notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    Это гарантирует, что при каждом запуске приложения оно извлекает категории из локального хранилища и запрашивает для них регистрацию.

4.  В BreakingNewsViewController.h добавьте следующий код в метод **viewDidLoad**:

        Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

    При этом пользовательский интерфейс при запуске обновляется в зависимости от состояния ранее сохраненных категорий.

Теперь приложение готово и может сохранять набор категорий в локальном хранилище устройств и использовать его для регистрации в концентраторе уведомлений всякий раз, когда пользователь изменяет выбранные категории. А сейчас определим серверную часть, которая может отправлять уведомления категорий в это приложение.

## <a name="send"></a><span class="short-header">Отправка уведомлений</span>Отправка уведомлений из серверной части

</h2>
[WACOM.INCLUDE [концентраторы-уведомлений-серверная-часть](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>Запуск приложения и создание уведомлений

1.  Нажмите клавишу "Запуск" для построения проекта, после чего запустите приложение.

    ![][2]

    Обратите внимание, что в пользовательском интерфейсе присутствует набор переключателей, позволяющий выбрать категории для подписки.

2.  Включите переключатель для одной или нескольких категорий, а затем щелкните по элементу **Подписаться**.

    Если выбрано **Подписка**, приложение преобразует выбранные категории в теги и запрашивает у концентратора уведомлений новую регистрацию устройств для выбранных тегов.

3.  Отправьте новое уведомление из серверной части одним из следующих способов.

    -   **Консольное приложение:** запустите консольное приложение.

    -   **Java/PHP:** запустите свое приложение/сценарий.

4.  Уведомления для выбранных категорий отображаются в виде всплывающих уведомлений.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике мы рассмотрели, как производить рассылку экстренных новостей по категориям. Далее вам рекомендуется изучить один из следующих учебников, в которых рассматриваются более сложные сценарии использования концентраторов уведомлений:

-   **[раздел Использование концентраторов уведомлений для передачи локализованных экстренных новостей][раздел Использование концентраторов уведомлений для передачи локализованных экстренных новостей]**

    Узнайте, как расширить возможности приложения экстренных новостей для отправки локализованных уведомлений.

-   **[Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений]**

    Узнайте, как рассылать push-уведомления определенным пользователям, прошедшим проверку подлинности. Это хорошее решение для отправки уведомлений только определенным пользователям.



  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/ "Windows Universal"
  [Windows Phone]: /ru-ru/documentation/articles/notification-hubs-windows-phone-send-breaking-news/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-send-breaking-news/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/ "Android"
  [Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
  [Добавление возможности выбора категорий в приложение]: #adding-categories
  [Регистрация для использования уведомлений]: #register
  [Отправка уведомлений из серверной части]: #send
  [Запуск приложения и создание уведомлений]: #test-app
  [Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios/
  [0]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
  [1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png
  [2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
  [раздел Использование концентраторов уведомлений для передачи локализованных экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users/
