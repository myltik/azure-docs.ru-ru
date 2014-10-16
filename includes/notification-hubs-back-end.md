В этом разделе показано, как отправлять уведомления из приложения консоли .NET и других приложений.
Если вы используете мобильные службы, см. учебники [Начало работы с push-уведомлениями][]. Если вы хотите использовать Java или PHP, см. раздел [Использование концентраторов уведомлений из Java/PHP][]. Можно отправлять уведомления из любого серверного компонента с помощью [интерфейса REST концентраторов уведомлений][].

Следующий код отправляет уведомления в Магазин Windows, на устройства Windows Phone, iOS и Android.

Пропустите шаги 1-3, если создано консольное приложение при завершении раздела [Приступая к работе с концентраторами уведомлений][].

1.  В Visual Studio создайте новое консольное приложение Visual C\#:

    ![][]

2.  В главном меню Visual Studio выберите пункт **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**, затем в окне консоли введите следующую команду и нажмите клавишу **Ввод**:

        Install-Package WindowsAzure.ServiceBus

    Это добавляет ссылку на пакет SDK служебной шины Azure с помощью [пакета NuGet WindowsAzure.ServiceBus][].

3.  Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

4.  В классе `Program` добавьте следующий метод или замените его, если он уже существует:

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
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

                    // Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
         }

    Этот код отправляет уведомления для каждого из шести тегов в массиве строк для магазина Windows, Windows Phone и устройств iOS. Использование тегов гарантирует, что устройства будут получать уведомления только для зарегистрированных категорий.

    <div class="dev-callout"><strong>Примечание.</strong> 
    <p>Этот код сервера поддерживает клиенты Магазина Windows, Windows Phone, iOS и Android. Методы Send возвращают ошибочный ответ, когда концентратор уведомлений еще не настроен для определенной платформы клиента. </p>
</div>

5.  В приведенном выше коде замените заполнители `<hub name>` и `<connection string with full access>` именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature*, полученными ранее.

6.  Добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
         Console.ReadLine();

<!-- Anchors -->
<!-- Images. -->
<!-- URLs. -->

  [Начало работы с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Использование концентраторов уведомлений из Java/PHP]: /ru-ru/documentation/articles/notification-hubs-java-backend-how-to/
  [интерфейса REST концентраторов уведомлений]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [Приступая к работе с концентраторами уведомлений]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  []: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
  [пакета NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
