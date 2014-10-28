<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Уведомление пользователей посредством концентраторов уведомлений

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ru-ru/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
        <a href="/ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ. В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть ASP.NET WebAPI используется для проверки подлинности клиентов и создания уведомлений, как показано в разделе руководства [Регистрация из серверной части приложения][Регистрация из серверной части приложения]. Материал этого учебника основан на концентраторе уведомлений, созданном вами в учебнике **Приступая к работе с концентраторами уведомлений**.

Этот учебник также необходимо изучить перед обращением к учебнику **Безопасные push-уведомления**. После выполнения шагов учебника **Уведомление пользователей** можно приступать к работе с учебником **Безопасные push-уведомления**, в котором показано, как следует изменять код **Уведомления пользователей** для безопасной отправки push-уведомлений.

> [AZURE.NOTE] В этом учебнике подразумевается, что вы создали и настроили концентратор уведомлений в соответствии с описанием в учебнике [Приступая к работе с концентраторами уведомлений (Магазин Windows)][Приступая к работе с концентраторами уведомлений (Магазин Windows)].
> Также обратите внимание, что в этом учебнике вы будете создавать приложение Windows Phone 8.1. Такой же код можно использовать для приложений Магазина Windows и Windows Universal. Во всех таких приложениях необходимо использовать учетные данные Windows (а не Windows Phone).

## Создание и настройка концентратора уведомлений

Перед началом работы с этим учебников необходимо зарезервировать имя приложения, затем создать концентратор уведомлений Azure и подключить его к этому приложению. Выполните шаги в учебнике [Приступая к работе с концентраторами уведомлений (Магазин Windows)][Приступая к работе с концентраторами уведомлений (Магазин Windows)], в частности, в разделах [Регистрация приложения для Магазина Windows][Регистрация приложения для Магазина Windows] и [Настройка концентратора уведомлений][Настройка концентратора уведомлений]. В частности, убедитесь, что вы ввели значения полей **SID пакета** и **Секрет клиента** на портале, на вкладке **Настройка** концентратора уведомлений. Эта процедура настройки описывается в разделе [Настройка концентратора уведомлений][Настройка концентратора уведомлений]. Этот шаг имеет большое значение: если учетные данные на портале не соответствуют учетным данным, указанным для выбранного имени приложения, push-уведомление не будет успешно выполнено.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][notification-hubs-aspnet-backend-notifyusers]]

## Создание проекта для Windows Phone

Следующий шаг заключается в создании приложения Windows Phone. Чтобы добавить этот проект в текущее решение, выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой узел верхнего уровня решения (в данном случае - **Solution NotifyUsers**), затем нажмите кнопку **Добавить**, после чего щелкните **Создать проект**.

2.  Разверните пункт **Приложения Магазина**, затем щелкните **Приложения Windows Phone** и выберите **Пустое приложение (Windows Phone)**.

    ![][]

3.  В поле **Имя** введите **NotifyUserWindowsPhone**, затем нажмите **ОК**, чтобы сформировать проект.

4.  Свяжите это приложение с Магазином Windows Phone. Для этого в обозревателе решений щелкните правой кнопкой мыши **NotifyUserWindowsPhone (Windows Phone 8.1)**, затем щелкните **Магазин**, после чего выберите **Связать приложение с Магазином...**.

    ![][1]

5.  Выполните шаги мастера, чтобы войти и связать приложение с Магазином.

    ![][2]

    > [AZURE.NOTE] Обязательно запишите имя приложения, выбранное при выполнении этой процедуры. Необходимо настроить концентратор уведомлений на портале с использованием учетных данных, полученных от [Центра разработки Windows][Центра разработки Windows], для данного зарезервированного имени приложения. Эта процедура настройки описывается в разделе [Настройка концентратора уведомлений][Настройка концентратора уведомлений]. Этот шаг имеет большое значение: если учетные данные на портале не соответствуют учетным данным, указанным для выбранного имени приложения, push-уведомление не будет успешно выполнено.

