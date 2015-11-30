<properties 
	pageTitle="Отправка кроссплатформенных уведомлений определенному пользователю в iOS" 
	description="Отправка push-уведомлений на все устройства определенного пользователя."
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-dotnet" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="11/17/2015"
	ms.author="yuaxu"/>

# Отправка кроссплатформенных уведомлений определенному пользователю

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

В этом разделе показано, как отправлять уведомления на все зарегистрированные устройства определенного пользователя с мобильного внутреннего сервера. Здесь вводится концепция [шаблонов], которая позволяет клиентским приложениям указывать форматы полезных данных и заполнители переменных при регистрации. Таким образом, уведомления отправляются на каждую платформу с такими заполнителями.

> [AZURE.NOTE]Чтобы push-уведомления работали с кроссплатформенными клиентами, потребуется пройти этот учебник для каждой задействованной платформы. Для клиентов с общим мобильным внутренним сервером потребуется только одно [обновление мобильного внутреннего сервера](#backend).
 
##Предварительные требования 

Перед тем как начать этот учебник, необходимо пройти учебники по службе приложений для каждой клиентской платформы, на которой вы хотите работать:

+ [Приступая к работе с аутентификацией в мобильных службах]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Приступая к работе с push-уведомлениями]<br/>Настраивает push-уведомления в примере приложения TodoList.

##<a name="client"></a>Обновление клиента, чтобы зарегистрироваться для получения шаблонов для обработки кроссплатформенных push-уведомлений

1. Переместите фрагменты регистрации APNs в **QSAppDelegate.m** **application:didFinishLaunchingWithOptions** в вызов **loginWithProvider** в **QSTodoListViewController.m**, чтобы после проверки подлинности произошло следующее.

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. Замените вызов **registerDeviceToken** в **application:didRegisterForRemoteNotificationsWithDeviceToken** на следующее для работы с шаблонами:

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

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
[Приступая к работе с аутентификацией в мобильных службах]: app-service-mobile-ios-get-started-users.md
[Приступая к работе с push-уведомлениями]: app-service-mobile-ios-get-started-push.md
[шаблонов]: https://msdn.microsoft.com/ru-RU/library/dn530748.aspx
 

<!---HONumber=Nov15_HO4-->