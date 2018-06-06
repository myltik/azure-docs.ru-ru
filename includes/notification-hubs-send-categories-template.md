---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 19352df7abff23ed44521a11e7907c84c8c0327f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "33835854"
---
В этом разделе вы будете отправлять экстренные новости в виде шаблонных уведомлений с тегами из консольного приложения .NET. 

1. Создайте в Visual Studio новое консольное приложение Visual C#.
   
      ![Установка связи с консольным приложением][13]

2. В главном меню Visual Studio выберите **Инструменты** > **Диспетчер пакетов библиотеки** > **Консоль диспетчера пакетов**, а затем в окне консоли введите следующую строку:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Нажмите клавишу **ВВОД**.  
    Это действие добавляет ссылку на пакет SDK для Центров уведомлений Azure с помощью [пакета NuGet Microsoft.Azure.Notification Hubs].

4. Откройте файл Program.cs и добавьте следующий оператор `using`:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. В класс `Program` добавьте следующий метод или замените его, если он уже существует:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

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
    ```   
   
    Этот код отправляет шаблонное уведомление по каждому из шести тегов в массиве строк. Использование тегов гарантирует, что устройства будут получать уведомления только зарегистрированных категорий.

5. В предыдущем коде замените заполнители `<hub name>` и `<connection string with full access>` именем центра уведомлений и строкой подключения для *DefaultFullSharedAccessSignature*, полученными ранее на панели мониторинга центра уведомлений.

6. Добавьте следующие строки в метод **Main**:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Выполните сборку консольного приложения.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[пакета NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/ (Центры уведомлений Microsoft Azure 1.0.7)
