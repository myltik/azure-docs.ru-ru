<properties
	pageTitle="Модель приложений версии 2.0: веб-приложение .NET | Microsoft Azure"
	description="Как создать веб-приложение .NET MVC, которое поддерживает вход пользователей в систему с помощью личной, рабочей и учебной учетных записей Майкрософт."
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
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# Предварительная версия модели приложений 2.0: реализация входа в веб-приложении .NET MVC

Модель приложений версии 2.0 позволяет быстро реализовать проверку подлинности для веб-приложения с поддержкой личных учетных записей Майкрософт, а также рабочих и учебных учетных записей. В веб-приложениях ASP.NET это можно делать с помощью ПО промежуточного уровня OWIN корпорации Microsoft, включенного в .NET Framework 4.5.

  >[AZURE.NOTE]
    Эти сведения относятся к общедоступной предварительной версии модели приложений 2.0. Инструкции по интеграции с общедоступной службой Azure AD см. в статье [Руководство разработчика Azure Active Directory](active-directory-developers-guide.md).

 Здесь мы будем использовать OWIN:
-	1) для входа пользователя в приложение с помощью Azure AD и модели приложений 2.0; 
-	2) отображения некоторых сведений о пользователе; 
-	3) выхода пользователя из приложения.

Для этого необходимо:

1. зарегистрировать приложение;
2. установить приложение для использования проверки подлинности OWIN;
3. использовать OWIN для выдачи запросов входа и выхода в Azure AD;
4. распечатать данные о пользователе.

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

Готовое приложение также приводится в конце этого руководства.

## 1. Регистрация приложения
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните следующие [действия](active-directory-v2-app-registration.md).  Не забудьте:

- скопировать **идентификатор приложения**, назначенный вашему приложению (он скоро вам понадобится);
- добавить **веб-платформу** для приложения;
- ввести правильный **URI перенаправления**.  URI перенаправления сообщает Azure AD, куда следует направлять ответы проверки подлинности. Значение по умолчанию для этого учебника — `https://localhost:44326/`.

## 2. установить приложение для использования проверки подлинности OWIN
Здесь мы настроим промежуточный слой OWIN для использования протокола проверки подлинности OpenID Connect.  Кроме всего прочего, OWIN будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

-	Для начала откройте файл `web.config` в корне проекта, а затем введите значения конфигурации приложения в разделе `<appSettings>`.
    -	`ida:ClientId`— это **идентификатор приложения**, назначенный приложению на портале регистрации.
    -	`ida:RedirectUri`— это **URI перенаправления**, который был введен на портале.

-    Затем с помощью консоли диспетчера пакетов добавьте в проект пакеты NuGet ПО промежуточного слоя OWIN.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect 
PM> Install-Package Microsoft.Owin.Security.Cookies 
PM> Install-Package Microsoft.Owin.Host.SystemWeb 
```

-	Добавьте "Стартовый класс OWIN" в проект с именем `Startup.cs`. Щелкните проект правой кнопкой мыши и последовательно выберите **Добавить**, **Новый элемент**. Выполните поиск "OWIN". При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(...)`.
-	Замените объявление класса на `public partial class Startup` — часть этого класса уже была реализована в другом файле. В методе `Configuration(...)` вызовите метод ConfgureAuth(...), чтобы настроить проверку подлинности для веб-приложения.  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`. Параметры, указанные в `OpenIdConnectAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD. Также будет необходимо настроить проверку подлинности для файлов Cookie — промежуточный слой OpenID Connect использует файлы cookie под обложками.

```C#
public void ConfigureAuth(IAppBuilder app)
			 {
					 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

					 app.UseCookieAuthentication(new CookieAuthenticationOptions());

					 app.UseOpenIdConnectAuthentication(
							 new OpenIdConnectAuthenticationOptions
							 {
									 // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
									 // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
									 // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

									 ClientId = clientId,
									 Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
									 RedirectUri = redirectUri,
									 Scope = "openid",
									 ResponseType = "id_token",
									 PostLogoutRedirectUri = redirectUri,
									 TokenValidationParameters = new TokenValidationParameters
									 {
											 ValidateIssuer = false,
									 },
									 Notifications = new OpenIdConnectAuthenticationNotifications
									 {
											 AuthenticationFailed = OnAuthenticationFailed,
									 }
							 });
			 }
```

## 3\. Использование OWIN для выдачи запросов входа и выхода в Azure AD
Теперь приложение правильно настроено для взаимодействия с конечной точки версии 2.0 с использованием протокола проверки подлинности OpenID Connect. OWIN полностью возьмет на себя выполнение процессов создания сообщений проверки подлинности, проверки маркеров из Azure AD и поддержки сеанса пользователя. Остается лишь предоставить пользователям возможность выполнять вход и выход.

- Вы можете использовать теги авторизации в своих контроллерах, чтобы обязать пользователя выполнять вход перед доступом к конкретной странице. Откройте `Controllers\HomeController.cs` и добавьте тег `[Authorize]` в контроллер "О программе".

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

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-	Теперь откройте `Views\Shared\_LoginPartial.cshtml`. Здесь вы будете показывать пользователю ссылки для входа и выхода из приложения, а также выводить имя пользователя.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## 4\. Отображение сведений о пользователе
При проверке подлинности пользователей с помощью OpenID Connect конечная точка версии 2.0 возвращает в приложение маркер идентификатора, который содержит [утверждения](active-directory-v2-tokens.md#id_tokens) о пользователе. Эти утверждения можно использовать для настройки своего приложения:

- Откройте файл `Controllers\HomeController.cs`. Для доступа к утверждениям о пользователе в своих контроллерах можно использовать объект субъекта безопасности `ClaimsPrincipal.Current`.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

Наконец, постройте и запустите свое приложение! Выполните вход с личной учетной записью Майкрософт, рабочей или учебной учетной записью и обратите внимание на то, как удостоверение пользователя отображается в верхней панели навигации. Теперь у вас есть веб-приложение, защищенное с помощью стандартных отраслевых протоколов, которое может проверять подлинность пользователей с помощью личных, рабочих и учебных учетных записей.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) в виде ZIP-файла находится [здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Также можно клонировать его с GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## Дальнейшие действия

Теперь можно перейти к более сложным темам. Можно попробовать:

[Защита веб-API с помощью модели приложения версии 2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Дополнительные ресурсы: 
- [Предварительная версия модели приложений 2.0 >>](active-directory-appmodel-v2-overview.md) 
- [Тег StackOverflow "azure active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=Oct15_HO1-->