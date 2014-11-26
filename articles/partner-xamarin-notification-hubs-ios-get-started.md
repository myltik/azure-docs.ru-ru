<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="Get Started" pageTitle="Get Started with Notification Hubs for Xamarin iOS apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="donnam" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

В этой теме показано, как можно использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение iOS.
В этом учебном материале описывается создание пустого приложения Xamarin.iOS, которое получает push-уведомления при помощи службы Apple Push Notification (APN). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений. Готовый код доступен в примере [приложения NotificationHubs][приложения NotificationHubs].

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Создание запроса подписи сертификата][Создание запроса подписи сертификата]
2.  [Регистрация приложения и включение push-уведомлений][Регистрация приложения и включение push-уведомлений]
3.  [Создание профиля подготовки для приложения][Создание профиля подготовки для приложения]
4.  [Настройка концентратора уведомлений][Настройка концентратора уведомлений]
5.  [Подключение приложения к концентратору уведомлений][Подключение приложения к концентратору уведомлений]
6.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. В работы с этим учебником необходимо следующее:

-   [XCode 5.0][XCode 5.0]
-   Устройство с iOS 5.0 (или более поздней версии)
-   Участие в программе для разработчиков на платформе iOS
-   [Xamarin.iOS][1]
-   [Компонент мобильных служб Azure][Компонент мобильных служб Azure]

   <div class="dev-callout"><b>Примечание.</b><br /> <p>В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.</p>
   </div>

Изучение этого учебника в полном объеме является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Xamarin.iOS.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

Служба push-уведомлений Apple (APNS) использует сертификаты для аутентификации вашей мобильной службы. Выполните следующие действия, чтобы создать необходимые сертификаты и передать их в мобильную службу. Официальную документацию по APNS см. в разделе [Служба push-уведомлений Apple][Служба push-уведомлений Apple].

## <a name="certificates"></a><span class="short-header">Создание файла CSR</span>Создание файла запроса подписи сертификата

Сначала необходимо создать файл запроса подписи сертификата (с расширением CSR), используемый Apple для создания подписанного сертификата.

1.  В папке "Служебные программы" запустите средство Keychain Access.

2.  Щелкните **Keychain Access**, разверните **Помощник по сертификатам**, а затем щелкните **Запросить сертификат в центре сертификации...**.

    ![][0]

3.  Выберите **Адрес электронной почты пользователя**, введите значения **Общее имя**  и **Адрес электронной почты ЦС**, убедитесь, что установлен флажок **Сохранено на диск**, а затем щелкните **Продолжить**.

    ![][2]

4.  Введите имя файла запроса подписи сертификата (CSR) в поле **Сохранить как**, выберите расположение в поле **Папка** и нажмите кнопку **Сохранить**.

    ![][3]

    При этом CSR-файл сохраняется в выбранном месте; по умолчанию — на рабочем столе. Запомните расположение, выбранное для этого файла.

Затем зарегистрируйте свое приложение в Apple, включите push-уведомления и передайте этот экспортированный CSR-файл для создания сертификата push-уведомлений.

## <a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для получения push-уведомлений

Чтобы иметь возможность отправлять push-уведомления в приложение для iOS из мобильных служб, необходимо зарегистрировать ваше приложение в Apple, а также зарегистрировать его для получения push-уведомлений.

1.  Если ваше приложение еще не зарегистрировано, перейдите на [портал подготовки iOS][портал подготовки iOS] в центре разработчиков Apple, выполните вход с использованием вашего идентификатора Apple, щелкните **Идентификаторы**, а затем — **Идентификаторы приложений** и, наконец, щелкните знак **+**, чтобы зарегистрировать новое приложение.

    ![][4]

2.  Введите имя приложения в поле **Описание** и введите значение в поле **Идентификатор набора**, выберите вариант Push-уведомления в разделе Службы приложений, а затем щелкните **Продолжить**.

    ![][5]

    ![][6]

    ![][7]

    При этом создается идентификатор вашего приложения и появляется запрос об отправке информации. Нажмите кнопку **Отправить**.

    ![][8]

    После нажатия кнопки **Отправить** вы увидите экран **Регистрация выполнена**, представленный ниже. Нажмите кнопку **Готово**.

    ![][9]

    <div class="dev-callout"><b>Примечание.</b>
<p>Если вы хотите указать значение <strong>Идентификатор набора</strong>, отличное от <b>MobileServices.Quickstart</b>, необходимо также обновить значение идентификатора набора в проекте Xcode.</p>
 </div>

3.  Найдите созданный вами идентификатор приложения и щелкните его строку.

    ![][10]

    Если щелкнуть идентификатор приложения, отобразятся сведения о приложении и идентификаторе приложения.

    ![][11]

    ![][12]

4.  Щелкните **Изменить**, затем прокрутите до нижней части экрана и нажмите кнопку **Создать сертификат...** в разделе **SSL-сертификат разработки push-уведомлений**.

    ![][13]

    Откроется помощник "Добавление сертификата iOS".

    ![][14]

    <div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Просто убедитесь, что задается тот же тип сертификата при отправке сертификата в мобильные службы.</p>
