<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Приступая к работе" pageTitle="Приступая к работе с концентраторами уведомлений Azure" metaKeywords="" description="Узнайте, как использовать концентраторы уведомлений Azure для работы с push-уведомлениями." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Приступая к работе с концентраторами уведомлений" authors=""  solutions="" writer="elioda" manager="" editor=""  />
# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/manage/services/notification-hubs/get-started-notification-hubs-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios" title="iOS">iOS</a><a href="/ru-ru/manage/services/notification-hubs/get-started-notification-hubs-android" title="Android">Android</a><a href="/ru-ru/manage/services/notification-hubs/getting-started-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/manage/services/notification-hubs/getting-started-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений в приложение Windows Phone 8. 
В этом учебнике вам предстоит создать пустое приложение Windows Phone 8, получающее push-уведомления с помощью службы push-уведомлений Майкрософт (MPNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие шаги для включения push-уведомлений:

1. [Создание концентратора уведомлений]
2. [Подключение приложения к концентратору уведомлений]
3. [Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств. Для данного учебника требуется следующее:

+ [Microsoft Visual Studio 2012 Express для Windows Phone] или более поздняя версия.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Windows Phone 8. 

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>


<h2><a name="configure-hub"></a><span class="short-header">Создание концентратора уведомлений</span>Создание концентратора уведомлений</h2>

1. Войдите на [портал управления Azure] и щелкните элемент **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][7]

3. Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][8]

4. Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

   	![][9]

5. Откройте расположенную сверху вкладку **Концентраторы уведомлений** и выберите недавно созданный концентратор уведомлений.

   	![][10]

6. Щелкните расположенный внизу элемент **Сведения о подключении**. Запишите значения двух строк подключения.

   	![][12]

7. Откройте вкладку **Настройка**, а затем установите флажок **Включить push-уведомления без проверки подлинности** в разделе **Параметры уведомлений Windows Phone**.

   	![][15]

Теперь у вас есть строки подключения, чтобы зарегистрировать приложение Windows Phone 8 и отправлять уведомления.

<div class="dev-callout"><b>Примечание.</b>
		<p>В этом учебнике MPNS используется в режиме без проверки подлинности. Режим MPNS без проверки подлинности налагает ограничения на использование уведомлений, отправляемых для каждого канала. Концентраторы уведомлений поддерживают <a href="http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/ff941099(v=vs.105).aspx">режим работы MPNS с проверкой подлинности</a>. <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

<h2><a name="connecting-app"></a><span class="short-header">Подключение приложения</span>Подключение приложения к концентратору уведомлений</h2>

1. В Visual Studio создайте новое консольное приложение Windows Phone 8.

   	![][13]

1. Добавьте ссылку на библиотеку обмена сообщениями Azure для магазина Windows с помощью <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">пакета NuGet WindowsAzure.Messaging.Managed</a>. В меню Visual Studio последовательно выберите **Сервис**, **Консоль диспетчера пакетов** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.Messaging.Managed

    и нажмите клавишу ВВОД.

2. Откройте файл App.xaml.cs и добавьте следующие инструкции `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

3. В следующем коде, расположенном в верхней части метода **Application_Launching** в App.xaml.cs, выполните следующие действия:
	
	    var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    Обязательно вставьте имя концентратора и строку подключения с именем **DefaultListenSharedAccessSignature**, полученные в предыдущем разделе.
    Этот код получает ChannelURI для приложения из MPNS, а затем регистрирует это значение ChannelURI для концентратора уведомлений. Он также гарантирует регистрацию ChannelURI в концентраторе уведомлений при каждом запуске приложения.

	<div class="dev-callout"><b>Примечание.</b>
		<p>В этом учебнике на устройство отправляется всплывающее уведомление. При отправке уведомления на плитке вместо этого необходимо вызвать для канала метод <strong>BindToShellTile</strong>. Чтобы поддерживать одновременно и всплывающие уведомления, и уведомления на плитке, следует вызвать оба метода — <strong>BindToShellTile</strong> и <strong>BindToShellToast</strong>. </p>
	</div>
    
4. В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml, откройте вкладку **Возможности** и убедитесь, что установлен флажок для возможности **ID___CAP___PUSH_NOTIFICATION**.

   	![][14]

   	Это гарантирует, что приложение сможет получать push-уведомления.
	
5. Нажмите клавишу F5, чтобы запустить приложение.

<h2><a name="send"></a><span class="short-header">Отправка уведомления</span>Отправка уведомления из серверной части</h2>

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике уведомления отправляются с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

Порядок отправки уведомлений с помощью приложения .NET:

1. Создайте новое консольное приложение Visual C#: 

   	![][213]

2. Добавьте ссылку на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Консоль диспетчера пакетов** и **Консоль диспетчера пакетов**. Затем в окне консоли введите:

        Install-Package WindowsAzure.ServiceBus

    и нажмите клавишу ВВОД.

2. Откройте файл Program.cs и добавьте следующую инструкцию `using`:

        using Microsoft.ServiceBus.Notifications;

3. Добавьте в класс `Program` следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Обязательно вставьте имя концентратора и строку подключения с именем DefaultFullSharedAccessSignature, полученные в разделе "Настройка концентратора уведомлений". Обратите внимание, что эта строка подключения имеет полный доступ, а не доступ к прослушиванию.


4. Затем добавьте следующую строку в метод Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Нажмите клавишу F5, чтобы запустить приложение. Вы получите всплывающее уведомление. Убедитесь, что эмулятор Windows Phone запущен, а приложение закрыто.

В [каталоге всплывающих уведомлений] и [каталоге уведомлений на плитке] MSDN можно найти самые разнообразные полезные данные.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows Phone 8. Чтобы сориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений].

<!-- Anchors. -->
[Создание концентратора уведомлений]: #configure-hub
[Подключение приложения к концентратору уведомлений]: #connecting-app
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]:#next-steps

<!-- Images. -->







[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png

[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png

[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Microsoft Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service

[Портал управления Azure]: https://manage.windowsazure.com/
[руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для Windows Phone 8]: tbd!!!
[Режим работы MPNS с проверкой подлинности]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
[каталоге всплывающих уведомлений]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/jj662938(v=vs.105).aspx
[каталоге уведомлений на плитке]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/hh202948(v=vs.105).aspx

