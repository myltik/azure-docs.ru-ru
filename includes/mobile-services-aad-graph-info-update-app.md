

Приведенные ниже инструкции и снимки экрана применимы для тестирования клиента Магазина Windows, но ими можно воспользоваться на любой из платформ, поддерживаемых мобильными службами Azure. 


1. В Visual Studio откройте файл MainPage.xaml.cs и добавьте следующий оператор  `using` в верхнюю часть файла.
 
        using System.Net.Http;

2. В файле MainPage.xaml.cs добавьте в пространство имен следующее определение класса, чтобы помочь сериализировать сведения пользователя.

        public class UserInfo
        {
            public String displayName { get; set; }
            public String streetAddress { get; set; }
            public String city { get; set; }
            public String state { get; set; }
            public String postalCode { get; set; }
        }


3. В файле MainPage.xaml.cs обновите метод  `AuthenticateAsync` для вызова пользовательского API, чтобы вернуть дополнительную информацию о пользователе из AAD. 

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
                    UserInfo userInfo = await App.MobileService.InvokeApiAsync<UserInfo>("getuserinfo", 
                        HttpMethod.Get, null);
                    message = string.Format("{0}, you are now logged in.\n\nYour address is...\n\n{1}\n{2}, {3} {4}", 
                        userInfo.displayName, userInfo.streetAddress, userInfo.city, userInfo.state, 
                        userInfo.postalCode);
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

<!--HONumber=42-->
