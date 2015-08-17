<properties 
	pageTitle="Управление доступом в мобильных службах с помощью Azure Active Directory на основе ролей (Магазин Windows) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как управлять доступом на основе ролей Azure Active Directory в приложении Магазина Windows." 
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

# Управление доступом на основе ролей в мобильных службах и Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##Обзор

Управление доступом на основе ролей (RBAC) — это способ назначения разрешений ролям, которые могут быть назначены пользователям. Этот способ четко определяет рамки того, что определенные классы пользователей могут делать, а что — нет. Этот учебник поможет разобраться, как добавлять основное управление RBAC в мобильные службы Azure.

В этом учебнике демонстрируется управление доступом на основе ролей с проверкой членства в группе продаж, определенного в Azure Active Directory (AAD), для каждого пользователя. Проверка доступа будет осуществляться на внутреннем сервере мобильной службы .NET с использованием [Graph REST API] для Azure Active Directory. Запрашивать данные смогут только пользователи, принадлежащие группе продаж.


>[AZURE.NOTE]Цель этого учебника — предоставить дополнительные сведения о методах авторизации в проверке подлинности. Предполагается, что вы сначала закончите учебник [Добавление проверки подлинности в приложение], используя поставщика проверки подлинности Azure Active Directory. В этом учебнике продолжается обновление приложения TodoItem, которое использовалось в учебнике [Добавление проверки подлинности в приложение].

##Предварительные требования

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Завершение учебника [Добавление проверки подлинности в приложение] с помощью поставщика проверки подлинности Azure Active Directory.

 


##Создание ключа для встроенного приложения


В учебнике [Добавление проверки подлинности в приложение] во время выполнения шага [Регистрация для использования имени входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ для использования при чтении сведений каталога с помощью идентификатора клиента встроенного приложения.

Если вы выполнили задания учебника [Получение доступа к сведениям Graph Azure Active Directory], значит, вы уже выполнили этот шаг, поэтому этот раздел можно пропустить.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Создание группы продаж с членством

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]



##Создание пользовательского атрибута авторизации в мобильной службе 

В этом разделе предстоит создать пользовательский атрибут авторизации, который можно использовать для выполнения проверок доступа в операциях мобильной службы. Атрибут будет искать группу Active Directory в соответствии с переданным именем роли. Затем он выполнит проверки доступа на основе членства этой группы.

1. В Visual Studio щелкните правой кнопкой мыши проект внутреннего сервера .NET мобильной службы и щелкните кнопку **Управление пакетами NuGet**.

2. В диалоговом окне диспетчера пакетов NuGet введите условие поиска **ADAL**, чтобы найти и установить **библиотеку проверки подлинности Active Directory** для мобильной службы. Это руководство в последний раз было протестировано с помощью версии 3.3.205061641-alpha (предварительный выпуск) пакета ADAL.

3. В Visual Studio щелкните правой кнопкой мыши проект мобильной службы и нажмите кнопку **Добавить**, а затем — **Создать папку**. Назовите папку **Служебные программы**.

4. В Visual Studio щелкните правой кнопкой мыши созданную папку **Служебные программы** и добавьте новый файл класса с именем **AuthorizeAadRole.cs**.

    ![][0]

5. В начало файла AuthorizeAadRole.cs добавьте следующие операторы `using`.

		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using System.Web.Http.Controllers;
		using System.Web.Http.Filters;
		using Newtonsoft.Json;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.IO;

6. В файле AuthorizeAadRole.cs добавьте следующий тип перечисления в пространство имен "Служебные программы". В этом примере используется только роль **Продажи**. Остальные примеры содержат группы, которые можно использовать.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. В файле AuthorizeAadRole.cs добавьте следующее определение класса `AuthorizeAadRole` в пространство имен "Служебные программы".

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
	        private ApiServices services = null;
	
	        // Constants used with ADAL and the Graph REST API for AAD
	        private const string AadInstance = "https://login.windows.net/{0}";
	        private const string GraphResourceId = "https://graph.windows.net/";
	        private const string APIVersion = "?api-version=2013-04-05";
	
	        // App settings pulled from the Mobile Service
	        private string tenantdomain;
	        private string clientid;
	        private string clientkey;
	        private Dictionary<int, string> groupIds = new Dictionary<int, string>();
	
	        private string token = null;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

	        // private class used to serialize the Graph REST API web response
	        private class MembershipResponse
	        {
	            public bool value;
	        }

            public AadRoles Role { get; private set; }

            // Generate a local dictionary for the role group ids configured as 
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }

            // Use ADAL and the authentication app settings from the Mobile Service to 
            // get an AAD access token
            private string GetAADToken()
            {
            }

            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }

            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }



