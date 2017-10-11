---
title: "Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET"
description: "Узнайте, как отправлять безопасные push-уведомления в Azure. Примеры кода написаны на C# с использованием API .NET."
documentationcenter: windows
author: ysxu
manager: erikre
services: notification-hubs
editor: 
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: c0b963ef661612b1a176dd8e5f01d56e61eb5acb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs-notify-users-with-net-backend"></a>Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Обзор
Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ. В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Для проверки подлинности клиентов используется серверная часть веб-API ASP.NET. С помощью пользователя, прошедшего проверку подлинности клиента, тег будет добавляться автоматически на серверной части для регистрации уведомлений. Этот тег будет использоваться для отправки с серверной части для создания уведомлений конкретному пользователю. Дополнительные сведения о регистрации уведомлений с помощью серверной части приложения см. в разделе руководства [Регистрация из внутренней службы приложения](http://msdn.microsoft.com/library/dn743807.aspx). В этом руководстве используется центр уведомлений и проект, созданные вами при работе с руководством [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows].

Этот учебник также необходимо изучить перед обращением к учебнику [Безопасные push-уведомления] . После выполнения действий, о которых рассказывается в данном учебнике, вы можете приступать к работе с учебником [Безопасные push-уведомления] , в котором рассказывается, как изменить код, указанный в этом учебнике, для безопасной отправки push-уведомлений.

## <a name="before-you-begin"></a>Перед началом работы
Мы очень ценим ваши отзывы. Если у вас возникли трудности с выполнением действий, описанных в этой статье, или у вас есть рекомендации по улучшению этого материала, пожалуйста, поделитесь с нами своими наблюдениями, воспользовавшись формой в нижней части страницы.

Полный код для этого учебника можно найти на портале [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

* [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows]<br/>С помощью этого учебника вы создадите центр уведомлений, зарезервируете имя приложения и зарегистрируетесь для получения уведомлений. В этом учебнике предполагается, что следующие действия уже были предприняты. В противном случае выполните действия, описанные в статье [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), в частности в разделах [Регистрация приложения для Магазина Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#register-your-app-for-the-windows-store) и [Настройка концентратора уведомлений](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Обязательно проверьте, что вы ввели на портале значения полей **SID пакета** и **Секрет клиента**, на вкладке **Настройка** для концентратора уведомлений. Эта процедура настройки описывается в разделе [Настройка концентратора уведомлений](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Этот шаг имеет большое значение: если учетные данные на портале не соответствуют учетным данным, указанным для выбранного имени приложения, push-уведомление не будет успешно выполнено.

> [!NOTE]
> Если вы используете мобильные приложения в службе приложений Azure в качестве внутренней службы, то перейдите к [версии этого учебника для мобильных приложений](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).
> 
> 

&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Обновите код для клиентского проекта
В этом разделе вы обновите код в проекте, который вы завершили при работе с учебником [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows] . Проект уже должен быть связан с хранилищем и настроен под концентратор уведомлений. В этом разделе вы добавите код для вызова нового внутреннего веб-API и используете его для регистрации и отправки уведомлений.

1. В Visual Studio откройте решение, созданное при работе с учебником [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows] .
2. В обозревателе решений щелкните правой кнопкой мыши проект **(Windows 8.1)** и выберите **Управление пакетами NuGet**.
3. В левой части окна выберите **В сети**.
4. В текстовом поле **Поиск** введите **Клиент HTTP**.
5. В списке результатов выберите **Клиентские библиотеки Microsoft HTTP** и нажмите **Установить**. Выполните установку.
6. Вернитесь к полю NuGet **Поиск** и введите **Json.net**. Установите пакет **Json.NET** и затем закройте окно диспетчера пакетов NuGet.
7. Повторите предыдущие шаги для проекта **(Windows Phone 8.1)**, чтобы установить пакет NuGet **JSON.NET** для проекта Windows Phone.
8. В обозревателе решений в проекте **(Windows 8.1)** дважды щелкните файл **MainPage.xaml**, чтобы открыть его в редакторе Visual Studio.
9. В XML-коде файла **MainPage.xaml** замените раздел `<Grid>` следующим кодом: Этот код добавляет текстовое поле имени пользователя и пароля, которые будут использоваться для проверки подлинности пользователя. Он также добавляет текстовые поля для сообщения уведомления и тег имени пользователя, который должен получать уведомления:
   
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
10. В обозревателе решений в проекте **(Windows Phone 8.1)** откройте файл **MainPage.xaml** и замените раздел `<Grid>` Windows Phone 8.1 приведенным выше кодом. Интерфейс должен выглядеть приблизительно так.
    
    ![][13]
11. В обозревателе решений откройте файл **MainPage.xaml.cs** для проектов **(Windows 8.1)** и **(Windows Phone 8.1)**. В верхнюю часть каждого из файлов добавьте такие `using` операторы:
    
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;
        using System.Threading.Tasks;
12. В файле **MainPage.xaml.cs** для проектов **(Windows 8.1)** и **(Windows Phone 8.1)** добавьте в класс `MainPage` следующий элемент. Обязательно замените `<Enter Your Backend Endpoint>` фактической конечной точкой серверной части, полученной ранее. Например, `http://mybackend.azurewebsites.net`.
    
        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
13. В файле **MainPage.xaml.cs** для проектов **(Windows 8.1)** и **(Windows Phone 8.1)** добавьте в класс MainPage приведенный ниже код.
    
    Метод `PushClick` является обработчиком нажатия для кнопки **Отправить push-уведомление** . Он обеспечивает отправку серверной частью уведомлений на все устройства с тегом имени пользователя, который соответствует параметру `to_tag` . Сообщение уведомления отправляется в виде JSON-содержимого в теле запроса.
    
    Метод `LoginAndRegisterClick` является обработчиком нажатия для кнопки **Вход и регистрация** . Он сохраняет маркер проверки подлинности в локальном хранилище (обратите внимание, что это относится к любому маркеру, используемому в данной схеме проверки подлинности), а затем с помощью `RegisterClient` подписывается на получение уведомлений, используя серверную часть.

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
                // The device handle used will be different depending on the device and PNS. 
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



1. В обозревателе решений в проекте **Shared** откройте файл **App.xaml.cs**. В обработчике событий `OnLaunched()` найдите вызов `InitNotificationsAsync()`: Закомментируйте или удалите вызов `InitNotificationsAsync()`. Обработчик кнопки, добавленный выше, инициализирует регистрацию уведомлений.

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


1. В обозревателе решений щелкните правой кнопкой мыши проект **Shared**, щелкните **Добавить**, а затем **Класс**. Присвойте классу имя **RegisterClient.cs**, затем нажмите кнопку **ОК**, чтобы создать класс.
   
   Этот класс будет реализовать вызовы REST, необходимые для связи с серверной частью приложения, с целью регистрации в службе push-уведомлений. В данном случае также происходит локальное сохранение идентификаторов *registrationId* , созданных концентратором уведомлений в соответствии с описанием в разделе [Регистрация из серверной части приложения](http://msdn.microsoft.com/library/dn743807.aspx). Обратите внимание, что в данном случае используется маркер проверки подлинности, сохраненный в локальном хранилище при нажатии кнопки **Вход и регистрация** .
2. Добавьте в начало файла RegisterClient.cs следующие операторы `using` :
   
       using Windows.Storage;
       using System.Net;
       using System.Net.Http;
       using System.Net.Http.Headers;
       using Newtonsoft.Json;
       using System.Threading.Tasks;
       using System.Linq;
3. Добавьте в определение класса `RegisterClient` следующий код:
   
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
   
           if (statusCode != HttpStatusCode.Accepted)
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
4. Сохраните изменения.

## <a name="testing-the-application"></a>Тестирование приложения
1. Запустите приложение в Windows 8.1 и Windows Phone 8.1. Для Windows Phone 8.1 запустить экземпляр можно в эмуляторе или в самом устройстве.
2. В экземпляре приложения Windows 8.1 введите **имя пользователя** и **пароль**, как показано ниже. Они должны отличаться от имени пользователя и пароля, вводимых в Windows Phone.
3. Щелкните **Вход и регистрация** и убедитесь, что в диалоговом окне отображается подтверждение выполнения входа. Таким образом будет активирована кнопка **Отправить push-уведомление** .
   
    ![][14]
4. В экземпляре Windows Phone 8.1 введите строку с именем пользователя в поле **Имя пользователя** и в поле **Пароль**, затем щелкните **Вход и регистрация**.
5. Затем в поле **Тег имени получателя** введите имя пользователя, зарегистрированное в Windows 8.1. Введите сообщение уведомления и нажмите кнопку **Отправить push-уведомление**.
   
    ![][16]
6. Сообщения уведомления могут получать только устройства с зарегистрированным соответствующим тегом имени получателя.
   
    ![][15]

## <a name="next-steps"></a>Дальнейшие действия
* Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей].
* Дополнительную информацию об использовании центров уведомлений можно найти в [руководстве по использованию центров уведомлений].

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Начало работы с Центрами уведомлений для приложений универсальной платформы Windows]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Безопасные push-уведомления]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[руководстве по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