6.  В обозревателе решений щелкните правой кнопкой мыши проект **NotifyUserWindowsPhone (Windows Phone 8.1)** и затем щелкните **Управление пакетами NuGet**.

7.  В левой части окна выберите **В сети**.

8.  В текстовом поле **Поиск** введите **Клиент HTTP**.

9.  В списке результатов выберите **Клиентские библиотеки Microsoft HTTP**, после чего выберите **Установить**. Выполните установку.

10. Вернитесь к полю NuGet **Поиск**, введите **Json.net**. Установите пакет **Json.NET** и затем закройте окно диспетчера пакетов NuGet.

11. В обозревателе решений в проекте **NotifyUserWindowsPhone (Windows Phone 8.1)** дважды щелкните файл **MainPage.xaml**, чтобы открыть его в редакторе Visual Studio.

12. В XML-коде **MainPage.xaml** замените раздел `<Grid>` следующим кодом:

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>

                    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

                    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
                </Grid>
            </StackPanel>
        </Grid>

13. В обозревателе решений щелкните правой кнопкой мыши проект **NotifyUserWindowsPhone (Windows Phone 8.1)** и затем щелкните **Добавить** и выберите **Класс**. Присвойте классу имя **RegisterClient.cs**, затем нажмите кнопку **ОК**, чтобы создать класс. Этот компонент реализует вызовы REST, необходимые для связи с серверной частью приложения с целью регистрации в службе push-уведомлений. В данном случае также происходит локальное сохранение идентификаторов *registrationId*, созданных концентратором уведомлений в соответствии с описанием в разделе [Регистрация из серверной части приложения][Регистрация из серверной части приложения]. Обратите внимание, что в данном случае используется маркер проверки подлинности, сохраненный в локальном хранилище при нажатии кнопки **Вход и регистрация**.

14. Добавьте в определение класса `RegisterClient` следующий код: Обязательно замените код `{backend endpoint}` конечной точкой серверной части, полученной в предыдущем разделе:

        private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
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

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
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
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

15. Добавьте в начало файла RegisterClient.cs следующие инструкции `using`:

        using Windows.Storage;
        using System.Net;
        using System.Net.Http;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;

16. Добавьте код кнопок в файле MainPage.xaml.cs. При обратном вызове действия **Вход и регистрация** происходит сохранение маркера проверки подлинности в локальном хранилище (обратите внимание, что это относится к любому маркеру, используемому в данной схеме проверки подлинности), затем с помощью кода `RegisterClient` вызывается серверная часть. При обратном вызове **AppBackend** происходит вызов серверной части с целью инициировать безопасные уведомления на все устройства пользователя.

    Добавьте следующий код в файле MainPage.xaml.cs после метода `OnNavigatedTo()`: Обязательно замените код `{backend endpoint}` конечной точкой серверной части, полученной в предыдущем разделе:

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }

17. Добавьте в начало файла MainPage.xaml.cs следующие инструкции `using`:

        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

## Запустите приложение

Для запуска приложения выполните следующие действия:

1.  В Visual Studio запустите приложение веб-API **AppBackend**. Отобразится веб-страница ASP.NET.

2.  В Visual Studio запустите приложение **NotifyUserWindowsPhone (Windows Phone 8.1)** для Windows Phone. Эмулятор Windows Phone запустится и автоматически загрузит приложение.

3.  В пользовательском интерфейсе приложения **NotifyUserWindowsPhone** введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми.

4.  В пользовательском интерфейсе приложения **NotifyUserWindowsPhone** щелкните **Вход и регистрация**. Затем нажмите кнопку **Отправить push-уведомление**.

  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [Регистрация из серверной части приложения]: http://msdn.microsoft.com/ru-ru/library/dn743807.aspx
  [Приступая к работе с концентраторами уведомлений (Магазин Windows)]: http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [Регистрация приложения для Магазина Windows]: http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register
  [Настройка концентратора уведомлений]: http://azure.microsoft.com/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
  [1]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
  [2]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
  [Центра разработки Windows]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