</div>

5.  Щелкните **Выбрать файл**, перейдите к папке, где был сохранен CSR-файл, созданный в первой задаче, а затем щелкните **Создать**.

    ![][15]

6.  После создания сертификата с помощью портала нажмите кнопку **Загрузить** и **Готово**.

    ![][16]

    ![][17]

    При этом сертификат подписи загружается и сохраняется на вашем компьютере в папке **Загрузки**.

    ![][18]

    <div class="dev-callout"><b>Примечание.</b>
<p>По умолчанию загруженный файл сертификата разработки называется <strong>aps_development.cer</strong>.</p>
</div>

7.  Дважды щелкните скачанный сертификат push-уведомлений **aps\_development.cer**.

    При этом новый сертификат устанавливается в Keychain, как показано ниже:

    ![][19]

    > [WACOM.NOTE]
    > Имя вашего сертификата может отличаться, но оно будет начинаться с префикса **Apple Development iOS Push Notification Services:**.

    Позже этот сертификат будет использоваться для создания файла .p12 и отправки его в концентратор уведомлений для включения push-уведомлений через APNS.

## <a name="profile"></a><span class="short-header">Подготовка приложения</span>Создание профиля подготовки для приложения

1.  На [портале подготовки iOS][портал подготовки iOS] выберите **Профили подготовки**, затем щелкните **Все** и нажмите кнопку **+**, чтобы создать новый профиль. Отображается мастер **Добавление профиля подготовки для iOS**.

    ![][20]

2.  Выберите **Разработка приложений для iOS** в разделе **Разработка** в качестве типа профиля подготовки и нажмите кнопку **Продолжить**.

    ![][21]

3.  Затем выберите идентификатор приложения для приложения быстрого начала работы с мобильными службами в раскрывающемся списке **Идентификатор приложения** и нажмите кнопку **Продолжить**.

    ![][22]

4.  На экране **Выбор сертификатов** выберите ранее созданный сертификат и нажмите кнопку **Продолжить**.

    ![][23]

5.  Затем выберите **Устройства** для тестирования и нажмите кнопку **Продолжить**.

    ![][24]

6.  Наконец, выберите имя профиля в поле **Имя профиля**, щелкните **Создать** и нажмите кнопку **Готово**.

    ![][25]

    ![][26]

    В результате создается новый профиль подготовки.

7.  В Xcode откройте Диспетчер, выберите представление Устройства, выберите **Профили подготовки** в разделе **Библиотека** на левой панели и импортируйте недавно созданный профиль подготовки.

8.  В левой части окна выберите устройство и снова импортируйте профиль подготовки.

9.  В Keychain Access щелкните правой кнопкой мыши новый сертификат, выберите **Экспорт**, введите имя для сертификата, выберите формат **.p12**, а затем нажмите кнопку **Сохранить**.

    ![][27]

    Запишите имя файла и расположение экспортируемого сертификата.

Это гарантирует, что проект Xcode использует новый профиль для подписывания кода. Затем необходимо загрузить этот сертификат в концентратор уведомлений.

## <a name="configure-hub"></a><span class="short-header">Настройка концентратора уведомлений</span>Настройка концентратора уведомлений

1.  Войдите на портал [управления Windows Azure][управления Windows Azure] и щелкните элемент **+СОЗДАТЬ** в нижней части экрана.

2.  Последовательно щелкните элементы **Службы приложений**, **Сервисная шина**, **Концентратор уведомлений** и **Быстрое создание**.

    ![][28]

3.  Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

    ![][29]

4.  Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

    ![][30]

5.  Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

    ![][31]

6.  Откройте расположенную сверху вкладку **Настройка**, а затем щелкните элемент **Загрузить** для параметров службы уведомлений Apple. Выберите ранее экспортированный сертификат **.p12** и пароль для него. Обязательно укажите, следует ли использовать службу push-уведомлений **Рабочая среда** (если вы хотите отправлять push-уведомления пользователям, которые приобрели ваше приложение в Магазине) или **Песочница** (во время разработки).

    ![][32]

7.  Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Сведения о подключении**. Запишите значения двух строк подключения.

    ![][33]

Концентратор уведомлений теперь настроен для работы с APNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

## <a name="connecting-app"></a><span class="short-header">Подключение приложения</span>Подключение приложения к концентратору уведомлений

### Загрузите библиотеку WindowsAzure.Messaging

Эта сборка предоставляет простой способ регистрации в концентраторах уведомлений Azure. Его можно загрузить, следуя инструкции ниже, или найти в [образце для загрузки][приложения NotificationHubs].

1.  Загрузите источник для [WindowsAzure.Messaging][WindowsAzure.Messaging] с ресурса GitHub.

2.  Скомпилируйте проект и найдите выходную сборку **WindowsAzure.Messaging.dll** — это потребуется при настройке приложения Xamarin.iOS ниже.

### Создание проекта

