<properties
	pageTitle="Предварительная версия Azure AD B2C | Microsoft Azure"
	description="Как с помощью Azure AD B2C создать веб-приложение, которое вызывает веб-API."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="dastrock"/>

# Предварительная версия Azure AD B2C: вызов веб-API из веб-приложения .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C позволяет добавлять в веб-приложения и веб-API мощные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье показано, как создать веб-приложение .NET MVC «Список дел», которое вызывает веб-API .NET с помощью токенов носителя OAuth 2.0. Веб-приложение и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Статья описывает вызов веб-интерфейсов API после проверки подлинности пользователя. При необходимости ознакомьтесь с основами Azure AD B2C, изложенными в [учебнике по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 1\. Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md).

## 2\. Создание приложения

Теперь необходимо создать приложение в каталоге B2C. Оно будет передавать в Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае веб-приложение и веб-API будут представлены одним **идентификатором приложения**, поскольку они включают одно приложение логики. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте

- Включить в приложение **веб-приложение и веб-API**.
- Введите `https://localhost:44316/` как **URL-адрес ответа** —URL-адрес по умолчанию для этого образца кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Скоро он вам понадобится.
- Скопируйте **идентификатор приложения**, назначенный приложению. Он вам также понадобится через некоторое время.

## 3\. Создание политик

В Azure AD B2C взаимодействие с каждым пользователем определяется [**политикой**](active-directory-b2c-reference-policies.md). Веб-приложение в этом примере кода включает три способа идентификации: регистрацию, вход в систему и изменение профиля. Вам нужно создать по одной политике каждого типа, как описано в [справочнике по политикам](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно сделайте следующее.

- Выберите в политике регистрации значение для атрибута **Отображаемое имя** и других атрибутов регистрации.
- Выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта** в каждой политике. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. Оно должно включать префикс `b2c_1_`. Эти имена политик понадобятся вам через некоторое время. 

Создав три политики, можно приступать к созданию приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о свойствах политик в Azure AD B2C см. в [учебнике по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Загрузка кода

Код для этого учебника размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Чтобы выполнить сборку образца, [загрузите схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip) или клонируйте ее:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

Готовое приложение также [доступно в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) или ветви `complete` того же репозитория.

Загрузив образец кода, откройте файл Visual Studio `.sln`, чтобы начать работу. Вы увидите, что решение содержит два проекта: `TaskWebApp` и `TaskService`. `TaskWebApp` — это внешний интерфейс в веб-приложении WPF для взаимодействия с пользователем. `TaskService` — это относящийся к приложению серверный веб-API, в котором хранится список дел каждого пользователя.

## 5\. Настройка службы задач

Когда проект `TaskService` получает запросы от `TaskWebApp`, он проверяет наличие допустимого маркера доступа для проверки подлинности запроса. Чтобы проверить маркер доступа, необходимо предоставить проекту `TaskService` определенные сведения о приложении. В корне проекта `TaskService` откройте файл `web.config` и замените значения в разделе `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
  ```

This article will not cover the details of securing the `TaskService`.  If you want to learn how a web API securely authenticates requests using Azure AD B2C, check out our
[Web API Getting Started article](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Configure the task web app

In order for the `TaskWebApp` to communicate with Azure AD B2C, there are a few common parameters that you will need to provide.  In the `TaskWebApp` project, open up the
`web.config` file in the root of the project and replace the values in the `<appSettings>` section.  These values will be used throughout the web app.

```
<appSettings> <add key="webpages:Version" value="3.0.0.0" /> <add key="webpages:Enabled" value="false" /> <add key="ClientValidationEnabled" value="true" /> <add key="UnobtrusiveJavaScriptEnabled" value="true" /> <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" /> <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" /> <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" /> <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" /> <add key="ida:RedirectUri" value="https://localhost:44316/" /> <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" /> <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" /> <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" /> <add key="api:TaskServiceUrl" value="https://localhost:44332/" /> </appSettings> ```

Также необходимо указать имя политики входа в двух декораторах `[PolicyAuthorize]`. Атрибут `[PolicyAuthorize]` используется для вызова определенной политики, когда пользователь пытается получить доступ к странице в приложении, которое требует выполнения проверки подлинности.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

## 7\. Получение маркеров доступа и вызов API задачи

В этом разделе показано, как выполнить обмен маркерами OAuth 2.0 в веб-приложении с помощью библиотек и платформ от Майкрософт. Если вы не знакомы с **кодами авторизации** и **маркерами доступа**, см. [справочник по протоколу OpenID Connect](active-directory-b2c-reference-protocols.md).

#### Получение кода авторизации

Первый шаг при вызове веб-API `TaskService` — проверка подлинности пользователя и получение **кода авторизации** из Azure AD. Код авторизации можно получить из Azure AD после успешного выполнения любой политики, включая политики регистрации, входа и изменения профиля.

Чтобы начать работу, установите ПО промежуточного слоя OWIN OpenID Connect с помощью консоли диспетчера пакетов Visual Studio. Мы будем использовать OWIN для отправки запросов проверки подлинности в Azure AD и обработки ответов:

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

Откройте файл `App_Start\Startup.Auth.cs`. В нем мы настроим конвейер проверки подлинности OWIN, указав данные каталога B2C и созданного вами приложения:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
	public const string PolicyKey = "b2cpolicy";
	public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

	// App config settings
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

	// B2C policy identifiers
	public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
	public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
	public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

	public void ConfigureAuth(IAppBuilder app)
	{
		app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

		app.UseCookieAuthentication(new CookieAuthenticationOptions());

		OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
		{
			// These are standard OpenID Connect parameters, with values pulled from web.config
			ClientId = clientId,
			RedirectUri = redirectUri,
			PostLogoutRedirectUri = redirectUri,
			Notifications = new OpenIdConnectAuthenticationNotifications
			{
				AuthenticationFailed = OnAuthenticationFailed,
				RedirectToIdentityProvider = OnRedirectToIdentityProvider,
				AuthorizationCodeReceived = OnAuthorizationCodeReceived,
			},
			Scope = "openid offline_access",

			// The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
			// endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
			ConfigurationManager = new PolicyConfigurationManager(
				String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
				new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

			// This piece is optional - it is used for displaying the user's name in the navigation bar.
			TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
			{
				NameClaimType = "name",
			},
		};

		app.UseOpenIdConnectAuthentication(options);
	}
	...
}
```

#### Получение маркера доступа с помощью кода авторизации

Теперь веб-приложение настроено на выполнение проверки подлинности пользователя с помощью каталога B2C, а также на получение кода авторизации из Azure AD. Следующий шаг — обмен этого кода авторизации на маркер доступа из Azure AD.

Каждый раз, когда веб-приложениям .NET требуется получить маркеры доступа из Azure AD, вы можете использовать **библиотеку проверки подлинности Active Directory (ADAL)**. В этом процессе не обязательно использовать библиотеку ADAL, но она упрощает многие аспекты работы, включая отправку сообщений о проверке подлинности OAuth 2.0, кэширование и обновление маркеров.

Сначала установите ADAL в проект `TaskWebApp`, снова используя консоль диспетчера пакетов:

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

Теперь необходимо передать в библиотеку ADAL код авторизации. Таким образом библиотека сможет получить для вас маркеры. ПО промежуточного слоя OWIN OpenID Connect уведомляет о необходимости использования этого кода авторизации. Уведомление появляется каждый раз, когда приложение получает код авторизации из Azure AD. В `App_Start\Startup.Auth.cs` реализуйте обработчик уведомлений `OnAuthorizationCodeReceived` с помощью ADAL:

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default.  In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache, for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
``` 

#### Получение маркера доступа в контроллерах

Мы получили маркер доступа для серверной части `TaskService` и сохранили его в кэше маркеров библиотеки ADAL. Пришло время воспользоваться им. `TasksController` отвечает за взаимодействие с API `TaskService`, а также отправляет в API HTTP-запросы для чтения, создания и удаления задач. Прежде чем отправлять HTTP-запрос, получите маркер доступа из ADAL:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	AuthenticationResult result = null;
	try
	{
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

		// We don't care which policy is used to access the TaskService, so let's use the most recent policy
		string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;
		
		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache, and throw an exception if it cannot do so.
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

		...
	}
	catch (AdalException ee)
	{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
	}
	...
}
``` 

Библиотека ADAL кэширует маркеры, обновляет их по окончании срока действия и сообщает о том, когда пользователь должен войти снова, вызывая исключения. Все, что вам нужно, — вызывать `AuthenticationContext.AcquireTokenSilentAsync(...)` каждый раз, когда требуется маркер в приложении.

#### Чтение задач из веб-API

Теперь, когда у вас есть маркер, его можно присоединить к HTTP-запросу GET в заголовке `Authorization`, чтобы безопасно вызвать `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	... 
	
	try 
	{
		HttpClient client = new HttpClient();
		HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

		// Add the token acquired from ADAL to the request headers
		request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
		HttpResponseMessage response = await client.SendAsync(request);

		if (response.IsSuccessStatusCode)
		{
			String responseString = await response.Content.ReadAsStringAsync();
			JArray tasks = JArray.Parse(responseString);
			ViewBag.Tasks = tasks;
			return View();
		}
		else
		{
			// If the call failed with access denied, then drop the current access token from the cache, 
			// and show the user an error indicating they might need to sign-in again.
			if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
			{
				var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
				foreach (TokenCacheItem tci in todoTokens)
					authContext.TokenCache.DeleteItem(tci);

				return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
			}
		}

		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
	}
	catch (Exception ex)
	{
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
	}
}

```

#### Создание и удаление задач в веб-API

С помощью этих же действий можно отправлять запросы POST и DELETE в `TaskService`. Просто вызовите `AuthenticationContext.AcquireTokenSilentAsync(...)` и присоедините к запросу полученный маркер в заголовке `Authorization`. Действие `Create` реализуется автоматически. Действие `Delete` в `TasksController.cs` следует выполнить самостоятельно.

## 8\. Выход пользователя

И последнее. При выходе пользователя из веб-приложения необходимо очистить кэш маркеров ADAL, чтобы удалить элементы, оставшиеся после проверенного сеанса пользователя:

```C#
// Controllers/AccountController.cs

public void SignOut()
{
	if (Request.IsAuthenticated)
	{
		// When the user signs out, clear their token cache in the process
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		authContext.TokenCache.Clear();

		HttpContext.GetOwinContext().Authentication.SignOut(
		new AuthenticationProperties(
			new Dictionary<string, string> 
			{ 
				{Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
			}), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
	}
}
```

## 9\. Запуск примера приложения

Наконец, выполните сборку и запуск проектов `TaskClient` и `TaskService`. Зарегистрируйтесь в приложении или войдите в него, а затем создайте задачи для пользователя, выполнившего вход. Выйдите и снова войдите от имени другого пользователя. Создайте задачи для этого пользователя. Обратите внимание, что в API хранятся задачи для каждого пользователя, поскольку API извлекает удостоверение пользователя из получаемого маркера доступа.

Готовый пример для сравнения в виде ZIP-файла находится [здесь](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Также можно клонировать его с GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO3-->