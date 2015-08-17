<properties 
	pageTitle="Передача локализованных экстренных новостей на устройства iOS с помощью центров уведомлений" 
	description="Узнайте, как использовать центры уведомлений Azure Service Bus для отправки уведомлений о локализованных экстренных новостях (iOS)." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="wesmc"/>
# Использование концентраторов уведомлений для вещания локализованных экстренных новостей на устройства iOS

> [AZURE.SELECTOR]
- [Windows Store C#](notification-hubs-windows-store-dotnet-send-localized-breaking-news)
- [iOS](notification-hubs-ios-send-localized-breaking-news)


##Обзор

В этом разделе показано, как использовать функцию **template** концентраторов уведомлений Azure для рассылки уведомлений об экстренных новостях, локализованных для языка и устройства. В этом учебнике вы начнете с приложения Магазина Windows, созданного в учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] По завершении вы сможете регистрировать для категорий, которые вас интересуют, указывать язык уведомлений, и получать push-уведомления только для выбранных категорий на этом языке.


Этот сценарий состоит из двух частей:

- приложение iOS позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей;

- сервер рассылает уведомления с помощью функций **tag** и **template** концентраторов уведомлений Azure.



##Предварительные требования

Вы должны предварительно выполнить учебник [Использование концентраторов уведомлений для передачи экстренных новостей], чтобы у вас был нужный код, так как этот учебник построен непосредственно на этом коде.

Нужен также Visual Studio 2012.



##Основные сведения о шаблонах

В учебнике [Использование центров уведомлений для передачи экстренных новостей] вы создали приложение, которое использовало **теги** для подписки на уведомления для различных категорий новостей. Однако многие приложения ориентированы на несколько рынков и требуют локализации. Это означает, что само содержимое уведомлений должно быть локализовано и доставлено в правильный набор устройств. В этом разделе будут продемонстрированы способы использования **шаблонов** центров уведомлений, которые позволяют легко доставлять уведомления о локализованных экстренных новостях.

Примечание. Один из способов отправки локализованных уведомлений — создание нескольких версий каждого тега. Например, для поддержки английского, французского и китайского нам понадобится три разных тега для мировых новостей: "world\_en", "world\_fr" и "world\_ch". Затем необходимо отправить локализованную версию мировых новостей по каждому из этих тегов. В этом разделе мы используем шаблоны во избежание избыточного количества тегов и необходимости отправки нескольких сообщений.

В общих чертах, шаблоны представляют собой способ указать, как конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В нашем случае мы отправим независимое от языка сообщение, которое содержит все поддерживаемые языки:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Затем мы убедимся, что устройство зарегистрировано с шаблоном, который ссылается на нужное свойство. Например, приложением iOS, которое требуется зарегистрировать для французских новостей, будет зарегистрировано следующее:

	{
		aps:{
			alert: "$(News_French)"
		}
	}

Шаблоны — это очень мощная функция, о них можно узнать в [Руководстве по использованию концентраторов уведомлений]. Справки по языку выражений шаблона см. в учебнике [Практическое руководство. Центры уведомлений Service Bus (приложения iOS)].

##Пользовательский интерфейс приложения

Теперь изменим приложение "Экстренные новости", созданное в разделе [Использование концентраторов уведомлений для передачи экстренных новостей] для отправки локализованных экстренных новостей с помощью шаблонов.


В своем файле MainStoryboard\_iPhone.storyboard добавьте Segmented Control для трех поддерживаемых языков: английского, французского и китайского.

![][13]

Затем обязательно добавьте IBOutlet в свой файл ViewController.h, как показано ниже:

![][14]

##Создание приложения iOS

Для адаптации приложений клиента для получения локализованных сообщений необходимо заменить *собственные* регистрации (т. е. регистрации с указанием шаблона) на регистрации шаблона.

1. Добавьте в свой файл Notification.h метод *retrieveLocale* и измените методы хранения и подписки, как показано ниже.

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	Измените в файле Notification.m метод *storeCategoriesAndSubscribe*, добавив параметр языка и сохранив его в пользовательских параметрах по умолчанию:

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	Затем измените метод *subscribe*, чтобы включить языковой стандарт:

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

		    NSString* template = [NSString stringWithFormat:@"{"aps":{"alert":"$(News_%@)"},"inAppMessage":"$(News_%@)"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	Обратите внимание на то, что мы теперь используем метод *registerTemplateWithDeviceToken*, а не *registerNativeWithDeviceToken*. При регистрации шаблона необходимо предоставить шаблон json, а также имя шаблона (поскольку для нашего приложения может возникнуть необходимость зарегистрировать различные шаблоны). Убедитесь, что категории регистрируются как теги, поскольку мы хотим получать уведомления об этих новостях.

	Наконец добавьте метод для извлечения языкового стандарта из пользовательских параметров по умолчанию:

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

3. Теперь, когда мы внесли изменения в наш класс уведомлений, необходимо убедиться в том, что наш ViewController использует новый UISegmentControl. Добавьте следующую строку в метод *viewDidLoad*, чтобы убедиться в том, что отображается языковой стандарт, выбранный в данный момент:

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	Затем в методе *subscribe* замените свой вызов *storeCategoriesAndSubscribe* следующим кодом:

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

4. И наконец, необходимо обновить метод *didRegisterForRemoteNotificationsWithDeviceToken* в методе AppDelegate.m, чтобы правильно обновить регистрацию при запуске приложения. Замените вызов метода уведомления *subscribe* следующим кодом:

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

##Отправка локализованных уведомлений из серверной части

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]


## Дальнейшие действия

Дополнительные сведения об использовании шаблонов см. по следующим ссылкам:

- [Уведомление пользователей с помощью центров уведомлений: ASP.NET]
- [Уведомление пользователей с помощью центров уведомлений: мобильные службы]
- [Руководство по использованию центров уведомлений]

Справочник по языку выражений шаблона см. в учебнике [Инструкции по использованию концентраторов уведомлений для iOS].






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[Практическое руководство. Центры уведомлений Service Bus (приложения iOS)]: http://msdn.microsoft.com/library/jj927168.aspx
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-ios
[Использование центров уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Уведомление пользователей с помощью центров уведомлений: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Уведомление пользователей с помощью центров уведомлений: мобильные службы]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Руководство по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для iOS]: http://msdn.microsoft.com/library/jj927168.aspx
 

<!---HONumber=August15_HO6-->