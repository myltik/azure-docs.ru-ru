<properties
	pageTitle="Приступая к работе с Центрами уведомлений для приложений Xamarin.iOS | Microsoft Azure"
	description="Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.iOS."
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/28/2015"
	ms.author="yuaxu"/>

# Приступая к работе с центрами уведомлений

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Обзор

В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS. Вам предстоит создать пустое приложение Xamarin.iOS, получающее push-уведомления с помощью службы push-уведомлений Apple (APNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

В этом учебнике описывается простой сценарий вещания с использованием Центров уведомлений.

##Предварительные требования

Для работы с данным учебником требуется следующее:

+ [XCode 6.0][Install Xcode]
+ Устройство с iOS 7.0 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS
+ [Xamarin.iOS]
+ [Компонент мобильных служб Azure]

   >[AZURE.NOTE]В соответствии с требованиями к конфигурации push-уведомлений необходимо выполнить развертывание push-уведомлений и протестировать их на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Изучение этого учебника в полном объеме является необходимым условием для работы со всеми другими учебниками, посвященными Центрам уведомлений для приложений Xamarin.iOS.

> [AZURE.IMPORTANT]Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Служба push-уведомлений Apple использует сертификаты для аутентификации вашей мобильной службы. Выполните следующие действия, чтобы создать необходимые сертификаты и передать их в мобильную службу. Официальную документацию по APNS см. в разделе [Служба push-уведомлений Apple].


##<a name="certificates"></a>Создание файла запроса подписи сертификата

Сначала необходимо создать файл запроса подписи сертификата (с расширением CSR), используемый Apple для создания подписанного сертификата.

1. В папке "Служебные программы" запустите средство Keychain Access.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем щелкните **Request a Certificate from a Certificate Authority** (Запросить сертификат в центре сертификации).

  	![][5]

3. Выберите **User Email Address** (Адрес электронной почты пользователя), введите значения **Common Name** (Общее имя) и **CA Email Address** (Адрес электронной почты ЦС), убедитесь, что установлен флажок **Saved to disk** (Сохранено на диск), а затем щелкните **Continue** (Продолжить).

  	![][6]

4. Введите имя файл CSR в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

  	![][7]

  	При этом CSR-файл сохраняется в выбранном месте; по умолчанию — на рабочем столе. Запомните расположение, выбранное для этого файла.

Затем зарегистрируйте свое приложение в Apple, включите push-уведомления и передайте этот экспортированный CSR-файл для создания сертификата push-уведомлений.

##<a name="register"></a>Регистрация приложения для получения push-уведомлений

Чтобы иметь возможность отправлять push-уведомления в приложение для iOS из мобильных служб, необходимо зарегистрировать ваше приложение в Apple, а также зарегистрировать его для получения push-уведомлений.

1. Если ваше приложение еще не зарегистрировано, перейдите на <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портал подготовки iOS</a> в центре разработчиков Apple, выполните вход с использованием вашего идентификатора Apple, щелкните **Идентификаторы**, а затем — **Идентификаторы приложений** и, наконец, нажмите кнопку **+**, чтобы зарегистрировать новое приложение.

   	![][105]

2. Введите имя приложения в поле **Description** (Описание) и введите значение в поле **Bundle Identifier** (Идентификатор набора), выберите вариант **Push Notifications** (Push-уведомления) в разделе **App Services** (Службы приложений), а затем нажмите **Continue** (Продолжить).

   	![][106]

   	![][107]

   	![][108]


	При этом будет создан идентификатор вашего приложения и появится запрос об отправке введенной информации. Нажмите кнопку **Submit** (Отправить).

   	![][109]

	После нажатия кнопки **Submit** (Отправить) вы увидите экран **Registration complete** (Регистрация выполнена), представленный ниже. Нажмите кнопку **Done** (Готово).

   	![][110]

	> [AZURE.NOTE]Если вы хотите указать значение **Идентификатор набора**, отличное от **MobileServices.Quickstart**, необходимо также обновить значение идентификатора набора в проекте Xcode.

3. Найдите созданный вами идентификатор приложения и щелкните его строку.

   	![][111]

	Если щелкнуть идентификатор приложения, отобразятся сведения о приложении и идентификаторе приложения.

   	![][112]

   	![][113]

4. Щелкните **Изменить**, затем прокрутите страницу до нижней части экрана и нажмите кнопку **Create Certificate** (Создать сертификат) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).

   	![][114]

	Откроется помощник Add iOS Certificate (Добавление сертификата iOS).

   	![][115]

	> [AZURE.NOTE]В этом учебнике используется сертификат разработки. При регистрации сертификата производства используется тот же процесс. Просто убедитесь, что задается тот же тип сертификата при отправке сертификата в мобильные службы.

