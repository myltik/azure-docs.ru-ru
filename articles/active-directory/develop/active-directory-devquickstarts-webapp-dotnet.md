---
title: "Приступая к работе с Azure AD для веб-приложения .NET | Документация Майкрософт"
description: "Узнайте, как выполнить сборку веб-приложения .NET MVC, которое интегрируется с Azure AD для входа."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 43ba592b6294a9a75a20dacd81953a77c241b89f
ms.contentlocale: ru-ru
ms.lasthandoff: 03/18/2017


---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>Вход в веб-приложение ASP.NET и выход из него с помощью Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) позволяет легко выполнять функции управления удостоверением веб-приложения, обеспечивая единый вход и выход с помощью всего лишь нескольких строк кода. Пользователи могут входить в веб-приложения ASP.NET и выходить из них с помощью реализованного корпорацией Майкрософт ПО промежуточного слоя OWIN (Open Web Interface for .NET). Поддерживаемое сообществом ПО промежуточного слоя OWIN включено в .NET Framework 4.5. В этой статье показано, как с помощью OWIN:

* выполнять вход пользователей в веб-приложения с использованием Azure AD как поставщика удостоверений;
* отображать некоторые сведения о пользователе;
* выполнять выход пользователей из приложений.

## <a name="before-you-get-started"></a>Необходимые условия
* Скачайте [схему приложения](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) или [готовый пример](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).
* Вам также необходим клиент Azure AD для регистрации приложения. Если у вас еще нет клиента Azure AD, [узнайте, как его получить](active-directory-howto-tenant.md).

Когда будете готовы, выполните процедуры, описанные в следующих четырех разделах.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>Шаг 1. Регистрация нового приложения в Azure AD
Чтобы настроить приложение для аутентификации пользователей, сначала зарегистрируйте его в клиенте. Для этого выполните следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com).
2. На верхней панели щелкните имя учетной записи. В списке **Каталог** выберите клиент Active Directory для регистрации приложения.
3. В области слева щелкните **Больше служб** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.
5. Следуйте инструкциям на экране, чтобы создать **веб-приложение и (или) веб-API**.
  * **Имя** — это описание приложения для пользователей.
  * **URL-адрес входа** — это базовый URL-адрес приложения. Формат URL-адреса по умолчанию — https://localhost:44320/.
6. После завершения регистрации Azure AD присваивает приложению уникальный идентификатор. Скопируйте значение на странице приложения, чтобы использовать его в следующих разделах.
7. На странице **Параметры** -> **Свойства** приложения обновите его универсальный код ресурса (URI) идентификатора. **URI кода приложения** — это уникальный идентификатор приложения. Соглашение об именовании — `https://<tenant-domain>/<app-name>` (например, `https://contoso.onmicrosoft.com/my-first-aad-app`).

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Шаг 2. Настройка приложения для использования конвейера аутентификации OWIN
На этом шаге настраивается ПО промежуточного слоя OWIN для использования протокола проверки подлинности OpenID Connect. OWIN используется для выдачи запросов на вход и выход, управления сеансами пользователей, получения сведений о пользователях и т. д.

1. Чтобы начать, добавьте пакеты NuGet ПО промежуточного слоя OWIN в проект с помощью консоли диспетчера пакетов.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. Чтобы добавить класс запуска OWIN в проект с именем `Startup.cs`, щелкните этот проект правой кнопкой мыши, выберите **Добавить**, затем выберите **Новый элемент** и найдите **OWIN**. При запуске приложения ПО промежуточного слоя OWIN вызывает метод **Configuration(...)**.
3. Замените объявление класса `public partial class Startup`. Часть этого класса уже была реализована в другом файле. В методе **Configuration(...)** отправьте вызов в **ConfigureAuth(...)**, чтобы настроить аутентификацию для приложения.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. Откройте файл App_Start\Startup.Auth.cs, а затем реализуйте метод **ConfigureAuth(...)**. Параметры, указанные в *OpenIDConnectAuthenticationOptions*, будут служить координатами приложения для взаимодействия с Azure AD. Также необходимо настроить аутентификацию на основе файлов cookie, так как ПО промежуточного слоя OpenID Connect использует файлы cookie в фоновом режиме.

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
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. Откройте файл web.config в корне проекта, а затем введите значения конфигурации в разделе `<appSettings>`.
  * `ida:ClientId`: GUID, скопированный с портала Azure на шаге 1 (Регистрация нового приложения в Azure AD).
  * `ida:Tenant`: имя вашего клиента Azure AD (например, contoso.onmicrosoft.com).
  * `ida:PostLogoutRedirectUri`: индикатор, указывающий системе Azure AD, куда должен перенаправляться пользователь после успешного выполнения запроса выхода.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Шаг 3. Использование OWIN для выдачи запросов входа и выхода в Azure AD
Теперь приложение правильно настроено для взаимодействия с Azure AD с использованием протокола проверки подлинности OpenID Connect. OWIN полностью возьмет на себя выполнение процессов создания сообщений аутентификации, проверки маркеров из Azure AD и поддержки сеансов пользователя. Остается лишь предоставить пользователям возможность выполнять вход и выход.

1. Вы можете использовать теги авторизации в своих контроллерах, чтобы обязать пользователей выполнять вход перед доступом к конкретным страницам. Для этого откройте файл Controllers\HomeController.cs и добавьте тег `[Authorize]` в контроллер About.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. Вы также можете использовать OWIN для выдачи запросов проверки подлинности прямо из своего кода. Для этого откройте файл Controllers\AccountController.cs. Затем в действиях SignIn() и SignOut() выдавайте соответственно запросы входа и выхода из OpenID Connect.

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

3. Откройте файл Views\Shared\_LoginPartial.cshtml, чтобы показывать пользователю ссылки для входа и выхода из приложения, а также отображать имя пользователя.

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

## <a name="step-4-display-user-information"></a>Шаг 4. Отображение сведений о пользователе
При аутентификации пользователей с помощью OpenID Connect служба Azure AD возвращает в приложение маркер id_token, который содержит "утверждения" о пользователе. Эти утверждения можно использовать для настройки приложения. Для этого выполните следующие действия:

1. Откройте файл Controllers\HomeController.cs. Для доступа к утверждениям о пользователе в своих контроллерах можно использовать объект субъекта безопасности `ClaimsPrincipal.Current` .

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

2. Выполните сборку и запустите приложение. Если вы еще этого не сделали, то создайте пользователя в своем клиенте с доменом onmicrosoft.com. Этот процесс описывается далее.

  а. Выполните вход от имени этого пользователя и обратите внимание на то, как удостоверение пользователя отображается на верхней панели.

  b. Выйдите, а затем снова войдите от имени другого пользователя в своем клиенте.

  c. Если у вас серьезные цели, зарегистрируйте и запустите другой экземпляр данного приложения (с его собственным clientId) и рассмотрите процесс единого входа в действии.

## <a name="next-steps"></a>Дальнейшие действия
Для справки просмотрите [готовый пример](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (без ваших значений конфигурации).

Теперь можно перейти к более сложным темам. Например, попробуйте использовать [защиту веб-API с помощью Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

