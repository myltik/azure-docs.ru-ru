



При отправке уведомлений шаблона необходимо предоставить набор свойств, в нашем случае мы отправим набор свойств, содержащий локализованную версию текущих новостей, например:

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


В этом разделе показано, как отправлять уведомления с помощью консольного приложения.

Включенный код выполняет широковещательную рассылку как для магазина Windows, так и для устройств iOS, поскольку серверный компонент может выполнять широковещательную рассылку на любое из поддерживаемых устройств.

### Для отправки уведомлений с помощью консольного приложения C
Измените метод `SendTemplateNotificationAsync` в созданном ранее консольном приложении с помощью следующего кода. Обратите внимание, что в данном случае нет необходимости отправлять несколько уведомлений для различных языков и платформ.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Также обратите внимание, что этот простой вызов будет доставлять правильно локализованную часть новостей на **все** ваши устройства, независимо от платформы, так как центр уведомлений создает и доставляет правильные собственные полезные данные на все устройств, подписанные на определенный тег.

### Отправка уведомления с помощью мобильных служб
В планировщике мобильных служб можно использовать следующий сценарий.

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });


<!---HONumber=AcomDC_1217_2015-->