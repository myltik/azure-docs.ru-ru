<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Приступая к работе с концентраторами уведомлений" pageTitle="Приступая к работе с концентраторами уведомлений Azure" metaKeywords="" description="Узнайте, как использовать концентраторы уведомлений Azure для работы с push-уведомлениями." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Приступая к работе с концентраторами уведомлений" authors=""  solutions="" writer="elioda" manager="" editor=""  />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/manage/services/notification-hubs/get-started-notification-hubs-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios" title="iOS">iOS</a><a href="/ru-ru/manage/services/notification-hubs/get-started-notification-hubs-android" title="Android">Android</a><a href="/ru-ru/manage/services/notification-hubs/getting-started-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/manage/services/notification-hubs/getting-started-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение Магазина Windows. 
В этом учебнике вам предстоит создать пустое приложение Магазина Windows, получающее push-уведомления с помощью службы push-уведомлений Windows (WNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для получения push-уведомлений]
2. [Настройка концентратора уведомлений]
3. [Подключение приложения к концентратору уведомлений]
4. [Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств. Для данного учебника требуется следующее:

+ Microsoft Visual Studio 2012 Express для Windows 8
+ Активная учетная запись Магазина Windows

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Магазина Windows. 

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для Магазина Windows</h2>

Чтобы отправлять push-уведомления в приложения Магазина Windows из мобильных служб, необходимо отправить приложение в Магазин Windows. После этого необходимо настроить концентратор уведомлений на интеграцию с WNS.

1. Если вы еще не зарегистрировали свое приложение, перейдите на [страницу "Отправить приложение"] в Центре разработки для приложений Магазина Windows, выполните вход с использованием вашей учетной записи Майкрософт, а затем щелкните **Имя приложения**.

   	![][0]

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения**, а затем щелкните **Сохранить**.

   	![][1]

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. Создайте в Visual Studio 2012 Express для Windows 8 новый проект Магазина Windows Visual C# с помощью шаблона **Пустое приложение**.

   	![][2]

4. В обозревателе решений щелкните правой кнопкой мыши проект, выберите **Магазин**, а затем щелкните **Связать приложение с Магазином...**. 

   	![][3]

   	Открывается мастер **Свяжите свое приложение с Магазином Windows**.

5. В окне мастера щелкните **Вход** и войдите в систему с использованием учетной записи Майкрософт.

6. Выберите приложение, зарегистрированное на шаге 2, нажмите кнопку **Далее**, а затем щелкните **Связать**.

   	![][4]

   	При этом в манифест приложения добавляются необходимые регистрационные данные Магазина Windows.    

7. На странице Центра разработки Windows для нового приложения щелкните **Службы**. 

   	![][5] 

8. На странице **Службы** щелкните **узел "Службы Live"** в разделе **Мобильные службы Azure**.

   	![][17]

9. Щелкните **Проверка подлинности службы** и запишите значения полей **Секрет клиента** и **Идентификатор безопасности пакета**. 

   	![][6]

 	<div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента и ИД безопасности пакета — это важные учетные данные для безопасного доступа. Не сообщайте никому эти значения и не распространяйте их вместе с вашим приложением.</p>
    </div>

<h2><a name="configure-hub"></a><span class="short-header">Настройка концентратора уведомлений</span>Настройка концентратора уведомлений</h2>

1. Войдите на [портал управления Azure] и щелкните элемент **СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][7]

3. Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][8]

4. Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

   	![][9]

5. Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

   	![][10]

6. Перейдите на расположенную сверху вкладку **Настройка**, введите значения **Секрет клиента** и **Идентификатор безопасности пакета**, полученные из WNS в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

   	![][11]

7. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Сведения о подключении**. Запишите значения двух строк подключения.

   	![][12]

Концентратор уведомлений теперь настроен для работы с WNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

<h2><a name="connecting-app"></a><span class="short-header">Подключение приложения</span>Подключение приложения к концентратору уведомлений</h2>

1. Добавьте ссылку на библиотеку обмена сообщениями Azure для магазина Windows с помощью <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">пакета NuGet WindowsAzure.Messaging.Managed</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.Messaging.Managed

    и нажмите клавишу **ВВОД**.

