<properties
	pageTitle="Приступая к работе с концентраторами уведомлений Azure"
	description="Узнайте, как использовать центры уведомлений Azure для push-уведомлений."
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
	ms.topic="hero-article" 
	ms.date="05/27/2015"
	ms.author="wesmc"/>

# Приступая к работе с концентраторами уведомлений

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Обзор

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS. В этом учебнике вам предстоит создать пустое приложение iOS, получающее push-уведомления с помощью службы push-уведомлений Apple (APNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений.

##Предварительные требования

В работы с этим учебником необходимо следующее:

+ [Пакет SDK для мобильных служб для iOS]
+ [XCode 6][Install Xcode]
+ Устройство с iOS 8 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS

   >[AZURE.NOTE]В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными центрам уведомлений для приложений iOS.

> [AZURE.NOTE]Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Включение push-уведомлений Apple через центры уведомлений](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##Настройка концентратора уведомлений

В этом разделе приводятся пошаговые инструкции по созданию и настройке нового центра уведомлений с помощью созданного сертификата push-уведомлений. При необходимости использовать уже созданный центр уведомлений шаги со 2 по 5 можно пропустить.


1. В Keychain Access щелкните правой клавишей мыши новый сертификат push-уведомлений, созданный в категории **Сертификаты**. Нажмите **Экспорт**, задайте имя файла, выберите формат **.p12** и нажмите кнопку **Сохранить**.

    ![][1]

	Запишите имя файла и расположение экспортируемого сертификата.

	>[AZURE.NOTE]В этом учебнике создается файл Quickstart.p12. Имя файла и расположение могут отличаться.

2. Войдите на [портал управления Аzure] и нажмите элемент **+СОЗДАТЬ** в нижней части экрана.

3. Последовательно щелкните элементы **Службы приложений**, **Сервисная шина**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][2]

4. Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][3]

5. Щелкните только что созданное пространство имен (обычно это ***имя центра уведомлений*-ns**), чтобы открыть ее панель мониторинга.

   	![][4]

6. Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

   	![][5]

7. Щелкните вкладку **Настройка** в верхней части, а затем щелкните кнопку **Отправить** в параметрах службы уведомлений Apple для отправки отпечатка сертификата. Выберите ранее экспортированный сертификат **.p12** и пароль для него. Обязательно укажите, следует ли использовать службу push-уведомлений **Рабочая среда** (если вы хотите отправлять push-уведомления пользователям, которые приобрели ваше приложение в Магазине) или **Песочница** (во время разработки).

   	![][6]

8. Щелкните расположенную сверху вкладку **Панель мониторинга**, затем щелкните **Просмотреть строку подключения**. Запишите две строки подключения.

   	![][7]

Центр уведомлений теперь настроен для работы с APNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

##Подключение приложения к концентратору уведомлений

1. В XCode создайте новый проект iOS и выберите шаблон **Приложение одного представления**.

   	![][8]

2. При настройке параметров нового проекта убедитесь, что используется то же **имя продукта** и **идентификатор организации**, которое использовалось при задании идентификатора пакета на портале Apple Development.

	![][11]

3. В разделе **Цели** щелкните имя проекта, затем щелкните вкладку **Параметры сборки** и разверните **Удостоверение подписи кода**, а затем в разделе **Отладка** задайте свое **Удостоверение подписи кода**. Переключите **Уровни** со значения **Basic** на значение **Все** и для параметра **Профиль подготовки** задайте ранее созданный профиль подготовки.

	Если вы не видите новый профиль подготовки, созданный в XCode, попробуйте обновить профили для своего удостоверения подписи, щелкнув **XCode** в строке меню, а затем щелкните **Предпочтения**, вкладка **Учетная запись**, кнопка **Просмотр сведений**, щелкните свое удостоверение подписи и щелкните "Обновить" в нижнем правом углу.

   	![][9]

4. Загрузите **версию 1.2.4** [пакета SDK для мобильных служб для iOS] и распакуйте архив. В XCode щелкните правой кнопкой мыши проект и выберите параметр **Добавить файлы в**, чтобы добавить папку **WindowsAzureMessaging.framework** в свой проект XCode. Выберите **Копировать элементы при необходимости**, затем щелкните **Добавить**.

   	![][10]

5. Откройте файл AppDelegate.h и добавьте следующую директиву импорта:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. В файле AppDelegate.m добавьте следующий код в метод `didFinishLaunchingWithOptions` в зависимости от используемой версии iOS. Этот код регистрирует маркер вашего устройства в APNS:

	Для iOS 8:

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	Для версий iOS до 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