5. Щелкните **Choose File** (Выбрать файл), перейдите к папке, где был сохранен CSR-файл, созданный в первой задаче, а затем щелкните **Generate** (Создать).

  	![][116]

6. После создания сертификата с помощью портала нажмите кнопку **Download** (Загрузить) и **Done** (Готово).

  	![][118]

  	![][119]

   	При этом сертификат подписи загружается и сохраняется на вашем компьютере в папке **Загрузки**.

  	![][9]

    > [AZURE.NOTE]По умолчанию загруженный файл сертификата разработки называется **aps\_development.cer**.

7. Дважды щелкните скачанный сертификат push-уведомлений **aps\_development.cer**.

	При этом новый сертификат устанавливается в Keychain, как показано ниже:

   	![][10]

	> [AZURE.NOTE]Имя вашего сертификата может отличаться, но оно будет начинаться с префикса <strong>Apple Development iOS Push Notification Services:</strong>.

	Позже этот сертификат будет использоваться для создания файла P12 и отправки его в центр уведомлений для включения push-уведомлений через APNS.

##<a name="profile"></a>Создание профиля подготовки для приложения

1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Профили подготовки**, затем щелкните **Все** и нажмите кнопку **+**, чтобы создать новый профиль. Отобразится мастер **Add iOS Provisioning Profile** (Добавление профиля подготовки для iOS).

   	![][120]

2. Выберите **iOS App Development** (Разработка приложений для iOS) в разделе **Development** (Разработка) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

   	![][121]

3. Затем в раскрывающемся списке **App ID** (Идентификатор приложения) выберите идентификатор приложения для приложения быстрого начала работы с мобильными службами и нажмите кнопку **Continue** (Продолжить).

   	![][122]

4. На экране **Select certificates** (Выбор сертификатов) выберите ранее созданный сертификат и нажмите кнопку **Continue** (Продолжить).

   	![][123]

5. Затем выберите Devices (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).

   	![][124]

6. Наконец, выберите имя профиля в поле **Profile Name** (Имя профиля), щелкните **Generate** (Создать) и нажмите кнопку **Done** (Готово).

   	![][125]

   	![][126]

  	В результате создается новый профиль подготовки.

7. В Xcode откройте Organizer (Диспетчер), выберите представление Devices (Устройства), выберите **Provisioning Profiles** (Профили подготовки) в разделе **Library** (Библиотека) на панели слева и импортируйте недавно созданный профиль подготовки.

8. В левой части окна выберите устройство и снова импортируйте профиль подготовки.

9. В Keychain Access щелкните правой кнопкой мыши новый сертификат, выберите **Export** (Экспорт), введите имя для сертификата, выберите формат **.p12**, а затем нажмите кнопку **Save** (Сохранить).

   	![][18]

  	Запишите имя файла и расположение экспортируемого сертификата.

Это гарантирует, что проект Xcode использует новый профиль для подписывания кода. Затем необходимо загрузить этот сертификат в концентратор уведомлений.

##<a name="configure-hub"></a>Настройка центра уведомлений

1. Войдите на [портал Azure] и нажмите **+СОЗДАТЬ** в нижней части экрана.

2. Щелкните **Службы приложений**, выберите **Служебная шина**, затем щелкните **Центр уведомлений** и нажмите кнопку **Быстрое создание**.

   	![][27]

3. Введите имя для центра уведомлений, выберите нужный регион и нажмите кнопку **Создать новый Центр уведомлений**.

   	![][28]

