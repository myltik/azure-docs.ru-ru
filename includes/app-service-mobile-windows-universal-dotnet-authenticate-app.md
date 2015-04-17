
1. Откройте общий файл проекта MainPage.cs и добавьте следующий оператор using:

        using Windows.UI.Popups;

2. Добавьте в класс MainPage следующий фрагмент кода:
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

		// Define a method that performs the authentication process
		// using a Facebook sign-in. 
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
					// Change 'MobileService' to the name of your MobileServiceClient instance.
					// Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Этот пользователь прошел проверку подлинности с именем для входа в Facebook. Если используется поставщик удостоверений, отличный от Facebook, измените значение **MobileServiceAuthenticationProvider** выше на значение для вашего поставщика.

3. Закомментируйте или удалите вызов метода **RefreshTodoItems** в существующем переопределении метода **OnNavigatedTo**.

	Это позволяет предотвратить загрузку данных до того, как пользователь прошел проверку подлинности.

	>[AZURE.NOTE]Для успешной аутентификации из приложения Магазина Windows Phone 8.1 необходимо вызвать метод LoginAsync после вызова метода **OnNavigated** и создания события **Loaded** для страницы. В данном учебнике для этого в приложение добавлена кнопка **Вход**.

4. Добавьте в класс MainPage следующий фрагмент кода:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile app.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
5. В проекте приложения Магазина Windows откройте файл проекта MainPage.xaml и добавьте следующий элемент **Button** непосредственно перед элементом, определяющим кнопку **Сохранить**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Повторите предыдущий шаг для проекта приложения Магазина Windows Phone, но на этот раз добавьте элемент **Button** в **TitlePanel** после элемента **TextBlock**.

7. Откройте общий файл проекта App.xaml.cs и добавьте следующий оператор using (если его еще нет):

        using Microsoft.WindowsAzure.MobileServices;  
 
8. В файле проекта App.xaml.cs добавьте следующий код:

        protected override void OnActivated(IActivatedEventArgs args)
        {
			// Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
				// Completes the sign-in process started by LoginAsync.
				// Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

	Если метод **OnActivated** уже существует, просто добавьте блок кода #if...#endif.

9. Нажмите клавишу F5, чтобы запустить приложение Магазина Windows, нажмите кнопку **Вход** и войдите в приложение с помощью выбранного поставщика удостоверений. 

   	После входа мобильное приложение должно работать без ошибок, а у вас должна быть возможность отправлять в него запросы и обновлять данные.

10. Щелкните правой кнопкой мыши проект приложения Магазина Windows Phone, выберите пункт **Назначить запускаемым проектом**, а затем повторите предыдущий шаг, чтобы проверить, правильно ли запускается приложение Магазина Windows Phone.  

<!--HONumber=49-->