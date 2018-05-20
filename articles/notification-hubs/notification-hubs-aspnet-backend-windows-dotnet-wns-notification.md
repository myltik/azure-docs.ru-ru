---
title: Отправка уведомлений определенным пользователям с помощью службы "Центры уведомлений Azure" | Документация Майкрософт
description: Узнайте, как отправлять уведомления определенным пользователям, используя приложения универсальной платформы Windows (UWP).
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.author: dimazaid
ms.openlocfilehash: ee0e78402515a733731e6faf21b09886674cda51
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Руководство по отправке уведомлений определенным пользователям с помощью службы "Центры уведомлений Azure"
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Обзор
В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Для проверки подлинности клиентов используется серверная часть веб-API ASP.NET. Когда серверная часть проверяет подлинность пользователя клиентского приложения, к регистрации уведомления автоматически добавляется тег. Серверная часть использует этот тег для отправки уведомлений определенному пользователю. 

> [!NOTE]
> Полный код для этого руководства можно найти на [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * Создание проекта веб-API
> * Аутентификация клиентов в серверной части веб-API.
> * Регистрация для получения уведомлений с помощью серверной части веб-API.
> * Отправка уведомлений из серверной части веб-API.
> * Публикация новой серверной части веб-API.
> * Обновите код для клиентского проекта
> * Тестирование приложения


## <a name="prerequisites"></a>предварительным требованиям
Это руководство основано на Центре уведомлений и проекте Visual Studio, которые были созданы в руководстве [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Выполните инструкции в том руководстве, прежде чем приступать к текущему. 

> [!NOTE]
> Если вы используете мобильные приложения в службе приложений Azure в качестве внутренней службы, то перейдите к [версии этого учебника для мобильных приложений](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).


&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Обновите код для клиентского проекта
В этом разделе вы обновите код проекта, созданного согласно руководству [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Проект уже должен быть связан с Магазином Windows. Он также должен использовать Центр уведомлений. В этом разделе вы добавляете код для вызова нового серверного веб-API и используете его для регистрации и отправки уведомлений.

1. В Visual Studio откройте решение, созданное согласно инструкциям руководства [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
2. В обозревателе решений щелкните правой кнопкой мыши проект **WindowsApp** и выберите **Управление пакетами NuGet**.
3. В левой части окна выберите **В сети**.
4. В текстовом поле **Поиск** введите **Клиент HTTP**.
5. В списке результатов щелкните **System.Net.Http** и выберите **Установить**. Выполните установку.
6. Вернитесь к полю NuGet **Поиск** и введите **Json.net**. Установите пакет **Newtonsoft.json**, а затем закройте окно диспетчера пакетов NuGet.
8. В обозревателе решений в проекте **WindowsApp** дважды щелкните **MainPage.xaml**, чтобы открыть его в редакторе Visual Studio.
9. В XML-коде **MainPage.xaml** замените раздел `<Grid>` следующим кодом. Этот код добавляет текстовые поля имени пользователя и пароля, которые используются для проверки подлинности. Он также добавляет текстовые поля для сообщения уведомления и тег имени пользователя, который должен получать уведомления:

    ```xml   
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```
11. В обозревателе решений откройте файл **MainPage.xaml.cs** для проектов **(Windows 8.1)** и **(Windows Phone 8.1)**. В верхнюю часть каждого из файлов добавьте такие `using` операторы:

    ```csharp    
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```
12. В файле **MainPage.xaml.cs** для проекта **WindowsApp** добавьте в класс `MainPage` следующий элемент. Обязательно замените `<Enter Your Backend Endpoint>` фактической конечной точкой серверной части, полученной ранее. Например, `http://mybackend.azurewebsites.net`.
    
    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```
13. В файле **MainPage.xaml.cs** для проектов **(Windows 8.1)** и **(Windows Phone 8.1)** добавьте в класс MainPage приведенный ниже код.
    
    Метод `PushClick` является обработчиком нажатия для кнопки **Отправить push-уведомление** . Он обеспечивает отправку серверной частью уведомлений на все устройства с тегом имени пользователя, который соответствует параметру `to_tag` . Сообщение уведомления отправляется в виде JSON-содержимого в теле запроса.
    
    Метод `LoginAndRegisterClick` является обработчиком нажатия для кнопки **Вход и регистрация**. Он сохраняет маркер проверки подлинности (относится к любому маркеру, используемому в данной схеме проверки подлинности) в локальном хранилище, а затем с помощью `RegisterClient` подписывается на получение уведомлений, используя серверную часть.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleGCM.IsChecked.Value)
        {
            await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS. 
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```
1. Откройте файл **App.xaml.cs**. В обработчике событий `OnLaunched()` найдите вызов `InitNotificationsAsync()`: Закомментируйте или удалите вызов `InitNotificationsAsync()`. Обработчик кнопки инициализирует регистрации уведомлений.

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```
1. Щелкните правой кнопкой мыши проект **WindowsApp**, а затем выберите пункты **Добавить** и **Класс**. Присвойте классу имя **RegisterClient.cs**, затем нажмите кнопку **ОК**, чтобы создать класс.
   
   Этот класс инкапсулирует вызовы REST, необходимые для связи с серверной частью приложения с целью регистрации в службе push-уведомлений. В данном случае также происходит локальное сохранение идентификаторов *registrationId* , созданных концентратором уведомлений в соответствии с описанием в разделе [Регистрация из серверной части приложения](http://msdn.microsoft.com/library/dn743807.aspx). В данном случае используется маркер проверки подлинности, сохраненный в локальном хранилище при нажатии кнопки **Вход и регистрация**.
2. Добавьте в начало файла RegisterClient.cs следующие операторы `using` :

    ```csharp   
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```
3. Добавьте в определение класса `RegisterClient` следующий код:
   
    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```
4. Сохраните изменения.

## <a name="test-the-application"></a>Тестирование приложения
1. Запустите приложение в обоих Windows.
2. Введите **Имя пользователя** и **Пароль**, как показано ниже. Они должны отличаться от имени пользователя и пароля, вводимых в Windows Phone.
3. Щелкните **Вход и регистрация** и убедитесь, что в диалоговом окне отображается подтверждение выполнения входа. Таким образом, будет активирована кнопка **Отправить push-уведомление**.
   
    ![][14]
5. Затем в поле **Тег имени получателя** введите зарегистрированное имя пользователя. Введите сообщение уведомления и нажмите кнопку **Отправить push-уведомление**.
6. Сообщения уведомления могут получать только устройства с зарегистрированным соответствующим тегом имени получателя.
   
    ![][15]

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы узнали, как отправлять push-уведомления определенным пользователям, с регистрацией которых связаны теги. Чтобы узнать, как отправлять push-уведомления на основе расположения, перейдите к следующему руководству: 

> [!div class="nextstepaction"]
>[Отправка push-уведомлений с определением геозон с помощью Центров уведомлений Azure и Bing Spatial Data](notification-hubs-push-bing-spartial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
