В этом разделе описывается обновление кода в существующем проекте серверной части мобильных приложений, которое позволит отправлять push-уведомления при каждом добавлении нового элемента. Это реализуется с помощью [шаблонов](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) Центров уведомлений, включая отправку push-уведомлений между разными платформами. Разные клиенты регистрируются для обмена push-уведомлениями с помощью шаблонов; одно такое универсальное push-уведомление можно получать на всех клиентских платформах.

Выберите одну из процедур ниже, которая соответствует типу вашего серверного проекта: [серверный проект .NET](#dotnet) или [серверный проект Node.js](#nodejs).

### <a name="dotnet"></a>Серверный проект .NET
1. В Visual Studio щелкните правой кнопкой мыши серверный проект и выберите пункт **Управление пакетами NuGet**. Найдите `Microsoft.Azure.NotificationHubs` и нажмите кнопку **Установить**. Это устанавливает библиотеку центров уведомлений для отправки уведомлений из серверной части.
2. В серверном проекте откройте **Контроллеры** > **TodoItemController.cs** и добавьте следующие операторы using:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. В метод **PostTodoItem** добавьте следующий код после вызова **InsertAsync**.  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    При вставке нового элемента отправляется шаблонное уведомление, содержащее item.Text.
4. Повторная публикация серверного проекта

### <a name="nodejs"></a>Серверный проект Node.js
1. При необходимости [скачайте серверный проект быстрого запуска](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) или воспользуйтесь [онлайн-редактором на портале Azure](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Замените существующий код в файле todoitem.js следующим кодом:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;  

    При вставке нового элемента отправляется шаблонное уведомление, содержащее item.Text.
3. При редактировании этого файла на локальном компьютере повторно опубликуйте серверный проект.
