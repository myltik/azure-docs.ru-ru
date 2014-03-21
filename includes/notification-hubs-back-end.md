
В этом разделе показано, как отправлять уведомления двумя способами:

- [Из консольного приложения]
- [Из мобильных служб]

Оба серверных компонента отправляют уведомления как для магазина Windows, так и устройств iOS. Можно отправлять уведомления из любого серверного компонента с помощью [интерфейса REST концентраторов уведомлений]. 

<h3><a name="console"></a>Для отправки уведомлений из консольного приложения в C#</h3>

Пропустите шаги 1-3, если создано консольное приложение при завершении раздела [Приступая к работе с концентраторами уведомлений][get-started].

1. В Visual Studio создайте новое консольное приложение Visual C#: 

   	![][13]

2. В главном меню Visual Studio выберите пункт **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**, затем в окне консоли введите следующую команду и нажмите клавишу **Ввод**:

        Install-Package WindowsAzure.ServiceBus
 	
	Это добавляет ссылку на пакет Windows Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета WindowsAzure.ServiceBus NuGet</a>. 

3. Откройте файл Program.cs и добавьте следующий оператор "using":

        using Microsoft.ServiceBus.Notifications;

4. Добавьте следующий метод в класс "Program" или замените его, если он уже существует:

        private static async void SendNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");
		
		    // Create an array of breaking news categories.
		    var categories = new string[] { "World", "Politics", "Business", 
		        "Technology", "Science", "Sports"};
		
            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";
                    // Send a WNS notification using the template.            
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";
                    // Send an MPNS notification using the template.            
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    // Send an APNS notification using the template.
                    await hub.SendAppleNativeNotificationAsync(alert, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }

	Этот код отправляет уведомления для каждого из шести тегов в массиве строк для магазина Windows, Windows Phone и устройств iOS. Использование тегов гарантирует, что устройства будут получать уведомления только для зарегистрированных категорий.
	
	<div class="dev-callout"><strong>Примечание</strong> 
		<p>Этот серверный код поддерживает магазин Windows, Windows Phone и клиенты iOS. Методы Send возвращают ошибочный ответ, когда концентратор уведомлений еще не настроен для определенной платформы клиента. </p>
	</div>

6. В приведенном выше коде замените заполнители "<hub name>" и "<connection string with full access>" именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученного ранее.

7. Добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

Теперь можно приступать к [запуску приложения и созданию уведомлений].

###<a name="mobile-services"></a>Для отправки уведомлений из мобильных служб

Чтобы отправить уведомление, используя мобильную службу, выполните следующие действия:

0. Завершите работу с разделом учебника [Приступая к работе с мобильными службами], чтобы создать свою мобильную службу.

1. Выполните вход на [портал управления Windows Azure], щелкните пункт "Мобильные службы", а затем щелкните свою мобильную службу.

2. Щелкните вкладку **Планировщик**, а затем нажмите кнопку **Создать**.

   	![][15]

3. В поле **Создать задание** введите имя, выберите пункт **По требованию** и поставьте флажок, чтобы принять.

   	![][16]

4. После создания задания щелкните имя задания, а затем на вкладке **Скрипт** вставьте следующий скрипт в функцию запланированного задания: 

	    var azure = require('azure');
	    var notificationHubService = azure.createNotificationHubService(
				'<hub name>', 
				'<connection string with full access>');

   		 // Create an array of breaking news categories.
		    var categories = ['World', 'Politics', 'Business', 'Technology', 'Science', 'Sports'];
		    for (var i = 0; i < categories.length; i++) {
		        // Send a WNS notification.
		        notificationHubService.wns.sendToastText01(categories[i], {
		            text1: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		        // Send a MPNS notification.
		        notificationHubService.mpns.sendToast(categories[i], {
		            text1: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		        // Send an APNS notification.
		        notificationHubService.apns.send(categories[i], {
		            alert: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		    }

	Этот код отправляет уведомления для каждого из шести тегов в массиве строк для магазина Windows, Windows Phone и устройств iOS. Использование тегов гарантирует, что устройства будут получать уведомления только для зарегистрированных категорий.

6. В приведенном выше коде замените заполнители "<hub name>" и "<connection string with full access>" именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученного ранее.

7. Добавьте следующую вспомогательную функцию после функции запланированного задания, а затем нажмите кнопку **Сохранить**: 
	
        function sendComplete(error) {
 		   if (error) {
	            // An exception is raised when there are no devices registered for 
	            // the iOS, Windows Store, or Windows Phone platforms. Consider using template 
	            // notifications instead.
	            //console.warn("Notification failed:" + error);
	        }
	    }
	
	<div class="dev-callout"><strong>Примечание</strong> 
		<p>Этот код поддерживает магазин Windows, Windows Phone и клиенты iOS. Методы Send возвращают ошибочный ответ, когда регистрация не существует для определенной платформы. Чтобы избежать этого, рассмотрите возможность использования регистраций шаблонов для отправки одного уведомления для нескольких платформ. Например, см. раздел <a href="/ru-ru/manage/services/notification-hubs/breaking-news-localized-dotnet/">Использование концентраторов уведомлений для передачи локализованных экстренных новостей</a>. </p>
	</div>

Теперь можно приступать к [запуску приложения и созданию уведомлений].

<!-- Anchors -->
[Из консольного приложения]: #console
[Из мобильных служб]: #mobile-services
[Запуск приложения и создание уведомлений]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[приступая к работе]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Использование концентраторов уведомлений для отправки уведомлений пользователям]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Портал управления Windows Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководства по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для магазина Windows]: http://msdn.microsoft.com/ru-ru/library/jj927172.aspx
[Интерфейс REST концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx

