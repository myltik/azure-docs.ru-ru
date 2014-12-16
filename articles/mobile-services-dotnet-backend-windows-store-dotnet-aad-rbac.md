<properties urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Управление доступом в мобильных службах с помощью Azure Active Directory на основе ролей (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# Управление доступом на основе ролей в мобильных службах и Azure Active Directory

[WACOM.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]

Управление доступом на основе ролей (RBAC) - это методика назначения разрешений ролям, которые могут хранить пользователи, с четко определенными границами разрешенных и запрещенных действий для определенных классов пользователей. В этом учебнике содержатся инструкции по добавлению базового RBAC в мобильные службы Azure.

В этом учебнике демонстрируется управление доступом на основе ролей с проверкой членства в группе продаж, определенного в Azure Active Directory (AAD), для каждого пользователя. Проверка доступа будет осуществляться на внутреннем сервере мобильной службы .NET с использованием [клиентской библиотеки Graph] для Azure Active Directory. Запрашивать данные смогут только пользователи, принадлежащие группе продаж.


>[AZURE.NOTE] Цель этого учебника - предоставить дополнительные сведения о методах авторизации в проверке подлинности. Предполагается, что вы сначала пройдете учебник [Добавление проверки подлинности к приложению], используя поставщик проверки подлинности Azure Active Directory. В этом учебнике продолжается обновление приложения TodoItem, которое использовалось в учебнике [Добавление проверки подлинности в приложение].

Данный учебник включает в себя следующие этапы.

1. [Создание группы продаж с членством]
2. [Создание ключа для встроенного приложения]
3. [Создание пользовательского атрибута авторизации] 
4. [Добавление проверки доступа на основе ролей в операции базы данных]
5. [Тестирование клиентского доступа]

Для работы с данным учебником требуется следующее:

* Visual Studio 2013 в Windows 8.1.
* Завершение [Добавление проверки подлинности к приложению] с помощью поставщика проверки подлинности Azure Active Directory.
* Пройдите учебник [Хранение серверных скриптов], чтобы ознакомиться с вариантами использования репозитория Git для хранения скриптов сервера.
 


## <a name="create-group"></a>Создание группы продаж с членством

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]


## <a name="generate-key"></a>Создание ключа для встроенного приложения


В учебнике [Добавление проверки подлинности в приложение] во время выполнения шага [Регистрация для использования имени входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ, который будет использоваться при чтении сведений каталога с помощью идентификатора клиента встроенного приложения. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-custom-authorization-attribute"></a>Создание пользовательского атрибута авторизации в мобильной службе 

В этом разделе предстоит создать пользовательский атрибут авторизации, который можно использовать для выполнения проверок доступа в операциях мобильной службы. Атрибут будет искать группу Active Directory в соответствии с переданным именем роли. Затем он выполнит проверки доступа на основе членства в этой группе.

1. В Visual Studio щелкните правой кнопкой мыши проект внутреннего сервера .NET мобильной службы и выберите пункт **Управление пакетами NuGet**.

2. В диалоговом окне диспетчера пакетов NuGet введите условие поиска **ADAL**, чтобы найти и установить **библиотеку проверки подлинности Active Directory** для мобильной службы.

3. В диспетчере пакетов NuGet также установите **клиентскую библиотеку Graph Microsoft Azure Active Directory** для мобильной службы.


4. В Visual Studio щелкните правой кнопкой мыши проект мобильной службы и нажмите кнопку **Добавить**, а затем **Создать папку**. Назовите папку **Служебные программы**.

5. В Visual Studio щелкните правой кнопкой мыши созданную папку **Служебные программы** и добавьте новый файл класса с именем **AuthorizeAadRole.cs**.

    ![][0]

6. В начало файла AuthorizeAadRole.cs добавьте следующие `используемые` операторы. 

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7. В файле AuthorizeAadRole.cs добавьте следующий тип перечисления в пространство имен "Служебные программы". В этом примере используется только роль **Продажи**. Остальные примеры содержат группы, которые можно использовать.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8. В файле AuthorizeAadRole.cs добавьте следующее определение класса `AuthorizeAadRole` в пространство имен "Служебные программы".

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9.  AuthorizeAadRole.cs добавьте следующий `GetAADToken` метод класса `AuthorizeAadRole`.

    >[WACOM.NOTE]  Следует кэшировать токен вместо того, чтобы создавать новый при каждой проверке доступа. Затем обновите кэш, если попытки использовать токен вызовут исключение AccessTokenExpiredException, как указано в [клиентской библиотеке Graph]. Для простоты это не показано в приведенном ниже коде, но при применении такого подхода количество дополнительного трафика к Active Directory будет снижено.  

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;
            
            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)
                token = result.AccessToken;

            return token;
        }

10. В файле AuthorizeAadRole.cs обновите метод `OnAuthorization` в классе `AuthorizeAadRole` с помощью следующего кода. Этот код использует [клиентскую библиотеку Graph] для поиска группы Active Directory, соответствующей роли. Затем он проверяет членство пользователя в этой группе для его авторизации.

    >[WACOM.NOTE] Этот код ищет группу Active Directory по имени. Во многих случаях рекомендуется сохранить идентификатор группы как параметр приложения мобильной службы. Это нужно, поскольку имя группы может измениться, но идентификатор останется прежним. Однако при изменении имени группы обычно меняется по крайней мере область роли, что может также потребовать обновления кода мобильной службы.  

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

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
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
						"User is not logged in or not a member of the required group");
            }
        }

8. Сохраните изменения в файле AuthorizeAadRole.cs.

## <a name="add-access-checking"></a>Добавление проверки доступа на основе ролей в операции базы данных

1. В Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте файл TodoItemController.cs.

2. В файле TodoItemController.cs добавьте `используемый`оператор для пространства имен служебной программы, который содержит пользовательский атрибут авторизации. 

        using todolistService.Utilities;

3. В файле TodoItemController.cs вы можете добавить атрибут в класс контроллера или отдельные методы в зависимости от желаемого способа проверки доступа. Если необходимо, чтобы все операции контроллера проверяли доступ на основе той же роли, просто добавьте атрибут в класс. Добавьте атрибут в класс для тестирования этого учебника, как показано далее.

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


## <a name="test-client"></a>Тестирование доступа клиентского приложения

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]





<!-- Anchors. -->
[Создание группы продаж с членством]: #create-group
[Создание ключа для встроенного приложения]: #generate-key
[Создание пользовательского атрибута авторизации]: #create-custom-authorization-attribute
[Добавление проверки доступа на основе ролей в операции базы данных]: #add-access-checking
[Тестирование клиентского доступа]: #test-client



<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Добавление проверки подлинности в приложение]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Регистрация в службе Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Портал управления Azure]: https://manage.windowsazure.com/
[Сценарии синхронизации каталогов]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Хранение серверных скриптов]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control/
[Регистрация для использования имени входа Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Клиентская библиотека графа]: http://go.microsoft.com/fwlink/?LinkId=510536
[IsMemberOf]: http://msdn.microsoft.com/ru-ru/library/azure/dn151601.aspx
