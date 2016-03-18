
В этом разделе показано, как отправлять уведомления из консольного приложения .NET и других приложений. Сведения об использовании уведомлений в мобильных службах см. в учебниках [Приступая к работе с push-уведомлениями](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md). Если вы хотите использовать Java или PHP, см. раздел [Использование концентраторов уведомлений из Java/PHP](../articles/notification-hubs/notification-hubs-java-backend-how-to.md). Уведомления можно отправлять из любого серверного компонента с помощью [интерфейса REST центров уведомлений](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

Следующий код отправляет уведомления приложениям Магазина Windows и на устройства Windows Phone, iOS и Android.

Пропустите шаги 1-3, если создано консольное приложение при завершении раздела [Приступая к работе с концентраторами уведомлений][get-started].

1. В Visual Studio создайте новое консольное приложение Visual C#: 

   	![][13]

2. В главном меню Visual Studio выберите пункт **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**, затем в окне консоли введите следующую команду и нажмите клавишу **ВВОД**:

        Install-Package Microsoft.Azure.NotificationHubs
 	
	После этого будет добавлена ссылка на пакет SDK для Центров уведомлений Azure с помощью <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">пакета Microsoft.Azure.Notification Hubs NuGet</a>.

3. Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.Azure.NotificationHubs;

4. В класс `Program` добавьте следующий метод или замените его, если он уже существует:

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
                    var wnsToast = "<toast><visual><binding template="ToastText01">" 
                        + "<text id="1">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version="1.0" encoding="utf-8"?>" +
                        "<wp:Notification xmlns:wp="WPNotification">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{"aps":{"alert":"Breaking " + category + " News!"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

					// Define an Android notification.
                    var notification = "{"data":{"msg":"Breaking " + category + " News!"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }

	Этот код отправляет уведомления по каждому из шести тегов в массиве строк для приложений Магазина Windows и устройств Windows Phone и iOS. Использование тегов гарантирует, что устройства будут получать уведомления только зарегистрированных категорий.
	
	> [AZURE.NOTE]Этот код сервера поддерживает клиентов Магазина Windows, Windows Phone, iOS и Android. Методы "Send" возвращают ошибочный ответ, когда центр уведомлений еще не настроен для определенной платформы клиента.

6. В приведенном выше коде замените заполнители `<hub name>` и `<connection string with full access>` на имя центра уведомлений и строку подключения для *DefaultFullSharedAccessSignature*, полученные ранее.

7. Добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

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

<!---HONumber=AcomDC_1210_2015-->