4. Выберите недавно созданное пространство имен (обычно это ***имя центра уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

   	![][29]

5. Откройте расположенную сверху вкладку **Центры уведомлений** и выберите недавно созданный центр уведомлений.

   	![][210]

6. Откройте расположенную сверху вкладку **Настройка**, а затем щелкните элемент **Загрузить** для параметров службы уведомлений Apple. Выберите ранее экспортированный сертификат **P12** и пароль для него. Обязательно укажите, следует ли использовать службу push-уведомлений **Рабочая среда** (если вы хотите отправлять push-уведомления пользователям, которые приобрели ваше приложение в Магазине) или **Песочница** (во время разработки).

   	![][211]

7. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Сведения о подключении**. Запишите две строки подключения.

   	![][212]

Концентратор уведомлений теперь настроен для работы с APNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

##<a name="connecting-app"></a>Подключение приложения к центру уведомлений

### Создание нового проекта

1. В Xamarin Studio создайте новый проект iOS и выберите шаблон **Unified API** (Единый API) > **Single View Application** (Приложение одного представления).

   	![][31]

2. Добавьте ссылку на компонент обмена сообщениями Azure. В представлении "Решение" щелкните правой кнопкой мыши папку **Компоненты** для вашего проекта и выберите **Получить больше компонентов**. Найдите компонент **Мобильные службы Azure** и добавьте его в проект.

3. В файле **AppDelegate.cs** добавьте следующий оператор using:

    using WindowsAzure.Messaging;

4. Объявите экземпляр **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }

5. Создайте класс **Constants.cs** со следующими переменными:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. В **AppDelegate.cs** обновите **FinishedLaunching()**, как указано ниже.

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert |
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);

            return true;
        }

7. Переопределите метод **RegisteredForRemoteNotifications()** в **AppDelegate.cs**.

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

8. Переопределите метод **ReceivedRemoteNotification()** в **AppDelegate.cs**.

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Создайте следующий метод **ProcessNotification()** в **AppDelegate.cs**:

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
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
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

    > [AZURE.NOTE]Вы можете переопределить метод **FailedToRegisterForRemoteNotifications()** для обработки различных ситуаций, таких как отсутствие сетевого подключения.


10. Запустите приложение на устройстве.

##<a name="send"></a>Отправка уведомлений из серверной части

Уведомления можно отправлять с помощью Центров уведомлений из любого серверного компонента через <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейс REST</a>. В этом учебнике уведомления отправляются с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

Отправка уведомлений с помощью приложения .NET

1. Создайте новое консольное приложение Visual C#.

   	![][213]

2. Добавьте ссылку на пакет SDK служебной шины Azure с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис** > **Диспетчер пакетов библиотеки** > **Консоль диспетчера пакетов**. Затем в окне консоли введите следующее и нажмите клавишу ВВОД:

        Install-Package WindowsAzure.ServiceBus

2. Откройте файл Program.cs и добавьте следующий оператор using:

        using Microsoft.ServiceBus.Notifications;

3. Добавьте в класс `Program` следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Затем добавьте следующие строки в метод `Main`:

         SendNotificationAsync();
		 Console.ReadLine();

5. Нажмите клавишу F5, чтобы запустить приложение. На устройстве должно отобразиться оповещение. Если вы используете Wi-Fi, убедитесь, что подключение работает.

Все возможные виды полезных нагрузок можно найти в [руководстве по программированию локальных и push-уведомлений] Apple.

Чтобы отправить уведомление с помощью мобильной службы, следуйте инструкциям из раздела [Приступая к работе с мобильными службами], а затем выполните указанные ниже действия.

1. Войдите на [портал Azure] и выберите мобильную службу.

2. Откройте расположенную сверху вкладку **Планировщик**.

   	![][215]

3. Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.

   	![][216]

4. Создав задание, щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.

5. Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученными ранее. Щелкните **Сохранить**.

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

В этом простом примере осуществляется широковещательная рассылка уведомлений на все устройства iOS. Чтобы сориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании Центров уведомлений см. в разделах [Руководство по использованию Центров уведомлений] и [Инструкции по использованию Центров уведомлений для iOS].

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

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
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started-xamarin-ios
[портал Azure]: https://manage.windowsazure.com/
[Руководство по использованию Центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию Центров уведомлений для iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-dotnet

[руководстве по программированию локальных и push-уведомлений]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!---HONumber=August15_HO9-->