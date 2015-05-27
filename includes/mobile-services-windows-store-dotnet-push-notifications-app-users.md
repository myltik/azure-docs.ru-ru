
Затем, чтобы удостовериться, что перед попыткой регистрации пользователь прошел проверку подлинности, необходимо изменить способ регистрации push-уведомлений. Обновление клиентского приложения зависит от способа реализации push-уведомлений.

###Использование мастера добавления push-уведомлений в Visual Studio 2013 с обновлением 2 или более поздней версии

В этом методе мастер создал в вашем проекте новый файл push.register.cs.

1. В Visual Studio откройте в обозревателе решений файл проекта app.xaml.cs и в обработчике события **OnLaunched** закомментируйте или удалите вызов метода **UploadChannel**. 

2. Откройте файл проекта push.register.cs и замените метод **UploadChannel** на следующий код:

		public async static void UploadChannel()
		{
		    var channel = 
		        await Windows.Networking.PushNotifications.PushNotificationChannelManager
		        .CreatePushNotificationChannelForApplicationAsync();
		
		    try
		    {
		        // Create a native push notification registration.
		        await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);		        
		
		    }
		    catch (Exception exception)
		    {
		        HandleRegisterException(exception);
		    }
		}

	Это обеспечит выполнение регистрации только для того экземпляра клиента, который имеет учетные данные пользователя, прошедшего проверку подлинности. В противном случае регистрация не удастся и будет возвращена ошибка "Не санкционировано" (401).

3. Откройте файл проекта MainPage.xaml.cs и замените переопределение метода **OnNavigatedTo** следующим:

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            todolistPush.UploadChannel();
            RefreshTodoItems();
        }

	В этом коде следует заменить автоматически созданное имя класса push (`todolistPush`) именем класса, созданным мастером, обычно в формате <code><em>mobile_service</em>Push</code>.

###Push-уведомления, включаемые вручную		

В этом методе вы добавили код регистрации из учебника непосредственно в файл проекта app.xaml.cs.

1. В Visual Studio откройте в обозревателе решений файл проекта app.xaml.cs и в обработчике события **OnLaunched** закомментируйте или удалите вызов метода **InitNotificationsAsync**. 
 
2. Измените диапазон доступа метода **InitNotificationsAsync** с `private` значением `public` и добавьте модификатор `static`.

3. Откройте файл проекта MainPage.xaml.cs и замените переопределение метода **OnNavigatedTo** следующим:

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.InitNotificationsAsync();
            RefreshTodoItems();
        }
<!--HONumber=54-->