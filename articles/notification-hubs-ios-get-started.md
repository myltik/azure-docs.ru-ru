<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/24/2014" ms.author="krisragh" />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS.
 В этом учебнике вам предстоит создать пустое приложение iOS, получающее push-уведомления с помощью службы push-уведомлений Apple (APNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Создание запроса подписи сертификата][Создание запроса подписи сертификата]
2.  [Регистрация приложения и включение push-уведомлений][Регистрация приложения и включение push-уведомлений]
3.  [Создание профиля подготовки для приложения][Создание профиля подготовки для приложения]
4.  [Настройка концентратора уведомлений][Настройка концентратора уведомлений]
5.  [Подключение приложения к концентратору уведомлений][Подключение приложения к концентратору уведомлений]
6.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств. В работы с этим учебником необходимо следующее:

-   [SDK мобильных служб для iOS][SDK мобильных служб для iOS]
-   [XCode 4.5][XCode 4.5]
-   Устройство с iOS 5.0 (или более поздней версии)
-   Участие в программе для разработчиков на платформе iOS

<div class="dev-callout"><b>Примечание.</b><br /> <p>В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.</p><br /> </div>

</p>
Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений iOS.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

[WACOM.INCLUDE [Включение службы push-уведомлений Apple](../includes/enable-apple-push-notifications.md)]

## <a name="configure-hub"></a>Настройка концентратора уведомлений

1.  В Keychain Access щелкните правой клавишей мыши по новому сертификату **Мои сертификаты** приложения быстрого запуска. Нажмите **Экспорт**, задайте имя файла, выберите формат **.p12** и нажмите кнопку **Сохранить**.

    ![][0]

Запишите имя файла и расположение экспортируемого сертификата.

> [WACOM.ПРИМЕЧАНИЕ] В этом учебнике описывается создание файла QuickStart.p12. Имя файла и расположение могут отличаться.

1.  Войдите на [портал управления Аzure][портал управления Аzure] и нажмите элемент **+СОЗДАТЬ** в нижней части экрана.

2.  Последовательно нажмите элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

    ![][1]

3.  Введите имя для концентратора уведомлений, выберите нужный регион и нажмите элемент **Создать новый концентратор уведомлений**.

    ![][2]

4.  Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и перейдите на расположенную сверху вкладку **Настройка**.

    ![][3]

5.  Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

    ![][4]

6.  Перейдите на расположенную сверху вкладку **Настройка**, а затем нажмите кнопку **Загрузить** для параметров службы уведомлений Apple. Выберите ранее экспортированный сертификат **.p12** и пароль для него. Обязательно укажите, следует ли использовать службу push-уведомлений **Рабочая среда** (если вы хотите отправлять push-уведомления пользователям, которые приобрели ваше приложение в Магазине) или **Песочница** (во время разработки).

    ![][5]

7.  Откройте расположенную сверху вкладку **Панель мониторинга** и нажмите **Сведения о подключении**. Запишите значения двух строк подключения.

    ![][6]

Концентратор уведомлений теперь настроен для работы с APNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

## <a name="connecting-app"></a>Подключение приложения к концентратору уведомлений

1.  В XCode создайте новый проект iOS и выберите шаблон **Приложение одного представления**.

    ![][7]

2.  В разделе **Цели** нажмите имя проекта, разверните **Удостоверения подписи кода**, а затем в разделе **Отладка** выберите профиль удостоверения подписи кода. Кроме того, при использовании более новой версии XCode переключите параметр **Levels** (Уровни) с **Basic** (Базовые) на **All** (Все) и задайте для элемента строки **Provisioning Profile** (Профиль подготовки) профиль подготовки.

    ![][8]

3.  Загрузите пакет Azure Mobile SDK для iOS. Откройте ZIP-файл и перетащите папку WindowsAzureMessaging.framework в папку Framework в проекте XCode. Выберите **Copy items in destination group's folder** (Копировать элементы в папку конечной группы) и нажмите кнопку **ОК**.

    ![][9]

4.  В файле AppDelegate.h добавьте следующий каталог импорта:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  В файле AppDelegate.m добавьте следующий код в метод `didFinishLaunchingWithOptions`:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  В том же файле добавьте следующий метод:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                      @"<connection string>" notificationHubPath:@"mynh"];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

7.  *(Необязательно)* В том же самом файле добавьте следующий метод для отображения **UIAlert**, если уведомление получено при активном приложении:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

8.  Запустите приложение на устройстве.

## <a name="send"></a>Отправка уведомления из серверной части

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью [интерфейса REST][интерфейса REST]. В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Примеры отправки уведомлений с сервера мобильных служб Azure, интегрированного с интегрированными концентраторами уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([Сервер .NET][Сервер .NET] | [Сервер JavaScript][Сервер .NET]). Примеры отправки уведомлений с помощью API REST см. в статье **Использование концентраторов уведомлений из Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  В Visual Studio из меню **Файл** последовательно выберите **Создать** и **Проект...**, затем в области **Visual C#** нажмите **Windows** и **Консольные приложения**, затем нажмите кнопку **OK**.

    [][20]

    Будет создан новый проект консольного приложения.

2.  В меню **Инструменты** последовательно выберите **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**.

    Отобразится консоль диспетчера пакетов.

3.  В окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus

    Будет добавлена ссылка на пакет Azure Service Bus SDK с помощью [пакета NuGet WindowsAzure.ServiceBus][пакета NuGet WindowsAzure.ServiceBus].

4.  Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

5.  Добавьте в класс **Программа** следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

    Обязательно замените заполнитель "имя концентратора" на имя концентратора уведомлений, отображаемое на вкладке **Концентраторы уведомлений** портала. Замените разделитель строки подключения строкой с именем **DefaultFullSharedAccessSignature**, полученной в разделе "Настройка концентратора уведомлений".

    > [WACOM.ПРИМЕЧАНИЕ]Обязательно используйте строку подключения с **полным** доступом, а не с доступом к **прослушиванию**. У строки с доступом к прослушиванию нет прав на отправку уведомлений.

6.  Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
         Console.ReadLine();

7.  Нажмите клавишу F5, чтобы запустить консольное приложение.

    На устройстве должно отобразиться оповещение. Если вы используете Wi-fi, убедитесь, что подключение работает.

Все возможные виды полезных нагрузок можно найти в [руководстве по программированию локальных и push-уведомлений][руководстве по программированию локальных и push-уведомлений] Apple.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства iOS. Для охвата определенных пользователей см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям][Использование концентраторов уведомлений для отправки push-уведомлений пользователям], если же требуется разделить пользователей по группам интересов, см. [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений][руководстве по использованию концентраторов уведомлений].



  [Создание запроса подписи сертификата]: #certificates
  [Регистрация приложения и включение push-уведомлений]: #register
  [Создание профиля подготовки для приложения]: #profile
  [Настройка концентратора уведомлений]: #configure-hub
  [Подключение приложения к концентратору уведомлений]: #connecting-app
  [Отправка уведомлений из серверной части]: #send
  [SDK мобильных служб для iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png
  [портал управления Аzure]: https://manage.windowsazure.com/
  [1]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [6]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [7]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [8]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
  [9]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png
  [интерфейса REST]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx
  [Сервер .NET]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Java]: /ru-ru/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ru-ru/documentation/articles/notification-hubs-php-backend-how-to/
  [пакета NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [руководстве по программированию локальных и push-уведомлений]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
  [Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
  [руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
