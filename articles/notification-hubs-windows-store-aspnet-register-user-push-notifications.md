<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Регистрация текущего пользователя для push-уведомлений с помощью ASP.NET

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS">iOS</a>
</div>

В этом разделе показано, как запросить регистрацию push-уведомления с помощью концентраторов уведомлений Azure, когда регистрация выполняется с помощью веб-интерфейса API веб-приложения ASP.NET. Этот раздел расширяет учебник [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений]. Чтобы создать прошедшую проверку подлинности мобильную службу, вы должны завершить требуемые действия в этом учебнике. Дополнительные сведения о сценарии уведомления пользователей см. в учебнике [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений].

1.  В Visual Studio 2012 откройте файл app.xaml.cs в проекте, который создали при прохождении предыдущего необходимого учебника [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений].

2.  Найдите метод **InitNotificationsAsync** и раскомментируйте код в методе.

    Вместо этого вы используете веб-интерфейс API ASP.NET для регистрации уведомлений.

3.  В **обозревателе решений** щелкните правой кнопкой мыши по имени проекта и выберите **Управление пакетами NuGet**.

4.  В левой области выберите категорию **Интернет**, выполните поиск `json.net`, щелкните пункт **Установить** в пакете **Json.NET**, а затем примите условия лицензионного соглашения.

    ![][0]

    При этом в проект добавляется сторонняя сборка Newtonsoft.Json.dll.

5.  В обозревателе решений щелкните правой кнопкой мыши по проекту, выберите **Добавить**, **Класс**, введите `LocalStorageManager` и нажмите кнопку **Добавить**.

    ![][1]

    При этом в проект добавляется файл кода для класса **LocalStorageManager**.

6.  Откройте новый файл проекта LocalStorageManager.cs и добавьте следующий оператор **using**:

        using Windows.Storage;

7.  Замените определение класса **LocalStorageManager** следующим кодом:

        class LocalStorageManager
        {
            private static ApplicationDataContainer GetLocalStorageContainer()
            {
                if (!ApplicationData.Current.LocalSettings
                    .Containers.ContainsKey("InstallationContainer"))
                {
                    ApplicationData.Current.LocalSettings
                        .CreateContainer("InstallationContainer",                                                            
                        ApplicationDataCreateDisposition.Always);
                }
                return ApplicationData.Current.LocalSettings
                    .Containers["InstallationContainer"];
            }

            public static string GetInstallationId()
            {
                var container = GetLocalStorageContainer();
                if (!container.Values.ContainsKey("InstallationId"))
                {
                    container.Values["InstallationId"] = Guid.NewGuid().ToString();
                }
                return (string)container.Values["InstallationId"];
            }
        }

    Этот код создает и сохраняет ИД установки конкретного устройства, который используется как тег при создании уведомлений. Если существует ИД установки, он будет использоваться вместо этого.

8.  Откройте файл проекта MainPage.xaml и замените корневой элемент **Grid** следующим кодом XAML:

        <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <Grid Margin="120, 58, 120, 80" >
                <Grid.RowDefinitions>
                    <RowDefinition />
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MaxWidth="200"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                               TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                               FontSize="42"  VerticalAlignment="Top"/>
                <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                               FontSize="24" VerticalAlignment="Center" />
                <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                             Width="300" Height="40" HorizontalAlignment="Left"/>
                <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                               VerticalAlignment="Center" />
                <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                 Width="300" Height="40" HorizontalAlignment="Left"/>
                <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                            Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
            </Grid>
        </Grid>

    Этот код создает пользовательский интерфейс для ввода имени пользователя и пароля и отображения ИД установки.

9.  В файле MainPage.xaml.cs добавьте следующие операторы **using**:

        using Newtonsoft.Json;
        using System.Net.Http;
        using System.Text;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

10. Добавьте следующее определение в класс **MainPage**:

        private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    Замените *`<SERVICE_ROOT_URL>`* корневым URI веб-API, который был создан на шаге **Уведомление пользователей с помощью концентраторов уведомлений**

11. Добавьте в класс **MainPage** следующий метод:

        private async void Login_Click(object sender, RoutedEventArgs e)
        {
            // Get the info that we need to request registration.
            var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
            var user = User.Text;
            var pwd = Password.Password;

            // Define a registration to pass in the body of the POST request.
            var registration = new Dictionary<string, string>()
                                   {{"platform", "windows"},
                                   {"instId", installationId.ToString()},
                                   {"channelUri", channel.Uri}};

            // Create a client to send the HTTP registration request.
            var client = new HttpClient();
            var request =
                new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));

            // Add the Authorization header with the basic login credentials.
            var auth = "Basic " +
                Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
            request.Headers.Add("Authorization", auth);
            request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                Encoding.UTF8, "application/json");

            string message;

            try
            {
                // Send the registration request.
                HttpResponseMessage response = await client.SendAsync(request);

                // Get and display the response, either the registration ID
                // or an error message.
                message = await response.Content.ReadAsStringAsync();
                message = string.Format("Registration ID: {0}", message);
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Этот метод возвращает ИД установки и канал для push-уведомлений и отправляет его, вместе с типом устройства, прошедшему проверку подлинности методу веб-API, который создает регистрацию в концентраторах уведомлений. Этот веб-API был определен в учебнике [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений].

Теперь, когда клиентское приложение было обновлено, вернитесь к учебнику [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений] и обновите мобильную службу для отправки уведомлений с помощью концентраторов уведомлений.



  [Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
  [0]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png
  [1]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png