1.  В Xamarin Studio создайте новый проект iOS и выберите шаблон **Приложение одного представления**.

    ![][34]

2.  Сначала добавьте ссылку на компонент "Мобильные службы Azure". В представлении "Решение" щелкните правой кнопкой мыши папку **Компоненты** для вашего проекта и выберите **Получить больше компонентов**. Найдите компонент **Мобильные службы Azure** и добавьте компонент в проект.

3.  Теперь добавьте ссылку на библиотеку WindowsAzure.Messaging, которую мы загрузили раньше. Щелкните правой кнопкой мыши по папке **Ссылки** проекта и выберите команду **Изменить ссылки...**. На вкладке **.Net Assembly** найдите **WindowsAzure.Messaging.dll** и

4.  В **AppDelegate.cs** добавьте следующие инструкции using:

        using Microsoft.WindowsAzure.MobileServices;

    using WindowsAzure.Messaging;

5.  Объявите экземпляр **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

6.  Создайте класс **Constants.cs** со следующими переменными:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";

7.  В **AppDelegate.cs** обновите **FinishedLaunching()** в соответствии со следующим.

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8.  Переопределите метод **RegisteredForRemoteNotifications()** в **AppDelegate.cs**.

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

9.  Переопределите метод **ReceivedRemoteNotification()** в **AppDelegate.cs**.

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

10. Создайте следующий метод **ProcessNotification()** в **AppDelegate.cs**:

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

    <div class="dev-callout"><b>Примечание.</b>
<p>Можно переопределить <b> FailedToRegisterForRemoteNotifications()</b> для обработки ситуаций, в том числе без сетевого подключения и т. д.</p>
</div>

11. Запустите приложение на устройстве.

## <a name="send"></a><span class="short-header">Отправка уведомления</span>Отправка уведомления из серверной части

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью [интерфейса REST][интерфейса REST]. В этом учебнике уведомления отправляются с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

Порядок отправки уведомлений с помощью приложения .NET:

1.  Создайте новое консольное приложение Visual C#.

    ![][35]

2.  Добавьте ссылку на пакет Windows Azure Service Bus SDK с помощью [пакета NuGet WindowsAzure.ServiceBus][пакета NuGet WindowsAzure.ServiceBus]. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.ServiceBus and press Enter.

3.  Откройте файл Program.cs и добавьте следующий оператор using:

        using Microsoft.ServiceBus.Notifications;

4.  Добавьте в класс `Program` следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

5.  Затем добавьте следующую строку в метод `Main`:

         SendNotificationAsync();
         Console.ReadLine();

6.  Нажмите клавишу F5, чтобы запустить приложение. На устройстве должно отобразиться оповещение. Если вы используете Wi-fi, убедитесь, что подключение работает.

Все возможные виды полезных нагрузок можно найти в [руководстве по программированию локальных и push-уведомлений][руководстве по программированию локальных и push-уведомлений] Apple.

Чтобы отправить уведомление с помощью мобильной службы, следуйте инструкциям из раздела [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами], а затем выполните следующие действия:

1.  Выполните вход на [портал управления Azure][управления Windows Azure] и выберите мобильную службу.

2.  Откройте расположенную сверху вкладку **Планировщик**.

    ![][36]

3.  Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.

    ![][37]

4.  Создав задание, щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.

5.  Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученными ранее. Щелкните **Сохранить**.

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

6.  Нажмите кнопку **Запустить один раз** на нижней панели. На устройстве должно отобразиться оповещение.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства iOS. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям][Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в разделах [Руководство по использованию концентраторов уведомлений][Руководство по использованию концентраторов уведомлений] и [Инструкции по использованию концентраторов уведомлений для iOS][Инструкции по использованию концентраторов уведомлений для iOS].



  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [приложения NotificationHubs]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Создание запроса подписи сертификата]: #certificates
  [Регистрация приложения и включение push-уведомлений]: #register
  [Создание профиля подготовки для приложения]: #profile
  [Настройка концентратора уведомлений]: #configure-hub
  [Подключение приложения к концентратору уведомлений]: #connecting-app
  [Отправка уведомлений из серверной части]: #send
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [1]: http://xamarin.com/download
  [Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  [0]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
  [2]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
  [3]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png
  [портал подготовки iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [4]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
  [5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
  [6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
  [7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
  [8]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
  [9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
  [10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
  [11]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
  [12]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
  [13]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
  [14]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
  [15]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png
  [16]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
  [17]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png
  [18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
  [19]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
  [20]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
  [21]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
  [22]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
  [23]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
  [24]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
  [25]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
  [26]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png
  [27]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
  [управления Windows Azure]: https://manage.windowsazure.com/
  [28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [30]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
  [34]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [интерфейса REST]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx
  [35]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
  [пакета NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [руководстве по программированию локальных и push-уведомлений]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios
  [36]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
  [37]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
  [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
  [Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
  [Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
  [Инструкции по использованию концентраторов уведомлений для iOS]: http://msdn.microsoft.com/ru-ru/library/jj927168.aspx
