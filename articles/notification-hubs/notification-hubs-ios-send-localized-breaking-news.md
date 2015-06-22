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
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>
# Использование концентраторов уведомлений для вещания локализованных экстренных новостей на устройства iOS

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Магазин Windows C# </a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>

##Обзор

В этом разделе показано, как использовать функцию **template** центров уведомлений Azure для рассылки уведомлений об экстренных новостях, локализованных для языка и устройства. В этом учебнике вы начнете с приложения Магазина Windows, созданного в учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] По завершении вы сможете регистрировать для категорий, которые вас интересуют, указывать язык уведомлений, и получать push-уведомления только для выбранных категорий на этом языке.


Этот сценарий состоит из двух частей:

- приложение iOS позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей;

- сервер рассылает уведомления с помощью функций **tag** и **template** центров уведомлений Azure.



##Предварительные требования

Вы должны предварительно выполнить учебник [Использование концентраторов уведомлений для передачи экстренных новостей], чтобы у вас был нужный код, так как этот учебник построен непосредственно на этом коде.

Нужен также Visual Studio 2012.



##Основные сведения о шаблонах

В учебнике [Использование концентраторов уведомлений для передачи экстренных новостей] вы построили приложение, которое использовало **теги** для подписки на уведомления для различных категорий новостей.
Однако многие приложения ориентированы на несколько рынков и требуют локализации. Это означает, что само содержимое уведомлений должно быть локализовано и доставлено в правильный набор устройств.
В этом разделе будут продемонстрированы способы использования функции центров уведомлений **template**, которая позволяет легко доставлять локализованные экстренные уведомления.

Примечание. Один из способов отправки локализованных уведомлений - создание нескольких версий каждого тега. Например, для поддержки английского, французского и китайского нам понадобится три разных тега для мировых новостей: world_en, world_fr и world_ch. Затем необходимо отправить локализованную версию мировых новостей по каждому из этих тегов. В этом разделе мы используем шаблоны во избежание избыточного количества тегов и необходимости отправки нескольких сообщений.

В общих чертах, с помощью шаблонов можно указать, как конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В нашем случае мы отправим независимое от языка сообщение, которое содержит все поддерживаемые языки:

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

Шаблоны - это очень мощная функция, о них можно узнать в [Руководстве по использованию центров уведомлений]. Справки по языку выражения шаблона см. в статье [Практическое руководство. Использование концентраторов уведомлений Service Bus (приложения iOS)].

##Пользовательский интерфейс приложения

Теперь изменим приложение "Экстренные новости", созданное в разделе [Использование концентраторов уведомлений для передачи экстренных новостей] для отправки локализованных экстренных новостей с помощью шаблонов.


Добавьте в MainStoryboard_iPhone.storyboard сегментированный элемент управления с тремя поддерживаемыми нами языками: английского, французского и китайского.

![][13]

Затем обязательно добавьте IBOutlet в свой файл ViewController.h, как показано ниже:

![][14]

##Создание приложения iOS

Чтобы адаптировать свои клиентские приложения для получения локализованных сообщений, вам нужно заменить *собственные* регистрации (т. е. регистрации с указанием шаблона) на регистрации шаблона.

1. Добавьте в файле Notification.h метод *retrieveLocale* и измените методы хранения и подписки, как показано ниже.

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	Измените в файле Notification.m метод *storeCategoriesAndSubscribe*, добавив параметр языка и сохранив его в пользовательских параметрах по умолчанию.

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	Затем измените метод *subscribe*, добавив в него языки.

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

	Обратите внимание, что мы теперь используем метод*registerTemplateWithDeviceToken*, а не *registerNativeWithDeviceToken*. При регистрации шаблона необходимо предоставить шаблон json, а также имя шаблона (поскольку для нашего приложения может возникнуть необходимость зарегистрировать различные шаблоны). Убедитесь, что категории регистрируются как теги, поскольку мы хотим получать уведомления об этих новостях.

	Наконец добавьте метод для извлечения языкового стандарта из пользовательских параметров по умолчанию:

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

3. Теперь, когда мы внесли изменения в наш класс уведомлений, необходимо убедиться в том, что наш ViewController использует новый UISegmentControl. Добавьте следующую строку в метод *viewDidLoad*, чтобы убедиться в том, что отображается выбранный язык.

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	Затем в методе *subscribe* измените вызов *storeCategoriesAndSubscribe*, как показано ниже.

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

4. И наконец, вам нужно обновить метод *didRegisterForRemoteNotificationsWithDeviceToken* в файле AppDelegate.m таким образом, чтобы можно было корректно обновить регистрацию при запуске приложения. Измените вызов метода уведомлений *subscribe* следующим образом.

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

##Отправка уведомлений из серверной части

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]


## Дальнейшие действия

Дополнительные сведения об использовании шаблонов см. по следующим ссылкам:

- [Уведомление пользователей с помощью центров уведомлений: ASP.NET]
- [Уведомление пользователей с помощью центров уведомлений: Мобильные службы]
- [Руководство по использованию концентраторов уведомлений]

Справочник по языку выражений шаблона см. в учебнике [Инструкции по использованию центров уведомлений для iOS].






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[Практическое руководство. Использование концентраторов уведомлений Service Bus (приложения iOS)]: http://msdn.microsoft.com/library/jj927168.aspx
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-ios
[Мобильная служба]: /develop/mobile/tutorials/get-started
[Уведомление пользователей с помощью центров уведомлений: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Уведомление пользователей с помощью центров уведомлений: Мобильные службы]: /manage/services/notification-hubs/notify-users
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с аутентификацией]: /develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /develop/mobile/tutorials/get-started-with-push-ios
[Push-уведомления для пользователей приложений]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Авторизация пользователей с помощью скриптов]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript и HTML]: ../get-started-with-push-js.md

[Портал управления Azure]: https://manage.windowsazure.com/
[Этапы регистрации Windows Developer Preview для мобильных служб]: ../mobile-services-windows-developer-preview-registration.md
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию центров уведомлений для iOS]: http://msdn.microsoft.com/library/jj927168.aspx

<!--HONumber=49--> 