7. В том же файле добавьте следующие методы и замените буквенные заполнители строки своим *именем центра* и значением *DefaultListenSharedAccessSignature*, которые были записаны ранее. Этот код передает маркер устройства центру уведомлений, чтобы центр уведомлений мог отправлять уведомления:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<Enter your listen connection string>"
										notificationHubPath:@"<Enter your hub name>"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
				else {
				    [self MessageBox:@"Registration Status" message:@"Registered"];
				}
	    	}];
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}


8. В том же файле добавьте следующий метод для отображения **UIAlert**, если уведомление получено при активном приложении:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

8. Соберите и запустите приложение на устройстве, чтобы проверить отсутствие сбоев.

## Как отправлять уведомления


Можно проверить получение уведомлений в приложении, отправив уведомления на портале Azure с помощью вкладки отладки центра уведомлений, как показано на следующем экране.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. В XCode откройте Main.storyboard и добавьте следующие компоненты пользовательского интерфейса из библиотеки объектов, чтобы разрешить пользователю отправлять push-уведомления в приложении.

	- Метка без текста метки. Она будет использоваться для оповещения об ошибках при отправке уведомлений. Свойству **Строки** присвойте значение **0** для автоматического ограничения размера правого и левого полей и верхней части представления.
	- Текстовому полю с текстом **Заполнитель** присвойте значение **Ввести сообщение уведомления**. Ограничьте поле сразу под меткой, как показано ниже. Установите контроллер представлений в качестве выходного делегата.
	- Кнопка **Отправить уведомление** ограничивается сразу под текстовым полем и горизонтально по центру.

	Представление будет выглядеть следующим образом:

	![][32]


2. Откройте свой файл ViewController.h файл и добавьте следующие инструкции `#import` и `#define`. Замените буквенную строку заполнителя своей фактической строкой соединения *DefaultFullSharedAccessSignature* и *именем центра*.


		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"


3. Добавьте выходы для метки и текстового поля, подключенные к вашему представлению, и обновите свое определение `interface` для поддержки `UITextFieldDelegate` и `NSXMLParserDelegate`. Добавьте три нижеуказанных объявления свойств для обеспечения вызова API REST и синтаксического анализа ответа.

	Ваш файл ViewController.h должен выглядеть следующим образом:

		#import <UIKit/UIKit.h>
		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging.
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end


4. Откройте ViewController.m и добавьте следующий код, чтобы проанализировать свою строку подключения *DefaultFullSharedAccessSignature*. Как уже говорилось в [Справочнике по API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), проанализированная информация будет использоваться при создании маркера SAS для заголовка запроса *Авторизация*.

		NSString *HubEndpoint;
		NSString *HubSasKeyName;
		NSString *HubSasKeyValue;

		-(void)ParseConnectionString
		{
			NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
			NSString *part;

			if ([parts count] != 3)
			{
				NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
					reason:@"Invalid full shared access connection string" userInfo:nil];

				@throw parseException;
			}

			for (part in parts)
			{
				if ([part hasPrefix:@"Endpoint"])
				{
					HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
				}
				else if ([part hasPrefix:@"SharedAccessKeyName"])
				{
					HubSasKeyName = [part substringFromIndex:20];
				}
				else if ([part hasPrefix:@"SharedAccessKey"])
				{
					HubSasKeyValue = [part substringFromIndex:16];
				}
			}
		}

5. В файле ViewController.m обновите метод `viewDidLoad`, чтобы выполнить синтаксический анализ строки подключения при загрузке представления. Также добавьте служебные методы, как показано ниже.


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
		}

		-(NSString *)CF_URLEncodedString:(NSString *)inputString
		{
		   return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
				NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}





