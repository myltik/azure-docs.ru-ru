<properties 
	pageTitle="Отправка уведомлений x-plat определенному пользователю с клиентом Магазина Windows"
	description="Отправка push-уведомлений на все устройства определенного пользователя."
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/14/2015"
	ms.author="yuaxu"/>

# Отправка кроссплатформенных уведомлений определенному пользователю

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

В этом разделе показано, как отправлять уведомления на все зарегистрированные устройства определенного пользователя с мобильного внутреннего сервера. Здесь вводится концепция [шаблонов], которая позволяет клиентским приложениям указывать форматы полезных данных и заполнители переменных при регистрации. Таким образом, уведомления отправляются на каждую платформу с такими заполнителями.

> [AZURE.NOTE]Чтобы push-уведомления работали с кроссплатформенными клиентами, потребуется пройти этот учебник для каждой задействованной платформы. Для клиентов с общим мобильным внутренним сервером потребуется только одно [обновление мобильного внутреннего сервера](#backend).
 
##Предварительные требования 

Перед тем как начать этот учебник, необходимо пройти учебники по службе приложений для каждой клиентской платформы, на которой вы хотите работать:

+ [Приступая к работе с аутентификацией в мобильных службах]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Приступая к работе с push-уведомлениями]<br/>Настраивает push-уведомления в примере приложения TodoList.

##<a name="client"></a>Обновление клиента, чтобы зарегистрироваться для получения шаблонов для обработки кроссплатформенных push-уведомлений

1. Вместо этого будет выполнен **InitNotificationAsync** в **MainPage.cs** для работы с проверкой подлинности пользователя. Удалите определение метода **InitNotificationAsync** и вызов в **App.xmal.cs**, и добавьте следующее в **MainPage.cs** в классе **MainPage**:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
 
            // building templates for wns
            var toastTemplate = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";
            JObject templateBody = new JObject();
            templateBody["body"] = toastTemplate;
 
            JObject wnsToastHeaders = new JObject();
            wnsToastHeaders["X-WNS-Type"] = "wns/toast";
            templateBody["headers"] = wnsToastHeaders;
 
            JObject templates = new JObject();
            templates["testTemplate"] = templateBody;
 
            await App.MobileService.GetPush().RegisterAsync(channel.Uri, templates);
        }

    Необходимо также передать некоторые операторы using в **MainPage.cs**.

2. Используйте этот метод сразу после вызова **AuthenticateAsync** в **ButtonLogin\_Click**.

        await AuthenticateAsync();
        InitNotificationAsync();

Приложение теперь настроено для регистрации сведений об имени пользователя в пользовательском устройстве.

##<a name="backend"></a>Обновление внутреннего сервера службы для отправки уведомлений определенному пользователю

1. В Visual Studio измените определение метода `PostTodoItem` с помощью следующего кода:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create tag to identify user
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // build dictionary for template
            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
            	await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>Тестирование приложения

Повторно опубликуйте проект мобильного внутреннего сервера и запустите все настроенные клиентские приложения. При вставке элемента внутренний сервер отправит уведомления всем клиентским приложениям, в которые пользователь выполнил вход.

<!-- URLs. -->
[Приступая к работе с аутентификацией в мобильных службах]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Приступая к работе с push-уведомлениями]: app-service-mobile-windows-store-dotnet-get-started-push.md
[шаблонов]: https://msdn.microsoft.com/ru-RU/library/dn530748.aspx
 

<!---HONumber=Nov15_HO1-->