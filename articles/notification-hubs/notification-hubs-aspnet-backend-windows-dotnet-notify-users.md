<properties 
	pageTitle="Уведомление пользователей посредством центров уведомлений" 
	description="Узнайте, как отправлять безопасные push-уведомления в Azure. Примеры кода написаны на C# с использованием API .NET." 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	services="notification-hubs" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="2/16/2015" 
	ms.author="wesmc"/>

#Уведомление пользователей посредством центров уведомлений

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

##Обзор

Поддержка push-уведомлений в Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ. В этом учебнике показано, как использовать центры уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть веб-API ASP.NET используется для проверки подлинности клиентов и создания уведомлений, как показано в разделе руководства [Регистрация из серверной части приложения](http://msdn.microsoft.com/library/dn743807.aspx). Материал этого учебника основан на центре уведомлений, созданном вами при изучении учебника [Приступая к работе с центрами уведомлений].

Этот учебник также необходимо изучить перед обращением к учебнику [Безопасные push-уведомления]. После выполнения действий, о которых рассказывается в данном учебнике, можно приступать к работе с учебником [Безопасные push-уведомления], в котором рассказывается, как изменить код, указанный в этом учебнике, для безопасной отправки push-уведомлений. 


##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Приступая к работе с центрами уведомлений]<br/>Вы создаете концентратор уведомлений, резервируете имя приложения и регистрируетесь для получения уведомлений в рамках этого учебника.




> [AZURE.NOTE] Если вы используете мобильные службы в качестве серверной службы, см. раздел [Версия для мобильных служб](../mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) этого учебника.


## Создание и настройка центра уведомлений

Перед началом работы с этим учебников необходимо зарезервировать имя приложения, затем создать центр уведомлений Azure и подключить его к этому приложению. Выполните инструкции учебника [Начало работы с концентраторами уведомлений (Магазин Windows)](notification-hubs-windows-store-dotnet-get-started.md), в частности, в разделах [Регистрация приложения для Магазина Windows](notification-hubs-windows-store-dotnet-get-started.md#register) и [Настройка концентратора уведомлений](notification-hubs-windows-store-dotnet-get-started.md#configure-hub). В частности, убедитесь, что вы ввели значения полей **SID пакета** и **Секрет клиента** на портале, на вкладке **Настройка центра уведомлений**. Эта процедура настройки описывается в разделе [Настройка концентратора уведомлений](notification-hubs-windows-store-dotnet-get-started.md#configure-hub). Этот шаг имеет большое значение: если учетные данные на портале не соответствуют учетным данным, указанным для выбранного имени приложения, push-уведомление не будет успешно выполнено.

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Создание проекта для Windows Phone

Следующий шаг заключается в создании приложения Windows Phone. Чтобы добавить этот проект в текущее решение, выполните следующие действия:

1. В обозревателе решений щелкните правой кнопкой узел верхнего уровня решения (в данном случае (в данном случае - **Solution NotifyUsers**), затем нажмите кнопку **Добавить** и щелкните **Создать проект**.

2. Разверните пункт **Приложения Магазина**, затем щелкните **Приложения Windows Phone** и выберите **Пустое приложение (Windows Phone)**.

	![][9]

3. В поле **Имя** введите **NotifyUserWindowsPhone**, затем нажмите **ОК**, чтобы сформировать проект.

 
4. Свяжите это приложение с Магазином Windows Phone. Для этого в обозревателе решений правой кнопкой мыши щелкните **NotifyUserWindowsPhone (Windows Phone 8.1)**, затем щелкните **Магазин** и выберите **Связать приложение с Магазином...**.

	![][10]
 
5. Выполните шаги мастера, чтобы войти и связать приложение с Магазином.

	![][11]
	
	> [AZURE.NOTE] Обязательно запишите имя приложения, выбранное при выполнении этой процедуры. Необходимо настроить концентратор уведомлений на портале с использованием учетных данных, полученных от [Центра разработки Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409), для данного зарезервированного имени приложения. Эта процедура настройки описывается в разделе [Настройка концентратора уведомлений](notification-hubs-windows-store-dotnet-get-started.md#configure-hub). Этот шаг имеет большое значение: если учетные данные на портале не соответствуют учетным данным, указанным для выбранного имени приложения, push-уведомление не будет успешно выполнено.

6. В обозревателе решений щелкните правой кнопкой мыши проект **NotifyUserWindowsPhone (Windows Phone 8.1)** и затем щелкните **Управление пакетами NuGet**.

7. В левой части окна щелкните **В сети**.

8. В текстовом поле **Поиск** введите **Клиент HTTP**.

9. В списке результатов выберите **Клиентские библиотеки Microsoft HTTP** и нажмите кнопку **Установить**. Выполните установку.

10. Вернитесь к полю NuGet **Поиск** и введите **Json.net**. Установите пакет **Json.NET** и закройте окно диспетчера пакетов NuGet.

11. В обозревателе решений в проекте **NotifyUserWindowsPhone (Windows Phone 8.1)** дважды щелкните файл **MainPage.xaml**, чтобы открыть его в редакторе Visual Studio.

12. В XML-коде файла **MainPage.xaml** замените раздел `<Grid>` следующим кодом:

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


13. В обозревателе решений щелкните правой кнопкой мыши проект **NotifyUserWindowsPhone (Windows Phone 8.1)** и затем щелкните **Добавить** и выберите **Класс**. Присвойте классу имя **RegisterClient.cs**, затем нажмите кнопку **ОК**, чтобы создать класс. Этот компонент реализует вызовы REST, необходимые для связи с серверной частью приложения с целью регистрации в службе push-уведомлений. В данном случае также происходит локальное сохранение идентификаторов *registrationIds*, созданных концентратором уведомлений в соответствии с описанием в разделе [Регистрация из серверной части приложения](http://msdn.microsoft.com/library/dn743807.aspx). Обратите внимание, что в данном случае используется маркер аутентификации, сохраненный в локальном хранилище при нажатии кнопки **Вход и регистрация**.

14. Добавьте следующий код в определение класса `RegisterClient`. Обязательно замените код `{backend endpoint}` конечной точкой серверной части, полученной в предыдущем разделе:

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


15. Добавьте в начало файла RegisterClient.cs следующие операторы `using`:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		
16. Добавьте код кнопок в файле MainPage.xaml.cs. При обратном вызове действия **Вход и регистрация** происходит сохранение маркера аутентификации в локальном хранилище (обратите внимание, что это относится к любому маркеру, используемому в данной схеме аутентификации), а затем с помощью кода `RegisterClient` вызывается серверная часть. При обратном вызове **AppBackend** происходит вызов серверной части с целью инициировать безопасные уведомления на все устройства пользователя. 

	Добавьте следующий код в файле MainPage.xaml.cs после метода `OnNavigatedTo()`. Обязательно замените код `{backend endpoint}` конечной точкой серверной части, полученной в предыдущем разделе:

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

## Запуск приложения

Для запуска приложения выполните следующие действия:

1. В Visual Studio запустите приложение **NotifyUserWindowsPhone (Windows Phone 8.1)** для Windows Phone. Эмулятор Windows Phone запустится и автоматически загрузит приложение.

2. В пользовательском интерфейсе приложения **NotifyUserWindowsPhone** введите имя пользователя и пароль. Это может быть любая строка, но имя и пароль должны быть одинаковыми.

3. В пользовательском интерфейсе приложения **NotifyUserWindowsPhone** щелкните **Вход и регистрация**. Затем нажмите кнопку **Отправить push-уведомление**.


[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png



<!-- URLs. -->
[Приступая к работе с центрами уведомлений]: notification-hubs-windows-store-dotnet-get-started.md
[Безопасные push-уведомления]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md

<!--HONumber=49--> 