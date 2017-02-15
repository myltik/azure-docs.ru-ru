---
title: "Azure Active Directory B2C | Документация Майкрософт"
description: "Инструкции по созданию веб-приложений, позволяющих пользователям выполнять вход, регистрироваться и управлять профилем, с помощью Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 0b1c6ede-de0a-4c32-92b3-5c813dc26804
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: a0e03bda81d26dbc28706b45ee974359337573f0


---
# <a name="azure-ad-b2c-build-a-net-web-app"></a>Azure AD B2C: создание веб-приложения API .NET
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C позволяет добавлять в веб-приложения мощные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье описывается, как создать веб-приложение MVC для .NET, которое предусматривает регистрацию и вход пользователя, а также управление профилем. Приложение будет поддерживать регистрацию и вход в систему по имени пользователя или адресу электронной почты, а также учетной записи в социальной сети, такой как Facebook или Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C
Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д.  Прежде чем продолжать работу с руководством, [создайте каталог B2C](active-directory-b2c-get-started.md), если вы его еще не создали.

## <a name="create-an-application"></a>Создание приложения
Затем необходимо создать приложение в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md).  Не забудьте сделать следующее.

* Включите в приложение **веб-приложение или веб-API** .
* Укажите `https://localhost:44316/` в качестве **URI перенаправления**. Это URL-адрес по умолчанию для данного примера кода.
* Скопируйте **идентификатор приложения** , назначенный приложению.  Оно понадобится вам позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Создание политик
В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Этот пример кода включает три способа идентификации: регистрацию, вход в систему и изменение профиля. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#create-a-sign-up-policy).

> [!NOTE]
> Azure AD B2C также поддерживает объединенную политику регистрации или входа в систему, не представленную в этом учебнике.  Политика регистрации или входа в систему описана в [этом учебнике](active-directory-b2c-devquickstarts-web-dotnet-susi.md).
>
>

При создании трех необходимых политик обязательно сделайте следующее:

* В колонке поставщиков удостоверений выберите элемент **User ID sign-up** (Регистрация с помощью идентификатора пользователя) или **Email sign-up** (Регистрация по электронной почте).
* В политике регистрации укажите **отображаемое имя** и другие атрибуты регистрации.
* В каждой политике в качестве утверждения приложения выберите утверждение **Отображаемое имя** . Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой политики после ее создания. Имена политик понадобятся вам позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к сборке приложения.  

