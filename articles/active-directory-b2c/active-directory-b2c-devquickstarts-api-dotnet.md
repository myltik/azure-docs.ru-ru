<properties
	pageTitle="Предварительная версия Azure AD B2C | Microsoft Azure"
	description="Как с помощью Azure AD B2C создать веб-API .NET, защищенный маркерами доступа OAuth 2.0 для проверки подлинности."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>
	
# Предварительная версия Azure AD B2C: создание веб-API .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

С помощью Azure AD B2C можно защитить веб-API, используя маркеры доступа OAuth 2.0. Таким образом, клиентские приложения, использующие Azure AD B2C, будут проходить проверку подлинности через API. В этой статье описывается, как создать приложение .NET MVC "Список дел", которое предусматривает регистрацию и вход пользователя, а также управление профилем. Список дел каждого пользователя будет храниться в службе задач. Это веб-API, который позволяет пользователям, прошедшим проверку подлинности, создавать и читать задачи в списке дел.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md).

## 2\. Создание приложения

Теперь необходимо создать приложение в каталоге B2C. Оно будет передавать в Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте

- Включить в приложение **веб-приложение (веб-API)**.
- Для веб-приложения используйте **универсальный код ресурса (URI) перенаправления** `https://localhost:44316/` — это стандартное расположение клиента веб-приложения для этого примера кода.
- Скопируйте **идентификатор приложения**, назначенный приложению. Скоро он вам понадобится.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Создание политик

В Azure AD B2C всякое взаимодействие с пользователем определяется [**политикой**](active-directory-b2c-reference-policies.md). Клиент в этом примере кода включает три способа идентификации: регистрацию, вход в систему и изменение профиля. Вам потребуется создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно сделайте следующее.

- В колонке поставщиков удостоверений выберите **Регистрация с помощью идентификатора пользователя** или **Регистрация по электронной почте**.
- В политике регистрации выберите **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике в качестве утверждения приложения выберите утверждения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. Эти имена политик понадобятся вам через некоторое время. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

## 4\. Загрузка кода

Код для этого учебника размещен на веб-сайте [GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip) или клонируйте ее.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

Кроме того, готовое приложение можно [скачать как ZIP-архив](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) или получить из ветви `complete` того же репозитория.

Скачав пример кода, откройте файл `.sln` Visual Studio, чтобы начать работу. Вы увидите, что решение содержит два проекта: `TaskWebApp` и `TaskService`. `TaskWebApp` — это веб-приложение MVC, с которым взаимодействует пользователь. `TaskService` — это серверный веб-интерфейс API приложения, в котором хранится список дел каждого пользователя.

## 5\. Настройка веб-приложения для задач

Когда пользователь взаимодействует с `TaskWebApp`, клиент отправляет запросы в Azure AD и получает маркеры, которые можно использовать для вызова веб-API `TaskService`. Чтобы пользователь мог войти и получить маркеры, необходимо предоставить `TaskWebApp` некоторые сведения о своем приложении. В корне проекта `TaskWebApp` откройте файл `web.config` и замените значения в разделе `<appSettings>`.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Кроме того, необходимо указать имя политики входа в двух декораторах `[PolicyAuthorize]`. Атрибут `[PolicyAuthorize]` используется для вызова определенной политики, когда пользователь пытается получить доступ к странице в приложении, требующем выполнения проверки подлинности.

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

Дополнительные сведения о том, как веб-приложения, например `TaskWebApp`, используют Azure AD B2C, см. в статье [Приступая к работе: вход в веб-приложения](active-directory-b2c-devquickstarts-web-dotnet.md).

## 6\. Защита API

Теперь, когда у вас есть клиент, который вызывает API от имени пользователей, можно защитить `TaskService` с помощью маркеров носителя OAuth 2.0. API может принимать и проверять токены с помощью библиотеки OWIN от Майкрософт.

#### Установка OWIN
Сначала установите конвейер проверки подлинности OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

#### Ввод данных B2C
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

#### Добавление класса запуска OWIN
Добавьте класс запуска OWIN в проект `TaskService` с именем `Startup.cs`. Щелкните правой кнопкой мыши проект и выберите в контекстном меню **Добавить**, затем **Новый элемент** и найдите OWIN.
  

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

#### Настройка проверки подлинности OAuth 2.0
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
			// This is where you specify that your API only accepts tokens from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

#### Настройка защиты контроллера задач
Теперь, когда приложение настроено для использования проверки подлинности OAuth 2.0, чтобы защитить веб-API, вам остается только добавить тег `[Authorize]` в контроллер задач. В этом контроллере выполняются все операции списка дел, поэтому мы защитим контроллер на уровне класса. Для более точного управления можно добавить тег `[Authorize]` к отдельным действиям.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

#### Получение сведений о пользователе из токена.
`TaskController` сохраняет задачи в базе данных, где с каждой задачей сопоставлен пользователь, являющийся ее "владельцем". Владелец определяется с помощью **идентификатора объекта** пользователя (поэтому необходимо добавить идентификатор объекта в качестве утверждения приложения во всех политиках).

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## 7\. Запуск примера приложения

Выполните сборку проектов `TaskWebApp` и `TaskService`, а затем запустите их. Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Создайте несколько задач в списке дел. Обратите внимание, что они сохраняются в API даже после остановки и перезапуска клиента.

## 8\. Изменение политик

Теперь, когда у вас есть API, защищенный с помощью Azure AD B2C, можно поэкспериментировать с политиками приложения и понаблюдать, как это влияет (или не влияет) на API. Вы можете управлять **утверждениями приложения** в политиках и изменять сведения о пользователе, доступные в веб-API. Любые дополнительные утверждения, добавляемые вами, будут доступны для веб-API .NET MVC в объекте `ClaimsPrincipal`, как описано выше.
<!--add **identity providers**
to the policies, allowing you users to sign into the Task Client using social accounts.  You can also 
-->

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO1-->