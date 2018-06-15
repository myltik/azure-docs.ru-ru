---
title: Отправка локализованных уведомлений в приложения Windows с использованием Центров уведомлений Azure | Документация Майкрософт
description: Узнайте, как использовать Центры уведомлений Azure для отправки уведомлений о локализованных экстренных новостях.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 517e7ae3871a1ed816ea407ad47c9033a1bb5a0e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776895"
---
# <a name="tutorial-push-localized-notifications-to-windows-apps-by-using-azure-notification-hubs"></a>Руководство по отправке локализованных уведомлений в приложения Windows с помощью Центров уведомлений Azure
> [!div class="op_single_selector"]
> * [C# в Магазине Windows](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Обзор
В этом руководстве показано, как принудительно отправлять уведомления на мобильные устройства, зарегистрированные в службе "Центры уведомлений". В этом руководстве рассматривается обновление приложений, созданных в руководстве [Использование концентраторов уведомлений для передачи экстренных новостей](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md), для поддержки следующих сценариев: 

- приложение Магазина Windows позволяет клиентским устройствам указывать язык и подписываться на различные категории экстренных новостей;
- внутреннее приложение рассылает уведомления, используя функции **tag** и **template** Центров уведомлений Azure.

Когда вы завершите работу с руководством, в мобильном приложении можно будет зарегистрироваться в интересующих вас категориях, а также указать язык получения уведомлений. Внутреннее приложение отправляет уведомления, локализованные на указанном языке и с учетом устройства. 

Из этого руководства вы узнаете, как выполнять такие задачи: 

> [!div class="checklist"]
> * обновление приложения Windows для поддержки информации о языковом стандарте;
> * обновление внутреннего приложения для отправки локализованных уведомлений;
> * Тестирование приложения

## <a name="prerequisites"></a>предварительным требованиям
Выполните инструкции в руководстве [Отправка push-уведомлений на конкретные устройства (универсальная платформа Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). 

В руководстве [Отправка push-уведомлений на конкретные устройства (универсальная платформа Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) создается приложение, использующее **теги** для подписки на уведомления в разных новостных **категориях**. В этом руководстве будут продемонстрированы способы использования **шаблонов** Центров уведомлений, которые позволяют легко доставлять **локализованные** уведомления об экстренных новостях.

В общих чертах, шаблоны позволяют указать формат, в котором конкретное устройство должно получать уведомления. Шаблон указывает точный формат полезных данных путем обращения к свойствам, которые являются частью сообщения, отправленного сервером вашего приложение. В этом руководстве внутреннее приложение отправляет независимое от языкового стандарта сообщение, содержащее все поддерживаемые языки:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Устройство регистрируется с шаблоном, который ссылается на нужное свойство. Например, приложение Магазина Windows, которое хочет получать всплывающее сообщение на английском, будет зарегистрировано для следующего шаблона с любыми соответствующими тегами:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Дополнительные сведения о шаблонах см. в [этой статье](notification-hubs-templates-cross-platform-push-messages.md). 

## <a name="update-windows-app-to-support-locale-information"></a>Обновление приложения Windows для поддержки информации о языковом стандарте

1. Откройте решение Visual Studio, созданное для руководства [Отправка push-уведомлений на конкретные устройства (универсальная платформа Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). 
2. Добавьте в файл **MainPage.xaml** поле языкового стандарта:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. В классе **Notifications** добавьте параметр языкового стандарта к методам **StoreCategoriesAndSubscribe** и **SubscribeToCategories**.

    ```csharp   
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Вместо вызова метода *RegisterNativeAsync* вызовите *RegisterTemplateAsync*. Зарегистрируйте определенный формат уведомлений, в котором шаблон зависит от языкового стандарта. Так как может потребоваться зарегистрировать более одного шаблона (например, один для всплывающих уведомлений и один для плиток), введите имя шаблона (например, "localizedWNSTemplateExample"). Вам также необходимо назвать их, чтобы иметь возможность обновить или удалить их.
   
    Если устройство регистрирует несколько шаблонов с тем же тегом, одно входящее сообщение для этого тега приведет к передаче нескольких уведомлений на устройство (по одному для каждого шаблона). Это полезно, когда одного логическое сообщение должно привести к нескольким визуальным уведомлениям в приложении Магазина Windows, например в виде эмблемы и во всплывающем окне.
3. Для получения сохраненного языкового стандарта добавьте следующий метод:
   
    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

4. В файле **MainPage.xaml.cs** обновите обработчик нажатия кнопки, чтобы он получал текущее значение языкового стандарта (поля со списком Locale) и передавал его в вызове класса Notifications, как показано ниже:
   
    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
4. Наконец, в файле App.xaml.cs обязательно обновите метод `InitNotificationsAsync` , чтобы получать языковый стандарт и использовать его при подписке.

    ```csharp   
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

## <a name="send-localized-notifications-from-your-back-end"></a>Отправка уведомлений из серверной части
При отправке уведомлений шаблона необходимо предоставить набор свойств. В этом руководстве внутреннее приложение отправит набор свойств, содержащий локализованную версию текущих новостей, например:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

В этом разделе вы обновляете проект консольного приложения в решении. Измените метод `SendTemplateNotificationAsync` в созданном ранее консольном приложении с помощью следующего кода: 

> [!IMPORTANT]
> Укажите имя и строку подключения с полным доступом для центра уведомлений в коде. 


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

Этот простой вызов будет доставлять правильно локализованную часть новостей на **все** ваши устройства, независимо от платформы, так как Центр уведомлений создает и доставляет правильные собственные полезные данные на все устройства, подписанные на определенный тег.

## <a name="test-the-app"></a>Тестирование приложения
1. Запустите универсальное приложение Магазина Windows. Дождитесь появления сообщения **Регистрация выполнена успешно**.

    ![Мобильное приложение и регистрация](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
1. Выберите **категории** и **языковой стандарт**, а затем щелкните **Подписаться**. Приложение преобразует выбранные категории в теги и запрашивает у концентратора уведомлений новую регистрацию устройств для выбранных тегов.

    ![Мобильное приложение](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
2.  Отобразится сообщение о **подтверждении** **подписок**. 

    ![Сообщение о подписке](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)
1. После получения подтверждения запустите **консольное приложение**, чтобы отправить уведомления для каждой категории и на каждом поддерживаемом языке. Убедитесь, что вы получаете уведомление только для категорий, на которые подписаны, и что сообщение соответствует выбранному языку. 

    ![Сообщения уведомлений](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)
 

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы узнали, как отправлять локализованные push-уведомления на определенные устройства, с регистрацией которых связаны теги. Сведения о том, как принудительно отправлять уведомления конкретным пользователям, которые могут использовать несколько устройств, см. в следующем руководстве: 

> [!div class="nextstepaction"]
>[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)


<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Use Notification Hubs to send breaking news]: /notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