## <a name="download-the-code-and-configure-authentication"></a>Загрузка кода и настройка проверки подлинности
Код для этого примера размещен на веб-сайте [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Чтобы выполнить сборку примера, [скачайте схему проекта в виде ZIP-файла](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

Полный пример кода можно скачать [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) или получить из ветви `complete` того же репозитория.

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу.

Приложение взаимодействует с Azure AD B2C, отправляя сообщения проверки подлинности. В этих сообщениях указана политика, которую необходимо выполнить как часть HTTP-запроса. В случае с веб-приложениями .NET можно использовать ПО промежуточного слоя OWIN Майкрософт. С его помощью можно отправлять запросы проверки подлинности по протоколу OpenID Connect, выполнять политики, управлять сеансом пользователя и решать другие задачи.

Сначала добавьте пакеты NuGet ПО промежуточного слоя OWIN в проект с помощью консоли диспетчера пакетов Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Затем откройте файл `web.config` в корне проекта и введите значения конфигурации приложения в разделе `<appSettings>`, заменив существующие значения собственными.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
...
```

[!INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Теперь добавьте класс запуска OWIN в проект с именем `Startup.cs`. Щелкните проект правой кнопкой мыши и выберите **Добавить** и **Новый элемент**, затем выполните поиск по запросу "OWIN". **Не забудьте заменить объявление класса `public partial class Startup`**. Часть этого класса уже была реализована в другом файле. При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(...)` . В этом методе отправьте вызов в `ConfigureAuth(...)`, чтобы настроить проверку подлинности для приложения.

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

Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`.  Параметры, указанные в `OpenIdConnectAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD. Также будет необходимо настроить проверку подлинности для файлов Cookie — промежуточный слой OpenID Connect использует файлы cookie для поддержки сеанса пользователя и выполнения других задач.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
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

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Отправка запросов на проверку подлинности в Azure AD
Теперь приложение настроено на взаимодействие с Azure AD B2C с использованием протокола проверки подлинности OpenID Connect.  OWIN полностью возьмет на себя выполнение процессов создания сообщений проверки подлинности, проверки маркеров из Azure AD и поддержки сеанса пользователя.  Осталось инициировать все потоки пользователей.

Когда пользователь нажимает кнопку **Регистрация**, **Вход** или **Изменить профиль** в веб-приложении, в `Controllers\AccountController.cs` вызывается соответствующее действие. В каждом случае можно использовать встроенные методы OWIN для запуска нужной политики:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

Кроме того, в контроллерах можно использовать тег `[Authorize]`. Он отвечает за выполнение определенных политик, если пользователь не вошел в приложение. Откройте файл `Controllers\HomeController.cs` и добавьте тег `[Authorize]` в контроллер утверждений.  OWIN выберет последнюю настроенную политику при вызове тега авторизации.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Для выхода пользователя из приложения также можно использовать OWIN. В `Controllers\AccountController.cs`добавьте:  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Отображение сведений о пользователе
При проверке подлинности пользователей с помощью OpenID Connect служба Azure AD возвращает в приложение маркер идентификатора, который содержит **утверждения**. о пользователе. Эти утверждения можно использовать для персонализации приложения.  

Откройте файл `Controllers\HomeController.cs` . Для доступа к утверждениям о пользователе в своих контроллерах можно использовать объект субъекта безопасности `ClaimsPrincipal.Current` .

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Вы можете открыть любое утверждение, которое ваше приложение получает таким же образом.  Список всех получаемых приложением утверждений можно найти на странице **Утверждения** .

## <a name="run-the-sample-app"></a>Запуск примера приложения
Приложение готово к сбору и запуску. Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Выйдите и снова войдите под именем того же пользователя. Измените профиль пользователя. Выйдите и зарегистрируйтесь от имени другого пользователя. Обратите внимание, что информация на вкладке **Утверждения** соответствует сведениям, настроенным в политиках.

## <a name="add-social-idps"></a>Добавление поставщиков удостоверений социальных сетей
В настоящее время приложение поддерживает регистрацию и вход пользователей только с использованием **локальных учетных записей**. Учетные записи хранятся в каталоге B2C, где применяется имя пользователя и пароль. С помощью Azure AD B2C можно добавить поддержку для других **поставщиков удостоверений** (IDP), не изменяя код.

Чтобы добавить в приложение поставщиков удостоверений социальных сетей, следуйте подробным инструкциям, приведенным в указанных ниже статьях. Для каждого поставщика удостоверений, поддержку которого нужно добавить, необходимо зарегистрировать приложение в соответствующей системе и получить идентификатор клиента.

* [Настройка Facebook как поставщика удостоверений](active-directory-b2c-setup-fb-app.md)
* [Настройка Google как поставщика удостоверений](active-directory-b2c-setup-goog-app.md)
* [Настройка Amazon как поставщика удостоверений](active-directory-b2c-setup-amzn-app.md)
* [Настройка LinkedIn как поставщика удостоверений](active-directory-b2c-setup-li-app.md)

Добавив поставщики удостоверений в каталог B2C, внесите соответствующие изменения в три политики, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md). Сохраните политики и перезапустите приложение.  Добавленные поставщики удостоверений должны отобразиться в виде вариантов при входе и регистрации.

Вы можете свободно экспериментировать с политиками, например: добавлять или удалять поставщиков удостоверений, управлять утверждениями приложений или изменять атрибуты регистрации, а также наблюдать эффект в примере приложения. Эксперименты помогают увидеть связь между политиками, запросами на проверку подлинности и библиотекой OWIN.

Полный пример (без ваших значений конфигурации) можно загрузить в виде [ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->



<!--HONumber=Dec16_HO4-->


