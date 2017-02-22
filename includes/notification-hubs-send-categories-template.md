
В этом разделе показано, как отправлять экстренные новости в виде шаблонных уведомлений с тегами из консольного приложения .NET.

Если вы используете мобильные приложения, см. руководство по [добавлению push-уведомлений в мобильные приложения] (выберите платформу в верхней части страницы).

Если вы хотите использовать Java или PHP, см. руководство по [использованию Центров уведомлений из Java и PHP]. Можно отправлять уведомления из любого серверного компонента с помощью [интерфейса REST Центров уведомлений].

Пропустите шаги 1–3, если консольное приложение для отправки уведомлений создано после завершения работы с руководством по [началу работы с Центрами уведомлений].

1. В Visual Studio создайте новое консольное приложение Visual C#:
   
       ![][13]
2. В главном меню Visual Studio выберите пункт **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**, а затем в окне консоли введите следующую команду и нажмите клавишу **ВВОД**:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    После этого будет добавлена ссылка на пакет SDK для Центров уведомлений Azure с помощью [пакета NuGet Microsoft.Azure.Notification Hubs].
3. Откройте файл Program.cs и добавьте следующий оператор `using` :
   
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
5. В приведенном выше коде замените заполнители `<hub name>` и `<connection string with full access>` именем центра уведомлений и строкой подключения для *DefaultFullSharedAccessSignature*, полученными ранее на панели мониторинга центра уведомлений.
6. Добавьте следующие строки в метод **Main** :
   
         SendTemplateNotificationAsync();
         Console.ReadLine();
7. Выполните сборку консольного приложения.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[началу работы с Центрами уведомлений]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[интерфейса REST Центров уведомлений]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx (REST API Центров уведомлений)
[Добавление push-уведомлений в приложение Windows]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[использованию Центров уведомлений из Java и PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[пакета NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/ (Центры уведомлений Microsoft Azure 1.0.7)


<!--HONumber=Nov16_HO3-->


