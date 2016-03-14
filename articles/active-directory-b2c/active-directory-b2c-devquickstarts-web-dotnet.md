<properties
	pageTitle="Предварительная версия Azure Active Directory B2C | Microsoft Azure"
	description="Инструкции по созданию веб-приложений, позволяющих пользователям выполнять вход, регистрироваться и управлять профилем, с помощью Azure Active Directory B2C."
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
	ms.date="02/19/2016"
	ms.author="dastrock"/>

# Предварительная версия Azure AD B2C: создание веб-приложения API .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C позволяет добавлять в веб-приложения мощные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье описывается, как создать веб-приложение MVC для .NET, которое предусматривает регистрацию и вход пользователя, а также управление профилем. Приложение будет поддерживать регистрацию и вход в систему по имени пользователя или адресу электронной почты, а также учетной записи в социальной сети, такой как Facebook или Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех ваших пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## Создание приложения

Затем необходимо создать приложение в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

- Включите в приложение **веб-приложение или веб-API**.
- Введите `https://localhost:44316/` как **URL-адрес ответа**. Это — URL-адрес по умолчанию для этого примера кода.
- скопировать **идентификатор приложения**, назначенный вашему приложению. Оно понадобится вам позднее.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Этот пример кода включает три способа идентификации: регистрацию, вход в систему и изменение профиля. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех необходимых политик обязательно сделайте следующее:

- В колонке поставщиков удостоверений выберите **Регистрация с помощью идентификатора пользователя** или **Регистрация по электронной почте**.
- В политике регистрации выберите **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике в качестве утверждения приложения выберите утверждение **Отображаемое имя**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. Имена политик понадобятся вам позднее.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к сборке приложения.

## Загрузка кода и настройка проверки подлинности

Код для этого примера размещен на веб-сайте [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

Полный пример кода можно скачать [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу.

Приложение взаимодействует с Azure AD B2C, отправляя HTTP-запросы проверки подлинности и указывая политику, которую необходимо выполнить как часть запроса. В случае с веб-приложениями .NET можно использовать библиотеку OWIN от Майкрософт. С ее помощью можно отправлять запросы проверки подлинности по протоколу OpenID Connect, выполнять политики, управлять сеансом пользователя и решать другие задачи.

Сначала добавьте пакеты NuGet ПО промежуточного слоя OWIN в проект с помощью консоли диспетчера пакетов Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Затем откройте файл `web.config` в корне проекта и введите значения конфигурации приложения в разделе `<appSettings>`.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="[Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com]" />
    <add key="ida:ClientId" value="[Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Теперь добавьте класс запуска OWIN в проект с именем `Startup.cs`. Щелкните правой кнопкой мыши проект и выберите **Добавить** в контекстном меню, затем **Новый элемент**, после чего найдите OWIN. Измените объявление класса на `public partial class Startup`. Часть этого класса уже была реализована в другом файле. При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(...)`. В этом методе отправьте вызов в `ConfigureAuth(...)`, чтобы настроить проверку подлинности для приложения.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`. Параметры, указанные в `OpenIdConnectAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD. Также будет необходимо настроить проверку подлинности для файлов Cookie — промежуточный слой OpenID Connect использует файлы cookie для поддержки сеанса пользователя и выполнения других задач.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // The ACR claim is used to indicate which policy was executed
    public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
    public const string PolicyKey = "b2cpolicy";
    public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

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
                AuthenticationFailed = AuthenticationFailed,
                RedirectToIdentityProvider = OnRedirectToIdentityProvider,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
            // endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory.
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional. It is used to display the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);

    }