2. Откройте файл App.xaml.cs и добавьте следующие инструкции `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

3. Добавьте в класс `App` файла App.xaml.cs следующий код: Обязательно замените заполнитель "имя концентратора" на имя концентратора уведомлений, отображаемое на вкладке **Концентраторы уведомлений** портала (например, в предыдущем примере это **mynotificationhub2**):
	
	    private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
			
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
			var result = await hub.RegisterNativeAsync(channel.Uri);
            
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    Обязательно вставьте имя концентратора и строку подключения с именем **DefaultListenSharedAccessSignature**, полученные в предыдущем разделе.
    Этот код получает ChannelURI для приложения из WNS, а затем регистрирует это значение ChannelURI для концентратора уведомлений.
    
4. В верхней части обработчика событий в **OnLaunched** в файле App.xaml.cs добавьте в новый метод **InitNotificationsAsync** следующий вызов:

        InitNotificationsAsync();

    Это гарантирует регистрацию ChannelURI в концентраторе уведомлений при каждом запуске приложения.

5. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.
   
   	![][19]

<h2><a name="send"></a><span class="short-header">Отправка уведомления</span>Отправка уведомления из серверной части</h2>

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике уведомления отправляются с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

Порядок отправки уведомлений с помощью приложения .NET:

1. Оставив предыдущее решение открытым в Visual Studio, дважды щелкните **Package.appxmanifest** в обозревателе решений, чтобы открыть его в редакторе Visual Studio.

2. Выполните прокрутку вниз до элемента **Все активы изображений** и щелкните **Эмблема**. В поле **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**.

   	![][18]

   	В меню **Файл** выберите **Сохранить все**.

3. Создайте новое консольное приложение Visual C#: 

   	![][13]

4. Добавьте ссылку на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите следующее:

        Install-Package WindowsAzure.ServiceBus

    и нажмите клавишу **ВВОД**.

5. Откройте файл Program.cs и добавьте следующую инструкцию `using`:

        using Microsoft.ServiceBus.Notifications;

6. Добавьте в класс `Program` следующий метод. Обязательно замените заполнитель "имя концентратора" на имя концентратора уведомлений, отображаемое на вкладке **Концентраторы уведомлений** портала:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Обязательно вставьте имя концентратора и строку подключения с именем **DefaultFullSharedAccessSignature**, полученные в разделе "Настройка концентратора уведомлений". Обратите внимание, что эта строка подключения имеет **полный** доступ, а не доступ к **прослушиванию**.

7. Затем добавьте следующие строки в метод `Main`:

         SendNotificationAsync();
		 Console.ReadLine();

8. Нажмите клавишу **F5**, чтобы запустить приложение. Вы получите всплывающее уведомление.

   	![][14]

В [каталоге всплывающих уведомлений], [каталоге уведомлений на плитке] и [обзоре эмблем] в MSDN можно найти самые разнообразные полезные данные.

Чтобы отправить уведомление с помощью мобильной службы, следуйте инструкциям из раздела [Приступая к работе с мобильными службами], а затем выполните следующие действия:

1. Выполните вход на [портал управления Azure] и щелкните мобильную службу.

2. Откройте расположенную сверху вкладку **Планировщик**.

   	![][15]

3. Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.

   	![][16]

4. После создания задания щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.

5. Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученными ранее. По завершении нажмите кнопку **Сохранить** на нижней панели.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
        notificationHubService.wns.sendToastText01(
            null,
            {
                text1: 'Hello from Mobile Services!!!'
            },
            function (error) {
                if (!error) {
                    console.warn("Notification successful");
                }
        });

6. Нажмите кнопку **Запустить один раз** на нижней панели. Вы получите всплывающее уведомление.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows. Чтобы сориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в разделах [Руководство по использованию концентраторов уведомлений] и [Инструкции по использованию концентраторов уведомлений для магазина Windows].

<!-- Anchors. -->
[Регистрация приложения для получения push-уведомлений]: #register
[Настройка концентратора уведомлений]: #configure-hub
[Подключение приложения к концентратору уведомлений]: #connecting-app
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png

<!-- URLs. -->
[страницу "Отправить приложение"]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js

[Портал управления Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководство по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для магазина Windows]: http://msdn.microsoft.com/ru-ru/library/jj927172.aspx

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet

[каталоге всплывающих уведомлений]: http://msdn.microsoft.com/ru-ru/library/windows/apps/hh761494.aspx
[каталоге уведомлений на плитке]: http://msdn.microsoft.com/ru-ru/library/windows/apps/hh761491.aspx
[обзоре эмблем]: http://msdn.microsoft.com/ru-ru/library/windows/apps/hh779719.aspx

