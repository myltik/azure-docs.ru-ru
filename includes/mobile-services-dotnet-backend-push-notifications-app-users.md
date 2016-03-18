
1. В обозревателе решений в Visual Studio разверните папку App\_Start и откройте файл WebApiConfig.cs.

2. В метод Register после определения **ConfigOptions** добавьте следующий код:

        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;
 
	Это потребует проверки подлинности пользователя перед регистрацией для получения push-уведомлений.

2. Щелкните по проекту правой кнопкой мыши, выберите **Добавить**, затем **Класс...**.

3. Назовите `PushRegistrationHandler` нового пустого класса, затем нажмите **Добавить**.

4. В начало страницы кода добавьте следующие операторы **using**:

		using System.Threading.Tasks; 
		using System.Web.Http; 
		using System.Web.Http.Controllers; 
		using Microsoft.WindowsAzure.Mobile.Service; 
		using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
		using Microsoft.WindowsAzure.Mobile.Service.Security; 

5. Замените существующий метод **PushRegistrationHandler** следующим кодом:
 
	    public class PushRegistrationHandler : INotificationHandler
	    {
	        public Task Register(ApiServices services, HttpRequestContext context,
            NotificationRegistration registration)
	        {
	            try
	            {
	                // Perform a check here for user ID tags, which are not allowed.
	                if(!ValidateTags(registration))
	                {
	                    throw new InvalidOperationException(
	                        "You cannot supply a tag that is a user ID.");                    
	                }
	
	                // Get the logged-in user.
	                var currentUser = context.Principal as ServiceUser;
	
	                // Add a new tag that is the user ID.
	                registration.Tags.Add(currentUser.Id);
	
	                services.Log.Info("Registered tag for userId: " + currentUser.Id);
	            }
	            catch(Exception ex)
	            {
	                services.Log.Error(ex.ToString());
	            }
	                return Task.FromResult(true);
	        }
	
	        private bool ValidateTags(NotificationRegistration registration)
	        {
	            // Create a regex to search for disallowed tags.
	            System.Text.RegularExpressions.Regex searchTerm =
	            new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
	                System.Text.RegularExpressions.RegexOptions.IgnoreCase);
	
	            foreach (string tag in registration.Tags)
	            {
	                if (searchTerm.IsMatch(tag))
	                {
	                    return false;
	                }
	            }
	            return true;
	        }
		
	        public Task Unregister(ApiServices services, HttpRequestContext context, 
	            string deviceId)
	        {
	            // This is where you can hook into registration deletion.
	            return Task.FromResult(true);
	        }
	    }

	В ходе регистрации вызывается метод **Register**. Это позволяет добавить к регистрации тег, представляющий собой идентификатор вошедшего в систему пользователя. Указанные теги проверяются, чтобы пользователь не мог зарегистрироваться под идентификатором другого пользователя. Когда этому пользователю отправляется уведомление, оно приходит на это и все другие устройства, зарегистрированные пользователем.

6. Разверните папку Controllers, откройте файл проекта TodoItemController.cs, найдите метод **PostTodoItem** и замените строку кода, вызывающую **SendAsync**, следующим кодом:

        // Get the logged-in user.
		var currentUser = this.User as ServiceUser;
		
		// Use a tag to only send the notification to the logged-in user.
        var result = await Services.Push.SendAsync(message, currentUser.Id);

7. Повторно опубликуйте проект мобильной службы.

Теперь служба будет отправлять push-уведомления (с текстом вставленного элемента) всем регистрациям, созданным пользователем, вошедшим в систему, с использованием тега идентификатора пользователя.
 

<!---HONumber=Oct15_HO3-->