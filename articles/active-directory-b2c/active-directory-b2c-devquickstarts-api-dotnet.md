<properties
	pageTitle="Предварительная версия Azure AD B2C | Microsoft Azure"
	description="Как с помощью Azure Active Directory B2C создать веб-API .NET, защищенный маркерами доступа OAuth 2.0 для проверки подлинности."
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
	ms.topic="hero-article"
	ms.date="05/16/2016"
	ms.author="dastrock"/>

# Предварительная версия Azure Active Directory B2C: создание веб-API .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

С помощью Azure Active Directory (Azure AD) B2C можно защитить веб-API с помощью маркера доступа OAuth 2.0. Эти маркеры позволяют клиентским приложениям, использующим Azure AD B2C, проходить проверку подлинности для API. В этой статье описывается, как создать MVC-приложение .NET "Список дел", которое предусматривает регистрацию и вход пользователя, а также управление профилем. Списки дел пользователей будут храниться в службе задач. Это веб-API, которое позволяет пользователям, прошедшим проверку подлинности, создавать и считывать задачи из своих списков дел.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех ваших пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## Создание приложения

Затем необходимо создать приложение в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

- Включите **веб-приложение** или **веб-API** в свое приложение.
- Используйте **универсальный код ресурса перенаправления** `https://localhost:44316/` для веб-приложения. Это стандартное расположение клиента веб-приложения для этого примера.
- Скопируйте **идентификатор приложения**, назначенный приложению. Он понадобится вам позднее.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Клиент в этом примере кода включает три способа работы с идентификацией: регистрацию, вход в систему и изменение профиля. Вам потребуется создать по одной политике для каждого типа, как описано в [справочнике по политикам](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании своих трех политик обязательно сделайте следующее:

- В колонке поставщиков удостоверений выберите **Регистрация с помощью идентификатора пользователя** или **Регистрация по электронной почте**.
- В политике регистрации выберите **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике в качестве утверждения приложения выберите утверждения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. Эти имена политик понадобятся вам через некоторое время.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

## Загрузка кода

Код этого учебника размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

Кроме того, можно скачать готовое приложение [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу. Файл решения содержит два проекта: `TaskWebApp` и `TaskService`. `TaskWebApp` — это веб-приложение MVC, с которым взаимодействует пользователь. `TaskService` — веб-API серверной части приложения, которое хранит список дел для каждого пользователя.

## Настройка веб-приложения для задач

Когда пользователь взаимодействует с `TaskWebApp`, клиент отправляет запросы в Azure AD и получает маркеры, которые можно использовать для вызова веб-API `TaskService`. Чтобы пользователь мог выполнить вход и получить маркеры, необходимо предоставить `TaskWebApp` некоторые сведения о приложении. В корне проекта `TaskWebApp` откройте файл `web.config` и замените значения в разделе `<appSettings>`.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Кроме того, необходимо указать имя политики входа в двух декораторах `[PolicyAuthorize]`. Атрибут `[PolicyAuthorize]` используется для вызова определенной политики, когда пользователь пытается обратиться к странице приложения, требующей проверки подлинности.

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

Чтобы узнать, как веб-приложения, такие как `TaskWebApp`, используют Azure AD B2C, обратитесь к разделу [Создание веб-приложения .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Защита API

Теперь, когда у вас есть клиент, который вызывает API от имени пользователей, можно защитить `TaskService` с помощью токенов носителя OAuth 2.0. API может принимать и проверять токены с помощью библиотеки OWIN от Майкрософт.

### Установка OWIN
Сначала установите конвейер проверки подлинности OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### Ввод данных B2C
В корне проекта `TaskService` откройте файл `web.config` и замените значения в разделе `<appSettings>`. Эти значения будут использоваться в API и библиотеке OWIN.

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

### Добавление класса запуска OWIN
Добавьте класс запуска OWIN в проект `TaskService` с именем `Startup.cs`. Щелкните правой кнопкой мыши проект и выберите **Добавить** в контекстном меню, затем **Новый элемент**, после чего найдите OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### Настройка проверки подлинности OAuth 2.0
Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	// These values are pulled from web.config
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
	private const string discoverySuffix = ".well-known/openid-configuration";

	public void ConfigureAuth(IAppBuilder app)
	{   
		TokenValidationParameters tvps = new TokenValidationParameters
		{
			// This is where you specify that your API accepts tokens only from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

### Настройка защиты контроллера задач
Теперь когда приложение настроено для использования проверки подлинности OAuth 2.0, вы можете добавить тег `[Authorize]` к контроллеру задач для защиты веб-API. В этом контроллере выполняются все операции со списками дел, поэтому нужно защитить весь контроллер на уровне класса. Для более точного управления можно добавить тег `[Authorize]` к отдельным действиям.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

### Получение сведений о пользователе из токена.
`TasksController` сохраняет задачи в базе данных, где каждой задаче соответствует пользователь, являющийся ее владельцем. Владелец определяется **идентификатором объекта** пользователя. (Поэтому необходимо добавить идентификатор объекта в качестве утверждения приложения во все свои политики.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## Запуск примера приложения

Наконец, выполните сборку и запуск `TaskWebApp` и `TaskService`. Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Создайте несколько задач в списке дел. Обратите внимание, что они сохраняются в API даже после остановки и перезапуска клиента.

## Изменение политик

Теперь, когда у вас есть API, защищенный с помощью Azure AD B2C, можно поэкспериментировать с политиками приложения и понаблюдать, как это влияет (или не влияет) на API. Вы можете <!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->управлять утверждениями приложения в политиках и изменять сведения о пользователе, доступные в веб-API. Любые дополнительные утверждения, которые вы добавляете, будут доступны для веб-API .NET MVC в объекте `ClaimsPrincipal`, как описано ранее в этой статье.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0518_2016-->