6. В файле ViewController.m добавьте следующий код для создания маркера авторизации SAS, который будет предоставляться в заголовке *Авторизация*, как указано в [Справочнике по API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

		-(NSString*) generateSasToken:(NSString*)uri
		{
			NSString *targetUri;
			NSString* utf8LowercasedUri = NULL;
			NSString *signature = NULL;
			NSString *token = NULL;

			@try
			{
				// Add expiration
				uri = [uri lowercaseString];
				utf8LowercasedUri = [self CF_URLEncodedString:uri];
				targetUri = [utf8LowercasedUri lowercaseString];
				NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
				int expiresInMins = 60; // 1 hour
				expiresOnDate += expiresInMins * 60;
				UInt64 expires = trunc(expiresOnDate);
				NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

				// Get an hmac_sha1 Mac instance and initialize with the signing key
				const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
				const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
				unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
				CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
				NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
				signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

				// construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sr=%@&sig=%@&se=%qu&skn=%@",
					targetUri, signature, expires, HubSasKeyName];
			}
			@catch (NSException *exception)
			{
				[self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
			}
			@finally
			{
				if (utf8LowercasedUri != NULL)
					CFRelease((CFStringRef)utf8LowercasedUri);
				if (signature != NULL)
				CFRelease((CFStringRef)signature);
			}

			return token;
		}


7. Используйте **Ctrl+перетащить** из кнопки **Отправить уведомление** в ViewController.m, чтобы добавить действие для события **Touch Down**, которое выполняет вызов API REST, используя следующий код.

		- (IBAction)SendNotificationMessage:(id)sender
		{
			self.sendResults.text = @"";
			[self SendNotificationRESTAPI];
		}

		- (void)SendNotificationRESTAPI
		{
		    NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

			// Apple Notification format of the notification message
		    NSString *json = [NSString stringWithFormat:@"{"aps":{"alert":"%@"}}",
								self.notificationMessage.text];

			// Construct the messages REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generated the token to be used in the authorization header.
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNS notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify apple notification format
			[request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

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
		        	xmlParser = [[NSXMLParser alloc] initWithData:data];
		        	[xmlParser setDelegate:self];
		       		[xmlParser parse];
		    	}
		    }];
		    [dataTask resume];
		}


8. В ViewController.m добавьте следующий метод делегата, чтобы обеспечить закрытие клавиатуры для текстового поля. Используйте **Ctrl+перетащить** из текстового поля на значок контроллера представления в конструкторе интерфейса, чтобы установить контроллер представления в качестве делегата выхода.

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


9. В ViewController.m добавьте следующие методы делегата для поддержки анализа ответа с помощью `NSXMLParser`.

		//===[ Implement NSXMLParserDelegate methods ]===

		-(void)parserDidStartDocument:(NSXMLParser *)parser
		{
		    self.statusResult = @"";
		}

		-(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
			namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
			attributes:(NSDictionary *)attributeDict
		{
		    NSString * element = [elementName lowercaseString];
		    NSLog(@"*** New element parsed : %@ ***",element);

		    if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
		    {
		        self.currentElement = element;
		    }
		}

		-(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
		{
		    self.statusResult = [self.statusResult stringByAppendingString:
		        [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
		}

		-(void)parserDidEndDocument:(NSXMLParser *)parser
		{
			// Set the status label text on the UI thread
			dispatch_async(dispatch_get_main_queue(),
			^{
				[self.sendResults setText:self.statusResult];
			});
		}



10. Соберите проект и убедитесь в отсутствии ошибок.



Все возможные виды полезных нагрузок уведомления можно найти в [Руководстве по программированию локальных и push-уведомлений] Apple.



##Тестирование приложения

Для тестирования push-уведомлений на iOS необходимо развернуть приложение на устройстве. Отправка push-уведомлений Apple посредством эмулятора iOS невозможна.

1. Запустите приложение и убедитесь в успешной регистрации, а затем нажмите **ОК**.

	![][33]

2. Коснитесь пространства внутри текстового поля, чтобы ввести сообщение уведомления. Нажмите кнопку **Отправить** на клавиатуре или кнопке **Отправить уведомление** в представлении, чтобы отправить сообщение уведомления.

	![][34]

3. Уведомление отправляется на все устройства, зарегистрированные для получения уведомлений.

	![][35]

В случае возникновения проблем или рекомендаций по улучшению тех или иных модулей чтения данного учебника оставьте свой комментарий ниже в разделе Disqus.


##Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства iOS. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений].



<!-- Images. -->

[1]: ./media/notification-hubs-ios-get-started/notification-hubs-export-cert-p12.png
[2]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal.png
[3]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal2.png
[4]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal.png
[5]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal2.png
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-connection-strings.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Пакет SDK для мобильных служб для iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[пакета SDK для мобильных служб для iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[портал управления Аzure]: https://manage.windowsazure.com/
[руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: notification-hubs-aspnet-backend-ios-notify-users.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-ios-send-breaking-news.md

[Руководстве по программированию локальных и push-уведомлений]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
 

<!---HONumber=July15_HO2-->