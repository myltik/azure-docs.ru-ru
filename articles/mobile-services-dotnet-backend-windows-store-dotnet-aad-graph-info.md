<properties linkid="develop-mobile-tutorials-dotnet-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# Получение доступа к сведениям Graph Azure Active Directory

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/" title="Магазин Windows C#" class="current">Магазин Windows C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title="Сервер .NET" class="current">Сервер .NET</a> |
    <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="Сервер JavaScript">Сервер JavaScript</a>
</div>

Как и другие поставщики удостоверений, которые предлагаются в мобильных службах, поставщик Azure Active Directory (AAD) также поддерживает обширную [клиентскую библиотеку Graph][клиентскую библиотеку Graph], которую можно использовать для программного доступа к каталогу. В этом учебнике предстоит обновить приложение ToDoList, персонализировав интерфейс приложения прошедшего проверку подлинности пользователя на основе сведений о пользователях, полученных из каталога с помощью [клиентской библиотеки Graph][клиентскую библиотеку Graph].

> [WACOM.NOTE] Цель этого учебника — предоставить дополнительные сведения о проверке подлинности с помощью Azure Active Directory. Предполагается, что вы прошли учебник [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности], используя поставщика проверки подлинности Azure Active Directory. В этом учебнике обновляется приложение TodoItem, использованное в учебнике [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности].

Данный учебник включает в себя следующие этапы.

1.  [Создание ключа доступа для регистрации приложения в AAD][Создание ключа доступа для регистрации приложения в AAD]
2.  [Создание пользовательского интерфейса API GetUserInfo][Создание пользовательского интерфейса API GetUserInfo]
3.  [Обновление приложения для использования пользовательского API][Обновление приложения для использования пользовательского API]
4.  [Тестирование приложения][Тестирование приложения]

## Предварительные требования

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

-   [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности]
    Добавление обязательного входа в демонстрационное приложение TodoList.

-   [Учебник по пользовательским API][Учебник по пользовательским API]
    Способ вызова пользовательского API.

## <a name="generate-key"></a>Создание ключа доступа для регистрации приложения в AAD

В учебнике [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности] во время выполнения шага [Регистрация для использования входа Azure Active Directory][Регистрация для использования входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ для использования при чтении сведений каталога с помощью идентификатора клиента встроенного приложения.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-api"></a>Создание пользовательского интерфейса API GetUserInfo

В этом разделе предстоит создать пользовательский API GetUserInfo, который будет использовать [клиентскую библиотеку Graph][клиентскую библиотеку Graph] для получения дополнительных сведений о пользователе из AAD.

Если вы не использовали пользовательские API с мобильными службами, ознакомьтесь с [учебником по пользовательским API][Учебник по пользовательским API], перед тем как переходить к этому разделу.

1.  В Visual Studio щелкните правой кнопкой мыши проект внутреннего сервера .NET мобильной службы и щелкните кнопку **Управление пакетами NuGet**.
2.  В диалоговом окне диспетчера пакетов NuGet введите условие поиска **ADAL**, чтобы найти и установить **библиотеку проверки подлинности Active Directory** для мобильной службы.
3.  В диспетчере пакетов NuGet также установите **клиентскую библиотеку Graph Microsoft Azure Active Directory** для мобильной службы.

4.  В Visual Studio щелкните правой кнопкой мыши папку **Контроллеры** проекта мобильной службы и нажмите кнопку **Добавить**, чтобы добавить новый **пользовательский контроллер мобильных служб Microsoft Azure** с именем `GetUserInfoController`. Клиент будет вызывать этот API для получения сведений о пользователе из Active Directory.

5.  В новом файле GetUserInfoController.cs добавьте следующие операторы `using`.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6.  В файле GetUserInfoController.cs добавьте в класс следующий метод `GetAADToken`.

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireToken(GraphResourceId, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    Этот метод использует параметры приложения, которые вы настроили в мобильной службе на [портале управления Azure][портале управления Azure], чтобы получить токен для доступа к Active Directory.

7.  В файле GetUserInfoController.cs добавьте в класс следующий метод `GetAADUser`.

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    Этот метод получает идентификатор объекта Active Directory для авторизованного пользователя, а затем использует клиентскую библиотеку Graph для получения сведений о пользователе из Active Diretory.

8.  В файле GetUserInfoController.cs замените метод `Get` на следующий метод. Этот метод возвращает объект `User` клиентской библиотеки Graph и требует, чтобы авторизованный пользователь вызвал API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9.  Сохраните изменения и создайте службу для проверки отсутствия ошибок в синтаксисе.
10. Опубликуйте проект мобильной службы в учетной записи Azure.

## <a name="update-app"></a>Обновление приложения для использования GetUserInfo

В этом разделе предстоит обновить метод `AuthenticateAsync`, реализованный в учебнике [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности] для вызова пользовательского API и возвращения дополнительных сведений о пользователе из AAD.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app][mobile-services-aad-graph-info-update-app]]

## <a name="test-app"></a> Тестирование приложения

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app][mobile-services-aad-graph-info-test-app]]

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, [Управление доступом в мобильных службах на основе ролей с помощью AAD][Управление доступом в мобильных службах на основе ролей с помощью AAD], предстоит использовать управление доступом на основе ролей с помощью Azure Active Directory (AAD) для проверки членства в группе перед разрешением доступа.

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/ "Магазин Windows C#"
  [Сервер .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ "Сервер .NET"
  [Сервер JavaScript]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "Сервер JavaScript"
  [клиентскую библиотеку Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
  [Начало работы с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [Создание ключа доступа для регистрации приложения в AAD]: #generate-key
  [Создание пользовательского интерфейса API GetUserInfo]: #create-api
  [Обновление приложения для использования пользовательского API]: #update-app
  [Тестирование приложения]: #test-app
  [Учебник по пользовательским API]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
  [Регистрация для использования входа Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [портале управления Azure]: https://manage.windowsazure.com/
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [Управление доступом в мобильных службах на основе ролей с помощью AAD]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
