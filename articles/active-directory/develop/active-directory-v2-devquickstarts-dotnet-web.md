---
title: "Начало работы с веб-приложением Azure AD v2.0 .NET с поддержкой входа | Документация Майкрософт"
description: "Как создать веб-приложение .NET MVC, которое поддерживает вход пользователей в систему с помощью личной, рабочей и учебной учетных записей Майкрософт."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a23b3b1084cf6776cee8583891ae3d879183d072
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Добавление функции входа в веб-приложение .NET MVC
Конечная точка версии 2.0 позволяет быстро реализовать аутентификацию в веб-приложениях с поддержкой личных учетных записей Майкрософт, а также рабочих или учебных учетных записей.  В веб-приложениях ASP.NET это можно делать с помощью ПО промежуточного уровня OWIN корпорации Microsoft, включенного в .NET Framework 4.5.

> [!NOTE]
> Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0.  Чтобы определить, следует ли вам использовать конечную точку 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
>
>

 Здесь мы создадим веб-приложение, использующее OWIN для выполнения входа пользователя, отображения информации о нем и выполнения выхода пользователя из приложения.

## <a name="download"></a>Загрузка
Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

Готовое приложение также приводится в конце этого руководства.

## <a name="register-an-app"></a>регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или выполните [эти подробные указания](active-directory-v2-app-registration.md).  Не забудьте:

* Запишите назначенный вашему приложению **идентификатор приложения**. Он вскоре вам понадобится.
* Добавьте **веб-платформу** для своего приложения.
* Введите правильный **универсальный код ресурса (URI) перенаправления**. URI перенаправления сообщает Azure AD, куда следует направлять ответы проверки подлинности. Значение по умолчанию в этом руководстве — `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Установка и настройка аутентификации OWIN
Здесь мы настроим промежуточный слой OWIN для использования протокола проверки подлинности OpenID Connect.  Кроме всего прочего, OWIN будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

1. Сначала откройте файл `web.config` в корневом каталоге проекта, а затем укажите параметры конфигурации приложения в разделе `<appSettings>`.

  * `ida:ClientId` — это **идентификатор приложения** , присвоенный приложению на портале регистрации.
  * `ida:RedirectUri` — это **универсальный код ресурса (URI) перенаправления** , который вы указали на портале.

2. Затем с помощью консоли диспетчера пакетов добавьте в проект пакеты NuGet ПО промежуточного слоя OWIN.

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. Добавьте класс запуска OWIN в проект с именем `Startup.cs`. Щелкните проект правой кнопкой мыши, выберите пункты **Добавить** --> **Новый элемент** и найдите "OWIN".  При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(...)` .
4. Замените объявление класса на `public partial class Startup` — часть этого класса уже была реализована в другом файле.  В методе `Configuration(...)` вызовите метод ConfgureAuth(...), чтобы настроить аутентификацию для веб-приложения.  

        ```csharp
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`.  Параметры, указанные в `OpenIdConnectAuthenticationOptions` , будут служить координатами приложения для взаимодействия с Azure AD.  Также будет необходимо настроить проверку подлинности для файлов Cookie — промежуточный слой OpenID Connect использует файлы cookie под обложками.

        ```csharp
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
                                             Scope = "openid email profile",
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

## <a name="send-authentication-requests"></a>Отправка запросов проверки подлинности
Теперь приложение правильно настроено для взаимодействия с конечной точки версии 2.0 с использованием протокола проверки подлинности OpenID Connect.  OWIN полностью возьмет на себя выполнение процессов создания сообщений проверки подлинности, проверки маркеров из Azure AD и поддержки сеанса пользователя.  Остается лишь предоставить пользователям возможность выполнять вход и выход.

- Вы можете использовать теги авторизации в своих контроллерах, чтобы обязать пользователя выполнять вход перед доступом к конкретной странице.  Откройте `Controllers\HomeController.cs` и добавьте тег `[Authorize]` в контроллер «О программе».
        
        ```csharp
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- Вы также можете использовать OWIN для выдачи запросов проверки подлинности прямо из своего кода.  Откройте `Controllers\AccountController.cs`.  В действиях SignIn() и SignOut() выдавайте соответственно запросы входа и выхода из OpenID Connect.

        ```csharp
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

- Теперь откройте `Views\Shared\_LoginPartial.cshtml`.  Здесь вы будете показывать пользователю ссылки для входа и выхода из приложения, а также выводить имя пользователя.

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

## <a name="display-user-information"></a>Отображение сведений о пользователе
При проверке подлинности пользователей с помощью OpenID Connect конечная точка версии 2.0 возвращает в приложение маркер идентификатора, который содержит утверждения о пользователе.  Эти утверждения можно использовать для настройки своего приложения:

- Откройте файл `Controllers\HomeController.cs` .  Для доступа к утверждениям о пользователе в своих контроллерах можно использовать объект субъекта безопасности `ClaimsPrincipal.Current` .

        ```csharp
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

## <a name="run"></a>Выполнить
Наконец, постройте и запустите свое приложение!   Выполните вход с личной учетной записью Майкрософт, рабочей или учебной учетной записью и обратите внимание на то, как удостоверение пользователя отображается в верхней панели навигации.  Теперь у вас есть веб-приложение, защищенное с помощью стандартных отраслевых протоколов, которое может проверять подлинность пользователей с помощью личных, рабочих и учебных учетных записей.

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)или клонировать с портала GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Дополнительная информация
Теперь можно перейти к более сложным темам.  Можно попробовать:

[Защита веб-API с помощью конечной точки версии 2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Дополнительные ресурсы:

* [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
* [Тег StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Получение обновлений системы безопасности для наших продуктов
Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).
