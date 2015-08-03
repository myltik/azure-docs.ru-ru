<properties 
	pageTitle="Доступ к сведениям в Azure Active Directory Graph (Магазин Windows) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как получить доступ к информации Azure Active Directory с помощью API Graph в приложении Магазина Windows." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="wesmc"/>

# Получение доступа к сведениям Graph Azure Active Directory



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]

##Обзор

Как и другие поставщики удостоверений, которые предлагаются в мобильных службах, поставщик Azure Active Directory (AAD) также поддерживает полнофункциональный интерфейс API Graph, который можно использовать для программного доступа к каталогу. В этом учебнике вы обновите приложение ToDoList для персонализации интерфейса приложения прошедшего проверку подлинности пользователя, возвращая дополнительные сведения о пользователях, полученные из каталога с помощью [Graph REST API].

Дополнительные сведения об API Graph Azure AD см. в статье [Блог команды разработчиков Azure Active Directory Graph].


>[AZURE.NOTE]Цель этого учебника — предоставить дополнительные сведения о проверке подлинности с помощью Azure Active Directory. Предполагается, что вы выполнили задания учебника [Добавление проверки подлинности в приложение], используя поставщика проверки подлинности Azure Active Directory. В этом учебнике продолжается обновление приложения TodoItem, которое использовалось в учебнике [Добавление проверки подлинности в приложение].




##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Добавление проверки подлинности в приложение]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Учебник по пользовательским API]<br/>Способ вызова пользовательского API.



## <a name="generate-key"></a>Создание ключа доступа для регистрации приложения в AAD


В учебнике [Добавление проверки подлинности в приложение] во время выполнения шага [Регистрация для использования имени входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ для использования при чтении сведений каталога с помощью идентификатора клиента встроенного приложения.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Создание настраиваемого интерфейса API GetUserInfo

В этом разделе предстоит создать настраиваемый API GetUserInfo, который будет использовать API Graph Azure AD для получения дополнительных сведений о пользователе из AAD.

Если вы не использовали пользовательские API с мобильными службами, ознакомьтесь с [учебником по пользовательским API], перед тем как переходить к этому разделу.

1. В Visual Studio щелкните правой кнопкой мыши проект внутреннего сервера .NET мобильной службы и щелкните кнопку **Управление пакетами NuGet**.
2. В диалоговом окне диспетчера пакетов NuGet введите условие поиска **ADAL**, чтобы найти и установить **библиотеку проверки подлинности Active Directory** для мобильной службы. Это руководство в последний раз было протестировано с помощью версии 3.3.205061641-alpha (предварительный выпуск) пакета ADAL.


3. В Visual Studio щелкните правой кнопкой мыши папку **Контроллеры** проекта мобильной службы и нажмите кнопку **Добавить**, чтобы добавить новый **пользовательский контроллер мобильных служб Microsoft Azure** с именем `GetUserInfoController`. Клиент будет вызывать этот API для получения сведений о пользователе из Active Directory.

4. В новом файле GetUserInfoController.cs добавьте следующие операторы `using`.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. В новом файле GetUserInfoController.cs добавьте следующий класс `UserInfo`, содержащий сведения, которые необходимо собирать с AAD.

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
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. В файле GetUserInfoController.cs добавьте следующие переменные-члены в класс `GetUserInfoController`.

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. В файле GetUserInfoController.cs добавьте в класс следующий метод `GetAADToken`.

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    Этот метод использует параметры приложения, которые вы настроили в мобильной службе на [портале управления Azure], чтобы получить токен для доступа к Active Directory.

7. В файле GetUserInfoController.cs добавьте в класс следующий метод `GetAADUser`.

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    Этот метод получает идентификатор объекта Active Directory для авторизованного пользователя, а затем использует Graph REST API для получения сведений о пользователе из Active Diretory.


8. В файле GetUserInfoController.cs замените метод `Get` следующим методом. Этот метод возвращает сущность пользователя из Azure Active Directory с помощью Graph REST API и требует авторизации пользователя для вызова API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. Сохраните изменения и создайте службу для проверки отсутствия ошибок в синтаксисе.
10. Опубликуйте проект мобильной службы в учетной записи Azure. 


## <a name="update-app"></a>Обновление приложения для использования GetUserInfo

В этом разделе предстоит обновить метод `AuthenticateAsync`, реализованный в учебнике [Добавление проверки подлинности в приложение], для вызова настраиваемого API и возвращения дополнительных сведений о пользователе из AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, [Управление доступом в мобильных службах на основе ролей с помощью AAD], предстоит использовать управление доступом на основе ролей с помощью Azure Active Directory (AAD) для проверки членства в группе перед разрешением доступа.



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[Добавление проверки подлинности в приложение]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[портале управления Azure]: https://manage.windowsazure.com/
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Учебник по пользовательским API]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[учебником по пользовательским API]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Регистрация для использования имени входа Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Блог команды разработчиков Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Управление доступом в мобильных службах на основе ролей с помощью AAD]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md
 

<!---HONumber=July15_HO4-->