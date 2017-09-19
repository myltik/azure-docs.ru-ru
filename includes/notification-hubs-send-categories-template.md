
В этом разделе вы будете отправлять экстренные новости в виде шаблонных уведомлений с тегами из консольного приложения .NET.

Если вы используете компонент "Мобильные приложения" службы приложений Microsoft Azure, см. статью [Добавление push-уведомлений в приложение Windows] (выберите платформу в верхней части страницы).

Если вы хотите использовать Java или PHP, см. руководство по [использованию Центров уведомлений из Java или PHP]. Отправлять уведомления можно из любого серверного компонента с помощью [интерфейса REST Центров уведомлений].

Если вы создали консольное приложение для отправки уведомлений при изучении руководства по [началу работы с Центрами уведомлений], пропустите шаги 1–3.

1. Создайте в Visual Studio новое консольное приложение Visual C#.
   
      ![Установка связи с консольным приложением][13]

2. В главном меню Visual Studio выберите **Инструменты** > **Диспетчер пакетов библиотеки** > **Консоль диспетчера пакетов**, а затем в окне консоли введите следующую строку:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Нажмите клавишу **ВВОД**.  
    Это действие добавляет ссылку на пакет SDK для Центров уведомлений Azure с помощью [пакета NuGet Microsoft.Azure.Notification Hubs].

4. Откройте файл Program.cs и добавьте следующий оператор `using`:
   
        using Microsoft.Azure.NotificationHubs;

5. В класс `Program` добавьте следующий метод или замените его, если он уже существует:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
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

5. В предыдущем коде замените заполнители `<hub name>` и `<connection string with full access>` именем центра уведомлений и строкой подключения для *DefaultFullSharedAccessSignature*, полученными ранее на панели мониторинга центра уведомлений.

6. Добавьте следующие строки в метод **Main**:
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. Выполните сборку консольного приложения.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[началу работы с Центрами уведомлений]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[интерфейса REST Центров уведомлений]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx (REST API Центров уведомлений)
[Добавление push-уведомлений в приложение Windows]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[использованию Центров уведомлений из Java или PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[пакета NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/ (Центры уведомлений Microsoft Azure 1.0.7)
