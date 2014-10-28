В предыдущем примере был показан стандартный вход, который требует, чтобы клиент подключался как к поставщику удостоверений, так и к мобильной службе каждый раз, когда приложение запускается. Мало того что этот метод неэффективен, вы можете столкнуться с проблемами, связанными с использованием приложения, если большое количество клиентов попытаются запустить приложение одновременно. Несколько лучшим подходом является проверка подлинности удостоверений с кэшированием маркера, возвращенного мобильной службой, которая будет использоваться до входа на основе поставщика.

> Можно кэшировать маркер, выданный мобильной службой, независимо от того, используете ли вы процесс входа, управляемый клиентом или сервером. Этот учебник использует управляемую сервером проверку подлинности.

1.  В файле проекта MainPage.xaml.cs добавьте следующие операторы **using**:

        using System.IO.IsolatedStorage;
        using System.Security.Cryptography;     

2.  Замените метод **AuthenticateAsync** следующим кодом:

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Provide some additional app-specific security for the encryption.
            byte [] entropy = { 1, 8, 3, 6, 5 };

            // Authorization credential.
            MobileServiceUser user = null;

            // Isolated storage for the app.
            IsolatedStorageSettings settings =
                IsolatedStorageSettings.ApplicationSettings;

            while (user == null)
            {
                // Try to get an existing encrypted credential from isolated storage.                    
                if (settings.Contains(provider))
                {
                    // Get the encrypted byte array, decrypt and deserialize the user.
                    var encryptedUser = settings[provider] as byte[];
                    var userBytes = ProtectedData.Unprotect(encryptedUser, entropy);
                    user = JsonConvert.DeserializeObject<MobileServiceUser>(
                        System.Text.Encoding.Unicode.GetString(userBytes, 0, userBytes.Length));
                }
                if (user != null)
                {
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            settings.Remove(provider);
                            user = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);

                        // Serialize the user into an array of bytes and encrypt with DPAPI.
                        var userBytes = System.Text.Encoding.Unicode
                            .GetBytes(JsonConvert.SerializeObject(user));
                        byte[] encryptedUser = ProtectedData.Protect(userBytes, entropy);

                        // Store the encrypted user credentials in local settings.
                        settings.Add(provider, encryptedUser);
                        settings.Save();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                MessageBox.Show(message);
            }
        }

    В этой версии **AuthenticateAsync** приложение пытается использовать для доступа к мобильной службе учетные данные, хранимые в локальном хранилище. Для того чтобы убедиться, что хранимый маркер не просрочен, посылается простой запрос. При возвращении ответа 401 предпринимается обычная попытка входа с использованием поставщика. Обычная попытка входа предпринимается и при отсутствии хранимых учетных данных.

    > [WACOM.NOTE]Это приложение проверяет маркеры на просроченность во время входа, но срок действия маркеров может истечь и в процессе использования приложения. Решение по обработке ошибок авторизации, связанных с просроченными маркерами, см. в записи [Кэширование и обработка просроченных маркеров в SDK под управлением мобильных служб Azure][Кэширование и обработка просроченных маркеров в SDK под управлением мобильных служб Azure].

3.  Дважды перезапустите приложение.

    Обратите внимание, что при первом перезапуске по-прежнему потребуется вход с использованием поставщика. При втором перезапуске будут использоваться кэшированные учетные данные и вход будет пропущен.

  [Кэширование и обработка просроченных маркеров в SDK под управлением мобильных служб Azure]: http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx
