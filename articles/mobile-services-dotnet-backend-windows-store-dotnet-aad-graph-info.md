<properties 
	pageTitle="Доступ к сведениям в Azure Active Directory Graph (Магазин Windows) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как получить доступ к информации Azure Active Directory с помощью API Graph в приложении Магазина Windows." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="wesmc"/>

# Получение доступа к сведениям Graph Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]



Как и другие поставщики удостоверений, которые предлагаются в мобильных службах, поставщик Azure Active Directory (AAD) также поддерживает полнофункциональную [клиентскую библиотеку Graph], которую можно использовать для программного доступа к каталогу. В этом учебнике предстоит обновить приложение ToDoList, персонализировав интерфейс приложения прошедшего проверку подлинности пользователя на основе сведений о пользователях, полученных из каталога с помощью [клиентской библиотеки Graph].

>[AZURE.NOTE] Цель этого учебника - предоставить дополнительные сведения о проверке подлинности с помощью Azure Active Directory. Предполагается, что вы сначала пройдете учебник [Добавление проверки подлинности в приложение], используя поставщик проверки подлинности Azure Active Directory. В этом учебнике продолжается обновление приложения TodoItem, которое использовалось в учебнике [Добавление проверки подлинности в приложение]. 



Данный учебник включает в себя следующие этапы:


1. [Создание ключа доступа для регистрации приложения в AAD] 
2. [Создание настраиваемого интерфейса API GetUserInfo] 
3. [Обновление приложения для использования настраиваемого интерфейса API]
4. [Тестирование приложения]

##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам:

+ [Приступая к работе с проверкой подлинности]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Учебник по применению настраиваемого API-интерфейса]<br/>Демонстрирует вызов настраиваемого интерфейса API. 



## <a name="generate-key"></a>Создание ключа доступа для регистрации приложения в AAD


В учебнике [Добавление проверки подлинности в приложение] во время выполнения шага [Регистрация для использования имени входа Azure Active Directory] вы создали регистрацию для встроенного приложения. В этом разделе предстоит создать ключ для использования при чтении сведений каталога с помощью идентификатора клиента встроенного приложения. 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Создание настраиваемого интерфейса API GetUserInfo

В этом разделе предстоит создать настраиваемый API GetUserInfo, который будет использовать [клиентскую библиотеку Graph] для получения дополнительных сведений о пользователе из AAD.

Если вы не использовали настраиваемые API с мобильными службами, ознакомьтесь с [учебником по настраиваемым API], перед тем как переходить к этому разделу.

1. В Visual Studio щелкните правой кнопкой мыши проект внутреннего сервера .NET мобильной службы и нажмите кнопку **Управление пакетами NuGet**.
2. В диалоговом окне диспетчера пакетов NuGet введите условие поиска **ADAL**, чтобы найти и установить **библиотеку проверки подлинности Active Directory** для мобильной службы.
3. В диспетчере пакетов NuGet также установите **клиентскую библиотеку Graph Microsoft Azure Active Directory** для мобильной службы.

4. В Visual Studio щелкните правой кнопкой мыши папку **Контроллеры** проекта мобильной службы и нажмите кнопку **Добавить**, чтобы добавить новый **пользовательский контроллер мобильных служб Microsoft Azure** с именем `GetUserInfoController`. Клиент будет вызывать этот API для получения сведений о пользователе из Active Directory.

5. В новом файле GetUserInfoController.cs добавьте следующие операторы `using`.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. В файле GetUserInfoController.cs добавьте в класс следующий метод `GetAADToken`.

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
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    Этот метод использует параметры приложения, которые вы настроили в мобильной службе на [портале управления Azure], чтобы получить токен для доступа к Active Directory.

7. В файле GetUserInfoController.cs добавьте в класс следующий метод `GetAADUser`.

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


8. В файле GetUserInfoController.cs замените метод `Get` следующим методом. Этот метод возвращает объект `User` клиентской библиотеки Graph и требует, чтобы авторизованный пользователь вызвал API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. Сохраните изменения и создайте службу для проверки отсутствия ошибок в синтаксисе.
10. Опубликуйте проект мобильной службы в учетной записи Azure. 


## <a name="update-app"></a>Обновление приложения для использования GetUserInfo

В этом разделе предстоит обновить метод `AuthenticateAsync`, реализованный в учебнике [Добавление проверки подлинности в приложение] для вызова настраиваемого API и возвращения дополнительных сведений о пользователе из AAD. 

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, [Управление доступом в мобильных службах на основе ролей с помощью AAD], предстоит использовать управление доступом на основе ролей с помощью Azure Active Directory (AAD) для проверки членства в группе перед разрешением доступа. 



<!-- Anchors. -->
[Создание ключа доступа для регистрации приложения в AAD]: #generate-key
[Создание настраиваемого интерфейса API GetUserInfo]: #create-api
[Обновление приложения для использования настраиваемого интерфейса API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images -->


<!-- URLs. -->
[Добавление проверки подлинности в приложение]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Регистрация в службе Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Портал управления Azure]: https://manage.windowsazure.com/
[Учебник по применению настраиваемого API-интерфейса]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[Хранение серверных скриптов]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control/
[Регистрация для использования имени входа Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Клиентская библиотека Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
[Получение пользователя]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Управление доступом на основе ролей в мобильных службах с помощью AAD]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/



<!--HONumber=42-->
