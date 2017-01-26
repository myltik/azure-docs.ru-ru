
1. Откройте файл общего проекта MainPage.xaml.cs и добавьте следующий фрагмент кода в класс MainPage:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Этот код выполняет проверку подлинности пользователя с помощью имени входа в Facebook. Если используется поставщик удостоверений, отличный от Facebook, измените значение **MobileServiceAuthenticationProvider** выше на значение для вашего поставщика.
2. Закомментируйте или удалите вызов метода **RefreshTodoItems** в существующем переопределении метода **OnNavigatedTo**.
   
    Это позволяет предотвратить загрузку данных до того, как пользователь прошел проверку подлинности. Затем добавьте кнопку **Вход** в приложение, которое запускает проверку подлинности.
3. Добавьте в класс MainPage следующий фрагмент кода:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. В проекте приложения Магазина Windows откройте файл проекта MainPage.xaml и добавьте следующий элемент **Button** непосредственно перед элементом, определяющим кнопку **Сохранить**:
   
        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
5. В проекте приложения для Магазина Windows Phone после элемента **TextBox** на панели **ContentPanel** добавьте указанный ниже элемент **Button**:
   
        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>
6. Откройте общий файл проекта App.xaml.cs и добавьте следующий код:
   
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
   
    Если метод **OnActivated** уже существует, просто добавьте блок кода `#if...#endif`.
7. Нажмите клавишу F5, чтобы запустить приложение Магазина Windows, нажмите кнопку **Вход** и войдите в приложение с помощью выбранного поставщика удостоверений. 
   
       When you are successfully logged-in, the app should run without errors, and you should be able to query your backend and make updates to data.
8. Щелкните правой кнопкой мыши проект приложения Магазина Windows Phone, выберите пункт **Назначить запускаемым проектом**, а затем повторите предыдущий шаг, чтобы проверить, правильно ли запускается приложение Магазина Windows Phone.  



<!--HONumber=Jan17_HO3-->


