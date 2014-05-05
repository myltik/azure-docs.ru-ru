<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Приступая к работе" pageTitle="Приступая к работе с концентраторами уведомлений Azure" metaKeywords="" description="Узнайте, как использовать концентраторы уведомлений Azure для работы с push-уведомлениями." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Приступая к работе с концентраторами уведомлений" authors="elioda" solutions="" manager="" editor="" />

# Приступая к работе с концентраторами уведомлений
<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>	

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS. 
В этом учебнике вам предстоит создать пустое приложение iOS, получающее push-уведомления с помощью службы push-уведомлений Apple (APNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание запроса подписи сертификата] 
2. [Регистрация приложения и включение push-уведомлений]
3. [Создание профиля подготовки для приложения]
4. [Настройка концентратора уведомлений]
5. [Подключение приложения к концентратору уведомлений]
6. [Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств. В работы с этим учебником необходимо следующее:

+ [SDK мобильных служб для iOS]
+ [XCode 4.5][Install Xcode] 
+ Устройство с iOS 5.0 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS

   <div class="dev-callout"><b>Примечание.</b>
   <p>В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.</p>
   </div>

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений iOS. 

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

Служба push-уведомлений Apple (APNS) использует сертификаты для аутентификации вашей мобильной службы. Выполните следующие действия, чтобы создать необходимые сертификаты и передать их в мобильную службу. Официальную документацию по APNS см. в разделе [Служба push-уведомлений Apple].



<h2><a name="certificates"></a><span class="short-header">Создание файла CSR</span>Создание файла запроса подписи сертификата</h2>

Сначала необходимо создать файл запроса подписи сертификата (с расширением CSR), используемый Apple для создания подписанного сертификата.

1. В папке "Служебные программы" запустите средство Keychain Access.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем щелкните **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации).
 
  	![][5]

3. Выберите ваши параметры **User Email Address** (Адрес электронной почты пользователя) и **Common Name** (Общее имя), убедитесь, что установлен флажок **Saved to disk** (Сохранено на диск), а затем щелкните **Continue** (Продолжить). Оставьте поле **CA Email Address** (Адрес электронной почты ЦС) пустым, поскольку оно не является обязательным.

  	![][6]

4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

  	![][7]
  
  При этом CSR-файл сохраняется в выбранном месте; по умолчанию — на рабочем столе. Запомните расположение, выбранное для этого файла.

Затем зарегистрируйте свое приложение в Apple, включите push-уведомления и передайте этот экспортированный CSR-файл для создания сертификата push-уведомлений.

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для получения push-уведомлений</h2>

Чтобы иметь возможность отправлять push-уведомления в приложение для iOS из мобильных служб, необходимо зарегистрировать ваше приложение в Apple, а также зарегистрировать его для получения push-уведомлений.  

1. Если ваше приложение еще не зарегистрировано, перейдите на <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портал подготовки iOS</a> в центре разработчиков Apple, выполните вход с использованием вашего идентификатора Apple, щелкните **Identifiers** (Идентификаторы), затем — **App IDs** (Идентификаторы приложений) и, наконец, щелкните знак **+**, чтобы зарегистрировать новое приложение.

   	![][B102] 

2. Введите имя приложения в поле **Description** (Описание), введите значение _MobileServices.Quickstart_ в поле **Bundle Identifier** (Идентификатор набора), выберите вариант "Push Notifications" (Push-уведомления) в разделе "App Services" (Службы приложений), а затем щелкните **Continue** (Продолжить). В этом примере используется идентификатор **MobileServices.Quickstart**, но вы не можете повторно использовать тот же идентификатор, поскольку идентификаторы приложений должны быть уникальными для всех пользователей. Поэтому рекомендуется добавить ваше полное имя или инициалы после имени приложения. 

   	![][B103]
   
   	При этом создается идентификатор вашего приложения и появляется запрос об **отправке** информации. Нажмите кнопку **Submit** (Отправить).
   
   	![][B104] 
   
   	После нажатия кнопки **Submit** (Отправить) вы увидите экран **Registration complete** (Регистрация выполнена), представленный ниже. Нажмите кнопку **Done** (Готово).
   
   	![][B105]

    > [WACOM.NOTE] Если вы хотите указать значение <strong>Bundle Identifier</strong> (Идентификатор набора), отличное от <i>MobileServices.Quickstart</i>, необходимо также обновить значение идентификатора набора в проекте Xcode.

