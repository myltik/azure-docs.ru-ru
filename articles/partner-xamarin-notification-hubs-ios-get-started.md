<properties 
	pageTitle="Приступая к работе с концентраторами уведомлений в приложениях Xamarin iOS" 
	description="Узнайте, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin для iOS." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# Приступая к работе с центрами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS. 
В этом учебнике вам предстоит создать пустое приложение Xamarin.iOS, получающее push-уведомления с помощью службы push-уведомлений Apple (APNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание запроса подписи сертификата] 
2. [Регистрация приложения и включение push-уведомлений]
3. [Создание профиля подготовки для приложения]
4. [Настройка центра уведомлений]
5. [Подключение приложения к центру уведомлений]
6. [Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием центров уведомлений. Для работы с этим учебником необходимо следующее:

+ [XCode 5.0][Установка Xcode] 
+ Устройство с iOS 5.0 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS
+ [Xamarin.iOS]
+ [Компонент мобильных служб Azure]

   > [AZURE.NOTE] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Выполнение заданий из этого учебника в полном объеме является необходимым условием для работы со всеми другими учебниками, посвященными центрам уведомлений для приложений Xamarin.iOS. 

> [AZURE.IMPORTANT] Для работы с этим учебником требуется активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F"%20target="_blank).

Служба push-уведомлений Apple (APNS) использует сертификаты для аутентификации вашей мобильной службы. Выполните следующие действия, чтобы создать необходимые сертификаты и передать их в мобильную службу. Официальную документацию по APNS см. в разделе [Служба push-уведомлений Apple].



<h2><a name="certificates"></a>Создание файла запроса подписи сертификата</h2>

Сначала необходимо создать файл запроса подписи сертификата (с расширением CSR), используемый Apple для создания подписанного сертификата.

1. В папке "Служебные программы" запустите средство Keychain Access.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем щелкните **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации...).

  	![][5]

3. Выберите **Адрес электронной почты пользователя**, введите значения **Общее имя** и **Адрес электронной почты ЦС**, убедитесь, что установлен флажок **Сохранено на диск**, а затем щелкните **Продолжить**.

  	![][6]

4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

  	![][7]
  
  	При этом CSR-файл сохраняется в выбранном месте; по умолчанию - на рабочем столе. Запомните расположение, выбранное для этого файла.

Затем зарегистрируйте свое приложение в Apple, включите push-уведомления и передайте этот экспортированный CSR-файл для создания сертификата push-уведомлений.

<h2><a name="register"></a>Регистрация приложения для получения push-уведомлений</h2>

Чтобы иметь возможность отправлять push-уведомления в приложение для iOS из мобильных служб, необходимо зарегистрировать ваше приложение в системе Apple, а также зарегистрировать его для получения push-уведомлений.  

1. Если ваше приложение еще не зарегистрировано, перейдите на <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портал подготовки iOS</a> в центре разработчиков Apple, выполните вход с использованием вашего идентификатора Apple, щелкните **Идентификаторы**, а затем - **Идентификаторы приложений** и, наконец, щелкните знак **+**, чтобы зарегистрировать новое приложение.

   	![][105] 

2. Введите имя приложения в поле **Описание** и введите значение в поле **Идентификатор набора**, выберите вариант "Push-уведомления" в разделе "Службы приложений", а затем щелкните **Продолжить**.

   	![][106]

   	![][107] 

   	![][108]
   

	При этом будет создан идентификатор вашего приложения и появится запрос об отправке введенной информации. Нажмите кнопку **Submit** (Отправить).
   
   	![][109] 
   
	После нажатия кнопки **Submit** (Отправить) вы увидите экран **Registration complete** (Регистрация выполнена), представленный ниже. Нажмите кнопку **Done** (Готово).
   
   	![][110]

	> [AZURE.NOTE] Если вы хотите указать значение **Идентификатор набора**, отличное от **MobileServices.Quickstart**, необходимо также обновить значение идентификатора набора в проекте Xcode.

3. Найдите созданный вами идентификатор приложения и щелкните его строку. 

   	![][111]
   
	Если щелкнуть идентификатор приложения, отобразятся сведения о приложении и идентификаторе приложения.
   
   	![][112] 
   
   	![][113]

