

Приведенные ниже инструкции и снимки экрана применимы для тестирования клиента Магазина Windows, но ими можно воспользоваться на любой из платформ, поддерживаемых мобильными службами Azure.


1. В Visual Studio откройте файл MainPage.xaml.cs и добавьте следующий оператор `using` в верхнюю часть файла.
 
        using System.Net.Http;

2. В файле MainPage.xaml.cs добавьте в пространство имен следующее определение класса, чтобы помочь сериализировать сведения пользователя.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
            
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ", otherMails);
	        }
	    }


3. В файле MainPage.xaml.cs обновите метод `AuthenticateAsync` для вызова пользовательского API, чтобы вернуть дополнительные сведения о пользователе из AAD.

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
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);

                    UserInfo userInfo = await App.MobileService
						.InvokeApiAsync<UserInfo>("getuserinfo", HttpMethod.Get, null);

                    message = string.Format("User info for the logged in user...\n\n{0}",userInfo.ToString());
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


4. Сохраните изменения и создайте службу для проверки отсутствия ошибок в синтаксисе.

<!---HONumber=Oct15_HO3-->