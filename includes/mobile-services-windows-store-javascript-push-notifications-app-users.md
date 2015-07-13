
Затем, чтобы удостовериться, что перед попыткой регистрации пользователь прошел проверку подлинности, необходимо изменить способ регистрации push-уведомлений. Обновление клиентского приложения зависит от способа реализации push-уведомлений.

###Использование мастера push-уведомлений в Visual Studio 2013 с обновлением 2 или более поздней версии

В этом методе мастер создал в вашем проекте новые файлы push.register.js и service.js.

1. В Visual Studio откройте в обозревателе решений файл проекта push.register.js и закомментируйте или удалите вызов метода **addEventListener**. 

2. В файле проекта default.js замените имеющуюся функцию **login** следующим кодом:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

###Push-уведомления, включаемые вручную		

В этом методе вы добавили код регистрации из учебника непосредственно в файл проекта default.js.

1. В обозревателе решений в Visual Studio откройте файл проекта default.js и в обработчике события **onActivated** найдите строку кода, вызывающую функцию **createPushNotificationChannelForApplicationAsync**. Она выглядит следующим образом:

		// Request a push notification channel.
		Windows.Networking.PushNotifications
		    .PushNotificationChannelManager
		    .createPushNotificationChannelForApplicationAsync()
		    .then(function (channel) {
		        // Register for notifications using the new channel
		        client.push.registerNative(channel.uri);
		    }); 
 
2. Переместите эту строку кода в функцию **login**, вставив ее перед вызовом **refreshTodoItems**. Функция **login** будет выглядеть так:
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

<!---HONumber=62-->