4. Щелкните **Изменить**, затем прокрутите до нижней части экрана и нажмите кнопку **Создать сертификат...** в разделе **SSL-сертификат разработки push-уведомлений**.

   	![][114] 

	Откроется помощник "Add iOS Certificate" (Добавление сертификата iOS).
   
   	![][115] 

	> [AZURE.NOTE] В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Просто убедитесь, что задается тот же тип сертификата при отправке сертификата в мобильные службы.

5. Щелкните **Выбрать файл**, перейдите к папке, где был сохранен CSR-файл, созданный в первом задании, а затем щелкните **Создать**. 

  	![][116]
  
6. После создания сертификата с помощью портала нажмите кнопку **Загрузить** и **Готово**.
 
  	![][118]

  	![][119]  
  
   	При этом сертификат подписи загружается и сохраняется на вашем компьютере в папке **Загрузки**. 

  	![][9] 

    > [AZURE.NOTE] По умолчанию скачанный файл сертификата разработки называется **aps_development.cer**.

7. Дважды щелкните скачанный сертификат push-уведомлений **aps_development.cer**.

	При этом новый сертификат устанавливается в Keychain, как показано ниже:

   	![][10]

	> [AZURE.NOTE]
	> Имя вашего сертификата может отличаться, но оно будет начинаться с префикса <strong>Apple Development iOS Push Notification Services:</strong>.

	Позже этот сертификат будет использоваться для создания файла .p12 и отправки его в концентратор уведомлений для включения push-уведомлений через APNS.

<h2><a name="profile"></a>Создание профиля подготовки для приложения</h2>
 
1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Профили подготовки**, затем щелкните **Все** и нажмите кнопку **+**, чтобы создать новый профиль. Отображается мастер **Add iOS Provisioning Profile** (Добавление профиля подготовки для iOS).

   	![][120]

2. Выберите **Разработка приложений для iOS** в разделе **Разработка** в качестве типа профиля подготовки и нажмите кнопку **Продолжить**.

   	![][121]

3. Затем выберите идентификатор приложения для приложения быстрого начала работы с мобильными службами в раскрывающемся списке **App ID** (Идентификатор приложения) и нажмите кнопку **Continue** (Продолжить).

   	![][122]

4. На экране **Выбор сертификатов** выберите ранее созданный сертификат и нажмите кнопку **Продолжить**.
  
   	![][123]

5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).

   	![][124]

6. Наконец, выберите имя профиля в поле **Имя профиля**, щелкните **Создать** и нажмите кнопку **Готово**.

   	![][125]
   
   	![][126]
	
  	В результате создается новый профиль подготовки.

7. В Xcode откройте диспетчер, выберите представление "Устройства", выберите **Профили подготовки** в разделе **Библиотека** на левой панели и импортируйте недавно созданный профиль подготовки.

8. В левой части окна выберите устройство и снова импортируйте профиль подготовки. 

9. В Keychain Access щелкните правой кнопкой мыши новый сертификат, выберите **Экспорт**, введите имя для сертификата, выберите формат **.p12**, а затем нажмите кнопку **Сохранить**.

   	![][18]

  	Запишите имя файла и расположение экспортируемого сертификата.

Это гарантирует, что проект Xcode использует новый профиль для подписывания кода. Затем необходимо загрузить этот сертификат в концентратор уведомлений.

<h2><a name="configure-hub"></a>Настройка центра уведомлений</h2>

1. Войдите на [портал управления Azure] и щелкните **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Шина обслуживания**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][27]

3. Введите имя для центра уведомлений, выберите нужный регион и щелкните элемент **Создать новый центр уведомлений**.

   	![][28]

