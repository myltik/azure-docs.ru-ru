<properties 
	pageTitle="Отправка push-уведомлений определенному пользователю с клиентом Xamarin iOS" 
	description="Узнайте, как отправлять push-уведомления на все устройства пользователя" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/17/2015"
	ms.author="yuaxu"/>

# Отправка кроссплатформенных уведомлений определенному пользователю

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../../includes/app-service-mobile-selector-push-users-preview.md)]

В этом разделе показано, как отправлять уведомления на все зарегистрированные устройства определенного пользователя с мобильного внутреннего сервера. Здесь вводится концепция [шаблонов], которая позволяет клиентским приложениям указывать форматы полезных данных и заполнители переменных при регистрации. Таким образом, уведомления отправляются на каждую платформу с такими заполнителями.

> [AZURE.NOTE]Чтобы push-уведомления работали с кроссплатформенными клиентами, потребуется пройти этот учебник для каждой задействованной платформы. Для клиентов с общим мобильным внутренним сервером потребуется только одно [обновление мобильного внутреннего сервера](#backend).
 
##Предварительные требования 

Перед тем как начать этот учебник, необходимо пройти учебники по службе приложений для каждой клиентской платформы, на которой вы хотите работать:

+ [Приступая к работе с аутентификацией в мобильных службах]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Приступая к работе с push-уведомлениями]<br/>Настраивает push-уведомления в примере приложения TodoList.

##<a name="client"></a>Обновление клиента, чтобы зарегистрироваться для получения шаблонов для обработки кроссплатформенных push-уведомлений

1. Переместите фрагменты регистрации APNs из **FinishedLaunching** в **AppDelegate.cs** в определение задачи **RefreshAsync** в **QSTodoListViewController.cs**. Регистрацию следует проводить после выполнения проверки подлинности.

        ...
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound, 
                new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        ...

2. В **AppDelegate.cs** замените вызов **RegisterAsync** в **RegisteredForRemoteNotifications** на следующий для работы с шаблонами:

        // delete await push.RegisterAsync (deviceToken);
        
        var notificationTemplate = "{"aps": {"alert":"$(message)"}}";

        JObject templateBody = new JObject();
        templateBody["body"] = notificationTemplate;

        JObject templates = new JObject();
        templates["testApnsTemplate"] = templateBody;

        // register with templates
        await push.RegisterAsync (deviceToken, templates);

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

            // get the current user id and create given user tag
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

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
[Приступая к работе с аутентификацией в мобильных службах]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Приступая к работе с push-уведомлениями]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[шаблонов]: https://msdn.microsoft.com/ru-ru/library/dn530748.aspx
<!--HONumber=54--> 