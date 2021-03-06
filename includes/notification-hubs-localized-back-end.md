---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/02/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 752feca30fdca663aaf8bd88e6686781b9065682
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33836687"
---
При отправке шаблонных уведомлений необходимо предоставить только набор свойств. В этом сценарии набор свойств содержит локализованную версию текущих новостей.

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }



### <a name="send-notifications-using-a-c-console-app"></a>Отправка уведомлений с помощью консольного приложения C#
В этом разделе показано, как отправлять уведомления с помощью консольного приложения. Код выполняет широковещательную рассылку уведомлений Магазина Windows и устройств iOS. Измените метод `SendTemplateNotificationAsync` в созданном ранее консольном приложении с помощью следующего кода:

```csharp
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
```

Метод SendTemplateNotificationAsync доставляет локализованные части новостей на **все** устройства (вне зависимости от платформы). Центр уведомлений создает и доставляет правильные собственные полезные данные на все устройства, подписанные на определенный тег.

### <a name="sending-notification-with-mobile-services"></a>Отправка уведомления с помощью мобильных служб
В планировщике мобильных служб можно использовать следующий сценарий:

```csharp
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
```