3. Найдите созданный вами идентификатор приложения и щелкните его строку. 

   	![][B106]
   
   	Если щелкнуть идентификатор приложения, отобразятся сведения о приложении и идентификаторе приложения. Нажмите кнопку **Settings** (Параметры).
   
   	![][B107] 
   
4. Прокрутите до нижней части экрана и нажмите кнопку **Create Certificate...** (Создать сертификат...) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).

   	![][B108] 

   	Откроется помощник "Add iOS Certificate" (Добавление сертификата iOS).
   
   	![][B108] 


    > [WACOM.NOTE] В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Просто убедитесь, что задается тот же тип сертификата при отправке сертификата в мобильные службы.

5. Щелкните **Choose File** (Выбрать файл), перейдите к папке, где был сохранен CSR-файл, созданный в первом задании, а затем щелкните **Generate** (Создать). 

  	![][B110]
  
6. После создания сертификата с помощью портала нажмите кнопку **Download** (Загрузить) и **Done** (Готово).
 
  	![][B111]  

   	При этом сертификат подписи загружается и сохраняется на вашем компьютере в папке "Загрузки". 

  	![][B9] 

    > [WACOM.NOTE] По умолчанию загруженный файл сертификата разработки называется **aps_development.cer**.

7. Дважды щелкните загруженный сертификат push-уведомлений **aps_development.cer**.

   	При этом новый сертификат устанавливается в Keychain, как показано ниже:

   	![][B10]

    > [WACOM.NOTE] Имя вашего сертификата может отличаться, но оно будет начинаться с префикса **Apple Development iOS Push Notification Services:**.

Позже этот сертификат будет использоваться для создания файла .p12 и отправки его в мобильные службы для включения проверки подлинности с помощью APNS.


<h2><a name="profile"></a><span class="short-header">Подготовка приложения</span>Создание профиля подготовки для приложения</h2>
 
1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Provisioning Profiles** (Профили подготовки), затем выберите **All** (Все) и нажмите кнопку **+**, чтобы создать новый профиль. Отображается мастер **Add iOS Provisioning Profile** (Добавление профиля подготовки для iOS).

   	![][120]

2. Выберите **iOS App Development** (Разработка приложений для iOS) в разделе **Development** (Разработка) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

   	![][121]

3. Затем выберите идентификатор приложения для приложения быстрого начала работы с мобильными службами в раскрывающемся списке **App ID** (Идентификатор приложения) и нажмите кнопку **Continue** (Продолжить).

   	![][122]

4. На экране **Select certificates** (Выбор сертификатов) выберите ранее созданный сертификат и нажмите кнопку **Continue** (Продолжить).
  
   	![][123]

5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).

   	![][124]

6. Наконец, выберите имя профиля в поле **Profile Name** (Имя профиля), щелкните **Generate** (Создать) и нажмите кнопку **Done** (Готово).

   	![][125]
   
   	![][126]
	
	В результате создается новый профиль подготовки.

7. В Xcode откройте "Organizer" (Диспетчер), выберите представление "Devices" (Устройства), выберите **Provisioning Profiles** (Профили подготовки) в разделе **Library** (Библиотека) на левой панели и импортируйте недавно созданный профиль подготовки.

8. В левой части окна выберите устройство и снова импортируйте профиль подготовки. 

9. В Keychain Access щелкните правой кнопкой мыши новый сертификат, выберите **Export** (Экспорт), введите имя для сертификата, выберите формат **.p12**, а затем нажмите кнопку **Save** (Сохранить).

   	![][18]

	Запишите имя файла и расположение экспортируемого сертификата.

<h2><a name="configure-hub"></a><span class="short-header">Настройка концентратора уведомлений</span>Настройка концентратора уведомлений</h2>

1. Войдите на [портал управления Azure] и щелкните элемент **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][27]

3. Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][28]

4. Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

   	![][29]

5. Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

   	![][210]

6. Откройте расположенную сверху вкладку **Настройка**, а затем щелкните элемент **Загрузить** для параметров службы уведомлений Apple. Выберите ранее экспортированный сертификат **.p12** и пароль для него. Обязательно укажите, следует ли использовать службу push-уведомлений **Рабочая среда** (если вы хотите отправлять push-уведомления пользователям, которые приобрели ваше приложение в Магазине) или **Песочница** (во время разработки).

   	![][211]

7. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Сведения о подключении**. Запишите значения двух строк подключения.

   	![][212]

Концентратор уведомлений теперь настроен для работы с APNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

<h2><a name="connecting-app"></a><span class="short-header">Подключение приложения</span>Подключение приложения к концентратору уведомлений</h2>

1. В XCode создайте новый проект iOS и выберите шаблон **Single View Application** (Приложение одного представления).

   	![][31]

2. В разделе **Targets** (Цели) щелкните имя проекта, разверните **Code Signing Identity** (Удостоверения подписи кода), а затем в разделе **Debug** (Отладка) выберите профиль удостоверения подписи кода. Кроме того, при использовании более новой версии XCode переключите параметр **Levels** (Уровни) с **Basic** (Базовые) на **All** (Все) и задайте для элемента строки **Provisioning Profile** (Профиль подготовки) профиль подготовки. 

   	![][32]

3. Загрузите пакет Azure Mobile SDK для iOS. Откройте ZIP-файл и перетащите папку WindowsAzureMessaging.framework в папку Framework в проекте XCode. Выберите **Copy items in destination group's folder** (Копировать элементы в папку конечной группы) и нажмите кнопку **ОК**.

   	![][33]

4. В файле AppDelegate.h добавьте следующую директиву импорта:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. В файле AppDelegate.m добавьте следующий код в метод `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

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

7. *(Необязательно)* В том же самом файле добавьте следующий метод для отображения **UIAlert**, если уведомление получено при активном приложении:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}
	
8. Запустите приложение на устройстве.

<h2><a name="send"></a><span class="short-header">Отправка уведомления</span>Отправка уведомления из серверной части</h2>

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике уведомления отправляются с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

Порядок отправки уведомлений с помощью приложения .NET:

1. Создайте новое консольное приложение Visual C#: 

	![][213]

2. Добавьте ссылку на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Консоль диспетчера пакетов** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.ServiceBus

	и нажмите клавишу ВВОД.

2. Откройте файл Program.cs и добавьте следующий оператор "using":

        using Microsoft.ServiceBus.Notifications;

3. Добавьте в класс `Program` следующий метод. Обязательно замените заполнитель "имя концентратора" на имя концентратора уведомлений, отображаемое на вкладке **Концентраторы уведомлений** портала (например, в предыдущем примере это **mynotificationhub2**):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Затем добавьте следующую строку в метод `Main`:

         SendNotificationAsync();
		 Console.ReadLine();

5. Нажмите клавишу F5, чтобы запустить приложение. На устройстве должно отобразиться оповещение. Если вы используете Wi-fi, убедитесь, что подключение работает.

Все возможные виды полезных нагрузок можно найти [руководстве по программированию локальных и push-уведомлений] Apple.

Чтобы отправить уведомление с помощью мобильной службы, следуйте инструкциям из раздела [Приступая к работе с мобильными службами], а затем выполните следующие действия:

1. Выполните вход на [портал управления Azure] и выберите мобильную службу.

2. Откройте расположенную сверху вкладку **Планировщик**.

   	![][215]

3. Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.

   	![][216]

4. После создания задания щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.

5. Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители на имя концентратора уведомлений и строку подключения для элемента *DefaultFullSharedAccessSignature*, полученного ранее. Щелкните **Сохранить**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6. Нажмите кнопку **Запустить один раз** на нижней панели. На устройстве должно отобразиться оповещение.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства iOS. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в разделах [Руководство по использованию концентраторов уведомлений] и [Инструкции по использованию концентраторов уведомлений для iOS].

<!-- Anchors. -->
[Создание запроса подписи сертификата]: #certificates
[Регистрация приложения и включение push-уведомлений]: #register
[Создание профиля подготовки для приложения]: #profile
[Настройка концентратора уведомлений]: #configure-hub
[Подключение приложения к концентратору уведомлений]: #connecting-app
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png


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
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Портал управления Azure]: https://manage.windowsazure.com/
[Руководства по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для iOS]: http://msdn.microsoft.com/ru-ru/library/jj927168.aspx
[Установить Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Портал подготовки iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet

[руководстве по программированию локальных и push-уведомлений]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1

