<properties
	pageTitle="Приступая к работе с Azure AD для .NET | Microsoft Azure"
	description="Как построить веб-приложение .NET MVC, которое интегрируется с Azure AD для входа."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Вход и выход веб-приложения с использованием Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD позволяет простым и удобным образом выполнять функции управления удостоверением вашего веб-приложения, обеспечивая единый вход и выход с помощью всего лишь нескольких строк кода. В веб-приложениях Asp.NET это можно делать с помощью реализации общинного промежуточного слоя OWIN корпорации Microsoft, включенного в .NET Framework 4.5. Мы будем использовать OWIN для следующего.
-	Вход пользователя в приложение с использованием Azure AD как поставщика удостоверений.
-	Отображение некоторой информации о пользователе.
-	Выход пользователя из приложения.

Для этого необходимо:

1. зарегистрировать приложение в Azure AD;
2. Настроить приложение для использования конвейера проверки подлинности OWIN.
3. использовать OWIN для выдачи запросов входа и выхода в Azure AD;
4. распечатать данные о пользователе.

Чтобы начать работу, [загрузите схему приложения](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) или [загрузите готовый пример](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip). Вам также потребуется клиент Azure AD для регистрации приложения. Если у вас нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## *1. Регистрация приложения с помощью Azure AD*
Чтобы ваше приложение могло осуществлять проверку подлинности пользователей, сначала необходимо зарегистрировать новое приложение в своем клиенте.

- Выполните вход на портале управления Azure.
- В левой панели навигации нажмите **Active Directory**.
- Выберите клиент, в котором нужно зарегистрировать приложение.
- Перейдите во вкладку **Приложения** и нажмите кнопку Добавить в нижней панели.
- Следуйте инструкциям на экране, а затем создайте новое **веб-приложение и/или WebAPI**.
    - **Имя** приложения отображает его описание конечным пользователям.
    -	**URL-адрес входа** — это базовый URL-адрес вашего приложения. Схема по умолчанию — `https://localhost:44320/`.
    - **URI идентификатора приложения** — это уникальный идентификатор вашего приложения. Соглашение заключается в использовании `https://<tenant-domain>/<app-name>`, например, `https://contoso.onmicrosoft.com/my-first-aad-app`
- После завершения регистрации Azure AD присваивает приложению уникальный идентификатор клиента. Это значение понадобится в следующих разделах, поэтому его стоит скопировать из вкладки «Настройка».

## *2. Установка приложения для использования проверки подлинности OWIN*
Здесь мы настроим промежуточный слой OWIN для использования протокола проверки подлинности OpenID Connect. Кроме всего прочего, OWIN будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

-	Чтобы начать, добавьте пакеты NuGet промежуточного слоя OWIN в проект с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	Добавьте класс запуска OWIN в проект под названием `Startup.cs` Щелкните проект правой кнопкой мыши--> **Добавить** --> **Новый элемент** --> Поиск «OWIN». При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(...)`.
-	Замените объявление класса на `public partial class Startup` — часть этого класса уже была реализована в другом файле. В методе `Configuration(...)` отправьте вызов в ConfgureAuth(...), чтобы настроить проверку подлинности для веб-приложения.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`. Параметры, указанные в `OpenIDConnectAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD. Также будет необходимо настроить проверку подлинности для файлов Cookie — промежуточный слой OpenID Connect использует файлы cookie под обложками.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-	Наконец, откройте файл `web.config` в корне проекта, а затем введите значения конфигурации в разделе `<appSettings>`.
    -	Ваше приложение `ida:ClientId` — это Guid, скопированный с портала Azure на шаге 1.
    -	`ida:Tenant` — это имя вашего клиента Azure AD, например contoso.onmicrosoft.com.
    -	Ваш `ida:PostLogoutRedirectUri` указывает системе Azure AD, куда должен перенаправляться пользователь после успешного выполнения запроса выхода.

## *3. Использование OWIN для выдачи запросов входа и выхода в Azure AD*
Теперь приложение правильно настроено для взаимодействия с Azure AD с использованием протокола проверки подлинности OpenID Connect. OWIN полностью возьмет на себя выполнение процессов создания сообщений проверки подлинности, проверки маркеров из Azure AD и поддержки сеанса пользователя. Остается лишь предоставить пользователям возможность выполнять вход и выход.

- Вы можете использовать теги авторизации в своих контроллерах, чтобы обязать пользователя выполнять вход перед доступом к конкретной странице. Откройте `Controllers\HomeController.cs` и добавьте тег `[Authorize]` в контроллер «О программе».

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	Вы также можете использовать OWIN для выдачи запросов проверки подлинности прямо из своего кода. Откройте `Controllers\AccountController.cs`. В действиях SignIn() и SignOut() выдавайте соответственно запросы входа и выхода из OpenID Connect.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-	Теперь откройте `Views\Shared\_LoginPartial.cshtml`. Здесь вы будете показывать пользователю ссылки для входа и выхода из приложения, а также выводить имя пользователя.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## *4. Отображение сведений о пользователе*
При проверке подлинности пользователей с помощью OpenID Connect система Azure AD возвращает в приложение маркер id\_token, который содержит "утверждения" о пользователе. Эти утверждения можно использовать для настройки своего приложения:

- Откройте файл `Controllers\HomeController.cs`. Для доступа к утверждениям о пользователе в своих контроллерах можно использовать объект субъекта безопасности `ClaimsPrincipal.Current`.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Наконец, постройте и запустите свое приложение! Если вы еще этого не сделали, создайте нового пользователя в своем клиенте с доменом *.onmicrosoft.com. Выполните вход под именем этого пользователя и обратите внимание на то, как удостоверение пользователя появляется в верхней панели навигации. Выйдите и снова войдите от имени другого пользователя в своем клиенте. Если у вас серьезные цели, зарегистрируйте и запустите другой экземпляр данного приложения (с его собственным clientId) и рассмотрите процесс единого входа в действии.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).

Теперь можно перейти к более сложным темам. Можно попробовать:

[Защита веб-интерфейса API с помощью Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->