```

## Отправка запросов на проверку подлинности в Azure AD
Теперь приложение настроено на взаимодействие с Azure AD B2C с использованием протокола проверки подлинности OpenID Connect. OWIN полностью возьмет на себя выполнение процессов создания сообщений проверки подлинности, проверки маркеров из Azure AD и поддержки сеанса пользователя. Осталось инициировать все потоки пользователей.

Когда пользователь нажимает кнопку **Регистрация**, **Вход** или **Изменить профиль** в веб-приложении, в `Controllers\AccountController.cs` вызывается соответствующее действие. В каждом случае можно использовать встроенные методы OWIN для запуска нужной политики:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties by using the PolicyKey provided.

        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties (
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.SignInPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.SignUpPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.ProfilePolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

Кроме того, в контроллерах можно использовать пользовательский тег `PolicyAuthorize`. Он отвечает за выполнение определенных политик, если вход пользователя не выполнен. Откройте файл `Controllers\HomeController.cs` и добавьте тег `[PolicyAuthorize]` в контроллер утверждений. Замените пример политики на собственную политику входа в систему.

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed in the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

Для выхода пользователя из приложения также можно использовать OWIN. В `Controllers\AccountController.cs` добавьте:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenID Connect sign-out request by using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string>
            {
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

По умолчанию OWIN не будет отправлять в Azure AD политики, которые вы указали в `AuthenticationProperties`. Но вы можете изменять запросы, которые OWIN создает в уведомлении `RedirectToIdentityProvider`. Используйте это уведомление в `App_Start\Startup.Auth.cs`, чтобы извлечь из метаданных политики правильную конечную точку для каждой политики. Это обеспечивает отправку в Azure AD правильного запроса для каждой политики, выполняемой приложением.

```C#
// App_Start\Startup.Auth.cs

private async Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    PolicyConfigurationManager mgr = notification.Options.ConfigurationManager as PolicyConfigurationManager;
    if (notification.ProtocolMessage.RequestType == OpenIdConnectRequestType.LogoutRequest)
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseRevoke.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.EndSessionEndpoint;
    }
    else
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseChallenge.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.AuthorizationEndpoint;
    }
}
```

## Отображение сведений о пользователе
При проверке подлинности пользователей с помощью OpenID Connect служба Azure AD возвращает в приложение маркер идентификатора, который содержит **утверждения** о пользователе. Эти утверждения можно использовать для персонализации приложения.

Откройте файл `Controllers\HomeController.cs`. Для доступа к утверждениям о пользователе в своих контроллерах можно использовать объект субъекта безопасности `ClaimsPrincipal.Current`.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
	Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
	ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Вы можете открыть любое утверждение, которое ваше приложение получает таким же образом. Список всех получаемых приложением утверждений можно найти на странице **Утверждения**.

## Запуск примера приложения

Приложение готово к сбору и запуску. Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Выйдите и снова войдите под именем того же пользователя. Измените профиль пользователя. Выйдите и зарегистрируйтесь от имени другого пользователя. Обратите внимание на то, что информация на вкладке **Утверждения** соответствует сведениям, настроенным в политиках.

## Добавление поставщиков удостоверений социальных сетей

В настоящее время приложение поддерживает регистрацию и вход пользователей только с использованием **локальных учетных записей**. Учетные записи хранятся в каталоге B2C, где применяется имя пользователя и пароль. С помощью Azure AD B2C можно добавить поддержку для других **поставщиков удостоверений** (IdP), не изменяя код.

Чтобы добавить в приложение поставщиков удостоверений социальных сетей, следуйте подробным инструкциям, приведенным в указанных ниже статьях. Для каждого поставщика удостоверений, поддержку которого нужно добавить, необходимо зарегистрировать приложение в соответствующей системе и получить идентификатор клиента.

- [Настройка Facebook как поставщика удостоверений](active-directory-b2c-setup-fb-app.md)
- [Настройка Google как поставщика удостоверений](active-directory-b2c-setup-goog-app.md)
- [Настройка Amazon как поставщика удостоверений](active-directory-b2c-setup-amzn-app.md)
- [Настройка LinkedIn как поставщика удостоверений](active-directory-b2c-setup-li-app.md)

После добавления поставщиков удостоверений в каталог B2C необходимо внести изменения во все три указанные выше политики, указав в них эти новые поставщики, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md). Сохраните политики и перезапустите приложение. Добавленные поставщики удостоверений должны отобразиться в виде вариантов при входе и регистрации.

Вы можете свободно экспериментировать с политиками, например: добавлять или удалять поставщиков удостоверений, управлять утверждениями приложений или изменять атрибуты регистрации, а также наблюдать эффект в примере приложения. Эксперименты помогают увидеть связь между политиками, запросами на проверку подлинности и библиотекой OWIN.

Полный пример (без ваших значений конфигурации) можно загрузить в виде [ ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->