<properties urlDisplayName="Localized Breaking News" pageTitle="Использование концентраторов уведомлений для передачи локализованных экстренных новостей на устройства iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications (iOS)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news to iOS devices" authors="yuaxu" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />
# Использование концентраторов уведомлений для передачи локализованных экстренных новостей на устройства iOS

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>


В этом разделе показано, как использовать функцию **template** концентраторов уведомлений Azure для рассылки уведомлений об экстренных новостях, локализованных для языка и устройства. В этом учебнике вы начнете с приложения Магазина Windows, созданного в учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] По завершении вы сможете регистрироваться для категорий, которые вас интересуют, указывать язык уведомлений и получать push-уведомления только для выбранных категорий на этом языке.

В этом учебнике рассматриваются следующие основные шаги для реализации данного сценария:

1. [Основные сведения о шаблонах]
2. [Пользовательский интерфейс приложения]
3. [Создание приложения iOS]
4. [Отправка уведомлений из серверной части]


Этот сценарий состоит из двух частей:

- приложение iOS позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей;

- сервер рассылает уведомления с помощью функций **tag** и **template** концентраторов уведомлений Azure.



##Предварительные требования ##

Необходимо выполнить задания учебника [Использование концентраторов уведомлений для передачи экстренных новостей], чтобы получить созданный в нем код, поскольку задания данного учебника основаны непосредственно на нем.

Нужен также Visual Studio 2012.



<h2><a name="concepts"></a>Основные сведения о шаблонах</h2>

В учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] вы построили приложение, которое использовало **теги** для подписки на уведомления для различных категорий новостей.
Однако многие приложения ориентированы на несколько рынков и требуют локализации. Это означает, что само содержимое уведомлений должно быть локализовано и доставлено в правильный набор устройств.
В этом разделе будут продемонстрированы способы использования функции концентраторов уведомлений **template**, которая позволяет легко доставлять локализованные экстренные уведомления.

Примечание. Один из способов отправки локализованных уведомлений - создание нескольких версий каждого тега. Например, для поддержки английского, французского и китайского нам понадобится три разных тега для мировых новостей: world_en, world_fr и world_ch. Затем необходимо отправить локализованную версию мировых новостей по каждому из этих тегов. В этом разделе мы используем шаблоны во избежание избыточного количества тегов и необходимости отправки нескольких сообщений.

В общих чертах, с помощью шаблонов можно указать, как конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В нашем случае мы отправим независимое от языка сообщение, которое содержит все поддерживаемые языки:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Затем мы убедимся, что устройство зарегистрировано с шаблоном, который ссылается на нужное свойство. Например, приложение iOS, которому необходимо зарегистрироваться для получения приложений для французского языка, зарегистрирует следующее:

	{
		aps:{
			alert: "$(News_French)"
		}
	}

Шаблоны - это очень мощная функция, о них можно узнать в [Руководстве по использованию концентраторов уведомлений]. Справки по языку выражения шаблона см. в статье [Практическое руководство. Концентраторы уведомлений Service Bus (приложения iOS)].

<h2><a name="ui"></a>Пользовательский интерфейс приложения</h2>

Теперь изменим приложение Breaking News, созданное в разделе [Использование концентраторов уведомлений для передачи экстренных новостей] для отправки локализованных экстренных новостей с помощью шаблонов.


Добавьте в MainStoryboard_iPhone.storyboard сегментированный элемент управления с тремя поддерживаемыми нами языками: английским, французским и мандаринским китайским.

![][13]

Затем обязательно добавьте IBOutlet в свой файл ViewController.h, как показано ниже:

![][14]

<h2><a name="building-client"></a><span class="building app">Пользовательский интерфейс приложения</span>Создание приложения iOS</h2>

Чтобы адаптировать приложения клиента для получения локализованных сообщений, необходимо заменить *собственные* регистрации (т. е. регистрации с указанием шаблона) на регистрации шаблона.

1. В файле Notification.h добавьте метод *retrieveLocale* и измените методы хранения и подписки следующим образом:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	В файле Notification.m измените метод *storeCategoriesAndSubscribe*, добавив параметр локали и сохранив его в пользовательских параметрах по умолчанию:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	Затем измените метод *Subscribe* таким образом, чтобы в него входила локаль:

		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }

		    NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	Теперь мы используем метод *registerTemplateWithDeviceToken* вместо *registerNativeWithDeviceToken*. При регистрации шаблона необходимо предоставить шаблон json, а также имя шаблона (поскольку для нашего приложения может возникнуть необходимость зарегистрировать различные шаблоны). Не забудьте зарегистрировать свои категории в качестве тегов, чтобы потом получать уведомления об этих новостях.

	Наконец добавьте метод для извлечения языкового стандарта из пользовательских параметров по умолчанию:

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

3. Теперь, когда мы изменили класс Notifications, надо убедиться, что наш ViewController использует новый UISegmentControl. Добавьте следующую строку в метод *viewDidLoad*, чтобы обеспечить показ текущей выбранной локали:

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	Затем измените в методе *Subscribe* вызов *storeCategoriesAndSubscribe* следующим образом:

		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

4. И наконец, остается обновить метод *didRegisterForRemoteNotificationsWithDeviceToken* в файле AppDelegate.m таким образом, чтобы можно было корректно обновить регистрацию при запуске приложения. Измените вызов метода уведомлений *Subscribe* следующим образом:

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

<h2><a name="send"></a>Отправка уведомлений из серверной части</h2>

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]


## Дальнейшие действия

Дополнительные сведения об использовании шаблонов см. по следующим ссылкам:

- [ Уведомление пользователей с помощью концентраторов уведомлений: ASP.NET]
- [ Уведомление пользователей с помощью концентраторов уведомлений: мобильные службы]
- [Руководства по использованию концентраторов уведомлений]

Ссылка на язык выражений шаблонов находится в разделе [Справочник по концентраторам уведомлений для iOS].




<!-- Anchors. -->
[Основные сведения о шаблонах]: #concepts
[Пользовательский интерфейс приложения]: #ui
[Создание приложения iOS]: #building-client
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]: #next-steps

<!-- Images. -->









[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[Практическое руководство. Использование концентраторов уведомлений Service Bus (приложения iOS)]: http://msdn.microsoft.com/ru-ru/library/jj927168.aspx
[Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-ios
[Мобильная служба]: /ru-ru/develop/mobile/tutorials/get-started
[ Уведомление пользователей с помощью концентраторов уведомлений: ASP.NET]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[ Уведомление пользователей с помощью концентраторов уведомлений: мобильные службы]: /ru-ru/manage/services/notification-hubs/notify-users
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js.md

[Портал управления Azure]: https://manage.windowsazure.com/
[Этапы регистрации Windows Developer Preview для мобильных служб]: ../HowTo/mobile-services-windows-developer-preview-registration.md
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководства по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для iOS]: http://msdn.microsoft.com/ru-ru/library/jj927168.aspx
