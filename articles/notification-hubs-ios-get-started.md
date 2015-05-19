<properties 
	pageTitle="Приступая к работе с концентраторами уведомлений Azure" 
	description="Узнайте, как использовать центры уведомлений Azure для push-уведомлений." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# Приступая к работе с концентраторами уведомлений

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Обзор

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS. В этом учебнике вам предстоит создать пустое приложение iOS, получающее push-уведомления с помощью службы push-уведомлений Apple \(APNS\). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений.

##Предварительные требования

В работы с этим учебником необходимо следующее:

+ [SDK мобильных служб для iOS]
+ [XCode 4.5][Install Xcode]
+ Устройство с iOS 5.0 \(или более поздней версии\)
+ Участие в программе для разработчиков на платформе iOS

   \>[AZURE.NOTE]В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS \(iPhone или iPad\), а не в эмуляторе.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений iOS.

> [AZURE.NOTE]Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Включение push-уведомлений Apple](../includes/enable-apple-push-notifications.md)]

##Настройка концентратора уведомлений

1. В Keychain Access щелкните правой клавишей мыши по новому сертификату **Мои сертификаты** приложения быстрого запуска. Нажмите **Экспорт**, задайте имя файла, выберите формат **.p12** и нажмите кнопку **Сохранить**.

    ![][26]

  Запишите имя файла и расположение экспортируемого сертификата.

>[AZURE.NOTE]В этом учебнике создается файл Quickstart.p12. Имя файла и расположение могут отличаться.

2. Войдите на [портал управления Аzure] и нажмите элемент **+СОЗДАТЬ** в нижней части экрана.

3. Последовательно щелкните элементы **Службы приложений**, **Сервисная шина**, **Концентратор уведомлений** и **Быстрое создание**.

   ![][27]

4. Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   ![][28]

5. Выберите недавно созданное пространство имен \(обычно это ***имя концентратора уведомлений*-ns**\) и щелкните расположенную сверху вкладку **Настройка**.

   ![][29]

6. Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

   ![][210]

7. Откройте расположенную сверху вкладку **Настройка**, а затем щелкните элемент **Загрузить** для параметров службы уведомлений Apple. Выберите ранее экспортированный сертификат **.p12** и пароль для него. Обязательно укажите, следует ли использовать службу push-уведомлений **Рабочая среда** \(если вы хотите отправлять push-уведомления пользователям, которые приобрели ваше приложение в Магазине\) или **Песочница** \(во время разработки\).

   ![][211]

8. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Сведения о подключении**. Запишите две строки подключения.

   ![][212]

Концентратор уведомлений теперь настроен для работы с APNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

##Подключение приложения к концентратору уведомлений

1. В XCode создайте новый проект iOS и выберите шаблон **Приложение одного представления**.

   ![][31]

2. В разделе **Цели** нажмите имя проекта, разверните **Удостоверения подписи кода**, а затем в разделе **Отладка** выберите профиль удостоверения подписи кода. Кроме того, при использовании более новой версии XCode переключите параметр **Levels** \(Уровни\) с **Basic** \(Базовые\) на **All** \(Все\) и задайте для элемента строки **Provisioning Profile** \(Профиль подготовки\) профиль подготовки.

   ![][32]

3. Загрузите пакет Azure Mobile SDK для iOS. Откройте ZIP-файл и перетащите папку WindowsAzureMessaging.framework в папку Framework в проекте XCode. Выберите **Copy items in destination group's folder** \(Копировать элементы в папку конечной группы\) и нажмите кнопку **ОК**.

   ![][33]

4. В файле AppDelegate.h добавьте следующий каталог импорта:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. В файле AppDelegate.m добавьте следующий код в метод `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	Для iOS 8:
   
	 UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound \| UIUserNotificationTypeAlert \| UIUserNotificationTypeBadge categories:nil];
 
    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

6. В том же файле добавьте следующий метод:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *\(Необязательно\)* В том же самом файле добавьте следующий метод для отображения **UIAlert**, если уведомление получено при активном приложении:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

8. Запустите приложение на устройстве.

##Отправка уведомления из серверной части

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью [интерфейса REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx). В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Примеры отправки уведомлений из серверных компонентов мобильных служб Azure, интегрированных с центрами уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** \([Серверная часть .NET](mobile-services-javascript-backend-ios-get-started-push.md) \| [Серверная часть JavaScript](mobile-services-javascript-backend-ios-get-started-push.md)\). Примеры отправки уведомлений с использованием API REST см. в статье **Использование центров уведомлений из Java или PHP** \([Java](notification-hubs-java-backend-how-to.md) \| [PHP](notification-hubs-php-backend-how-to.md)\).

1. В Visual Studio из меню **Файл** последовательно выберите **Создать** и **Проект...**, затем в области **Visual C\#** нажмите **Windows** и **Консольные приложения**, затем нажмите кнопку **OK**.  

   	![][20]

	Будет создан новый проект консольного приложения.

2. В меню **Инструменты** последовательно выберите **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**.

	Отобразится консоль диспетчера пакетов.

6. В окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus

	В результате будет добавлена ссылка на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета WindowsAzure.ServiceBus NuGet</a>.

5. Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

6. Добавьте в класс **Program** следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

	Обязательно замените заполнитель «hub name» на имя центра уведомлений, отображаемое на вкладке **Центры уведомлений** портала. Кроме того, замените заполнитель строки подключения на строку подключения с именем **DefaultFullSharedAccessSignature**, полученную в разделе «Настройка центра уведомлений».

	>[AZURE.NOTE]Обязательно используйте строку подключения с **полным** доступом, а не доступом к **прослушиванию**. У строки с доступом к прослушиванию отсутствуют разрешения для отправки уведомлений.

5. Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

5. Нажмите клавишу F5, чтобы запустить консольное приложение.

	На устройстве должно отобразиться оповещение. Если вы используете Wi-fi, убедитесь, что подключение работает.

Все возможные виды полезных нагрузок можно найти в [руководстве по программированию локальных и push-уведомлений] Apple.

##Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства iOS. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений].



<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[26]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[SDK мобильных служб для iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[портал управления Аzure]: https://manage.windowsazure.com/
[руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: notification-hubs-ios-mobile-services-register-user-push-notifications.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-ios-send-breaking-news.md

[руководстве по программированию локальных и push-уведомлений]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1


<!--HONumber=52-->