4. Выберите недавно созданное пространство имен (обычно это ***имя центра уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

   	![][29]

5. Щелкните расположенную сверху вкладку **Центры уведомлений** и выберите недавно созданный центр уведомлений.

   	![][210]

6. Выберите расположенную сверху вкладку **Настройка**, а затем щелкните **Загрузить** для параметров службы уведомлений Apple. Выберите ранее экспортированный сертификат **.p12** и пароль для него. Обязательно укажите, следует ли использовать службу push-уведомлений **Рабочая среда** (если вы хотите отправлять push-уведомления пользователям, которые приобрели ваше приложение в Магазине) или **Песочница** (во время разработки).

   	![][211]

7. Щелкните расположенную сверху вкладку **Панель мониторинга** и щелкните **Информация о подключении**. Запишите значения двух строк подключения.

   	![][212]

Концентратор уведомлений теперь настроен для работы с APNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

<h2><a name="connecting-app"></a>Подключение приложения к центру уведомлений</h2>

### Загрузите библиотеку WindowsAzure.Messaging

Эта сборка позволяет легко выполнять регистрацию в концентраторах уведомлений Azure. Следуйте приведенным ниже указаниями, чтобы скачать ее, или найдите ее в разделе [скачивания примера][GitHub].

1. Скачайте источник для [WindowsAzure.Messaging] с ресурса GitHub.

2. Скомпилируйте проект и найдите выходную сборку **WindowsAzure.Messaging.dll** - это потребуется при настройке приложения Xamarin.iOS ниже.


### Создание нового проекта

1. В Xamarin Studio создайте новый проект iOS и выберите шаблон **Однооконное приложение**.

   	![][31]

2. Сначала добавьте ссылку на компонент "Мобильные службы Azure". В представлении "Решение" щелкните правой кнопкой мыши папку **Компоненты** для вашего проекта и выберите **Получить больше компонентов**. Найдите компонент **Мобильные службы Azure** и добавьте компонент в проект.

3. Теперь добавьте ссылку на библиотеку WindowsAzure.Messaging, которую мы загрузили раньше. Щелкните правой кнопкой мыши по папке **Ссылки** проекта и выберите команду **Изменить ссылки...**. На вкладке **Сборка .Net** найдите **WindowsAzure.Messaging.dll** и 

4. В **AppDelegate.cs** добавьте следующие операторы:

		using Microsoft.WindowsAzure.MobileServices;
    using WindowsAzure.Messaging;

5. Объявите экземпляр **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }
		
6. Создайте класс **Constants.cs** со следующими переменными:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
	
	
7. В **AppDelegate.cs** обновите **FinishedLaunching()** в соответствии со следующим:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8. Переопределите метод **RegisteredForRemoteNotifications()** в **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null) 
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                } 

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

9. Переопределите метод **ReceivedRemoteNotification()** в **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
        
10. Create the following **ProcessNotification()** method in **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying 
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc., 
                // your "alert" object from the aps dictionary will be another NSDictionary. 
                // Basically the json gets dumped right into a NSDictionary, 
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }           
            }
        }

    > [AZURE.NOTE] Можно переопределить **FailedToRegisterForRemoteNotifications()** для обработки ситуаций, в том числе без сетевого подключения, и т. д.

	
11. Запустите приложение на устройстве.

<h2><a name="send"></a>Отправка уведомления из серверной части</h2>

Уведомления можно отправлять с помощью центров уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике уведомления отправляются с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

Порядок отправки уведомлений с помощью приложения .NET:

1. Создайте новое консольное приложение Visual C#. 

   	![][213]

2. Добавьте ссылку на пакет SDK для Azure Service Bus с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. Откройте файл Program.cs и добавьте следующий оператор using:

        using Microsoft.ServiceBus.Notifications;

3. Добавьте в класс `Program` следующий метод:

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

Все возможные виды полезных нагрузок можно найти в [руководстве по программированию локальных и push-уведомлений] Apple.

Чтобы отправить уведомление с помощью мобильной службы, следуйте инструкциям из раздела [Приступая к работе с мобильными службами], а затем выполните следующие действия:

1. Выполните вход на [портал управления Azure] и выберите мобильную службу.

2. Выберите расположенную сверху вкладку **Планировщик**.

   	![][215]

3. Создайте новое запланированное задание, вставьте имя и щелкните **По запросу**.

   	![][216]

4. Создав задание, щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.

5. Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители именем центра уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученными ранее. Щелкните **Сохранить**.

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

В этом простом примере осуществляется рассылка уведомлений на все устройства iOS. Для охвата определенных пользователей см. учебник [Использование центров уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование центров уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании центров уведомлений см. в разделах [Руководство по использованию центров уведомлений] и [Инструкции по использованию центров уведомлений для iOS].

<!-- Anchors. -->
[Создание запроса подписи сертификата]: #certificates
[Регистрация приложения и включение push-уведомлений]: #register
[Создание профиля подготовки для приложения]: #profile
[Настройка центра уведомлений]: #configure-hub
[Подключение приложения к центру уведомлений]: #connecting-app
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Пакет SDK для мобильных служб для iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios
[Портал управления Azure]: https://manage.windowsazure.com/
[Руководство по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию центров уведомлений для iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Установка Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Портал подготовки iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Использование центров уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование центров уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet

[Руководство по программированию локальных и push-уведомлений]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!--HONumber=45--> 