8. В файле AuthorizeAadRole.cs обновите метод `InitGroupIds` в классе `AuthorizeAadRole` с помощью следующего кода: Этот метод создает словарь-сопоставление идентификаторов группы для каждой роли.

        private void InitGroupIds()
        {
            string groupId;

            if (services == null)
                return;

            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }


9. В файле AuthorizeAadRole.cs обновите метод `GetAADToken` в классе `AuthorizeAadRole`. Этот метод использует параметры приложения, хранящиеся в мобильной службе, для получения маркера доступа к AAD из ADAL.

    >[AZURE.NOTE]ADAL для .NET по умолчанию включает содержащийся в памяти кэш маркеров, чтобы разгрузить интенсивный сетевой трафик, связанный с Active Directory. Тем не менее, можно написать собственную реализацию кэша или отключить кэширование полностью. Дополнительные сведения см. в статье [Библиотека проверки подлинности Azure AD для .NET].

        // Use ADAL and the authentication app settings from the Mobile Service to get an AAD access token
        private async Task<string> GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);

            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);
            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

10. В файле AuthorizeAadRole.cs обновите метод `CheckMembership` в классе `AuthorizeAadRole`. Этот метод получает идентификатор объекта пользователя. Затем Graph REST API службы AAD используется для проверки этого идентификатора объекта, чтобы узнать, является ли он идентификатором члена группы, связанной с выбранной ролью класса `AuthorizeAadRole`

        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format(""groupId":"{0}","memberId":"{1}"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }

                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }

            return membership;
        }


11. В файле AuthorizeAadRole.cs обновите метод `OnAuthorization` в классе `AuthorizeAadRole` с помощью следующего кода: Этот код предполагает, что пользователь, вызывающий мобильную службу, прошел проверку подлинности в AAD. Он возвращает идентификатор объекта пользователя AAD и проверяет членство в группе Active Directory, которая соответствует роли.

    >[AZURE.NOTE]Поиск группы Active Directory осуществляется по имени. Тем не менее, во многих случаях лучше сохранить идентификатор группы как параметр приложения мобильной службы. Это связано с тем, что имя группы может измениться, но идентификатор останется прежним.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
            }

            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();

                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");

                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }

12. Сохраните изменения в файле AuthorizeAadRole.cs.

##Добавление проверки доступа на основе ролей в операции базы данных

1. В Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте файл TodoItemController.cs.

2. В файле TodoItemController.cs добавьте оператор `using` для пространства имен служебных программ, который содержит пользовательский атрибут авторизации.

        using todolistService.Utilities;

3. В файле TodoItemController.cs вы можете добавить атрибут в класс контроллера или индивидуальные методы в зависимости от желаемого способа проверки доступа. Если необходимо, чтобы все операции контроллера проверяли доступ на основе той же роли, просто добавьте атрибут в класс. Добавьте атрибут в класс для тестирования этого учебника, как показано далее.

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    Если требуется только проверка доступа к операциям вставки, обновления и удаления, следует задать атрибут только для этих методов, как показано далее.

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. Сохраните файл TodoItemController.cs и создайте мобильную службу, чтобы убедиться в отсутствии ошибок в синтаксисе.
5. Опубликуйте мобильную службу в учетной записи Azure.


##Тестирование доступа клиентского приложения

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Добавление проверки подлинности в приложение]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Management Portal]: https://manage.windowsazure.com/
[портале управления Azure] https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Регистрация для использования имени входа Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Получение доступа к сведениям Graph Azure Active Directory]: mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info.md
[Библиотека проверки подлинности Azure AD для .NET]: https://msdn.microsoft.com/library/azure/jj573266.aspx

<!---HONumber=August15_HO6-->