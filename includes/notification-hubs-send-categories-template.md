
В этом разделе показано, как отправлять экстренные новости в виде шаблонных уведомлений с тегами из консольного приложения .NET.

При использовании мобильных приложений обратитесь к учебнику [Добавление push-уведомлений для мобильных приложений](../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) и выберите платформу в верхней части страницы.

Если вы хотите использовать Java или PHP, см. раздел [Использование концентраторов уведомлений из Java/PHP](../articles/notification-hubs/notification-hubs-java-backend-how-to.md). Уведомления можно отправлять из любого серверного компонента с помощью [интерфейса REST центров уведомлений](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

Пропустите шаги 1–3, если консольное приложение для отправки уведомлений было создано при завершении раздела [Приступая к работе с центрами уведомлений][get-started].

1. В Visual Studio создайте новое консольное приложение Visual C#: 

   	![][13]

2. В главном меню Visual Studio выберите пункт **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**, затем в окне консоли введите следующую команду и нажмите клавишу **ВВОД**:

        Install-Package Microsoft.Azure.NotificationHubs
 	
	После этого будет добавлена ссылка на пакет SDK для центров уведомлений Azure с помощью <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">пакета NuGet Microsoft.Azure.Notification Hubs</a>.

3. Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.Azure.NotificationHubs;

4. В класс `Program` добавьте следующий метод или замените его, если он уже существует:

        private static async void SendTemplateNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
											"Technology", "Science", "Sports"};

            // Sending the notification as a template notification. All template registrations that contain 
			// "messageParam" and the proper tags will receive the notifications. 
			// This includes APNS, GCM, WNS, and MPNS template registrations.

            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";            
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
		 }

	Этот код отправляет шаблонное уведомление по каждому из шести тегов в массиве строк. Использование тегов гарантирует, что устройства будут получать уведомления только зарегистрированных категорий.

6. В приведенном выше коде замените заполнители `<hub name>` и `<connection string with full access>` именем центра уведомлений и строкой подключения для *DefaultFullSharedAccessSignature*, полученными ранее на панели мониторинга центра уведомлений.

7. Добавьте следующие строки в метод **Main**:

         SendTemplateNotificationAsync();
		 Console.ReadLine();

8. Выполните сборку консольного приложения.

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!---HONumber=AcomDC_0330_2016-->