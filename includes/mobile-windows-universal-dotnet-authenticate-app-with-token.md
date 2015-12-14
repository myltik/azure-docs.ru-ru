
1. В файле проекта MainPage.xaml.cs добавьте следующие операторы **using**:

		using System.Linq;		
		using Windows.Security.Credentials;

2. Замените метод **AuthenticateAsync** следующим кодом:

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider,
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            message = string.Format("You are now logged in - {0}", user.UserId);
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

	В этой версии **AuthenticateAsync** приложение пытается использовать для доступа к службе учетные данные, хранимые в **PasswordVault**. Обычная попытка входа предпринимается и при отсутствии хранимых учетных данных.

	>[AZURE.NOTE]Срок действия кэшированного маркера может истечь до или после проверки подлинности, в процессе использования приложения. Чтобы узнать, как определить, истек ли срок действия маркера, см. статью [Проверка на наличие маркеров проверки подлинности с истекшим сроком действия](http://aka.ms/jww5vp). Решение по обработке ошибок авторизации, связанных с просроченными токенами, см. в записи [Кэширование и обработка просроченных токенов в SDK под управлением мобильных служб Azure](hive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx).

3. Дважды перезапустите приложение.

	Обратите внимание, что при первом перезапуске по-прежнему потребуется вход с использованием поставщика. При втором перезапуске будут использоваться кэшированные учетные данные и вход будет пропущен.

<!---HONumber=AcomDC_1203_2015-->