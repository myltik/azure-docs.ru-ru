---
title: включение файла
description: включение файла
services: active-directory
author: andretms
ms.service: active-directory
ms.topic: include
ms.date: 05/08/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 5d3af1800e18e3686e69d4a25131c68d3bdc805b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33949453"
---
## <a name="set-up-your-project"></a>Настройка проекта

В этом разделе описаны шаги по установке и настройке конвейера проверки подлинности через промежуточный слой OWIN в проекте ASP.NET с помощью OpenID Connect. 

> Предпочитаете скачать этот пример проекта Visual Studio? [Скачайте проект](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) и перейдите к [настройке](#register-your-application), чтобы настроить пример кода перед выполнением.

### <a name="create-your-aspnet-project"></a>Создание проекта ASP.NET

1. В Visual Studio выберите `File` > `New` > `Project`.
2. В разделе *Visual C#\Web* выберите `ASP.NET Web Application (.NET Framework)`.
3. Присвойте имя приложению и нажмите кнопку *ОК*.
4. Выберите `Empty` и установите флажок, чтобы добавить ссылки `MVC`.

## <a name="add-authentication-components"></a>Добавление компонентов проверки подлинности

1. В Visual Studio выберите `Tools` > `Nuget Package Manager` > `Package Manager Console`.
2. Добавьте *пакеты NuGet для промежуточного слоя OWIN*, введя следующие команды в окне консоли диспетчера пакетов:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>О библиотеках
>Библиотеки, приведенные выше, позволяют выполнять единый вход с помощью OpenID Connect через проверку подлинности на основе файлов cookie. После завершения проверки подлинности и отправки приложению маркера, представляющего пользователя, промежуточный слой OWIN создаст файл cookie сеанса. Затем браузер будет использовать этот файл cookie при последующих запросах, так что пользователю не нужно будет повторно вводить пароль и проходить дополнительную проверку.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Настройка конвейера для проверки подлинности
Приведенные ниже шаги позволяют создать класс запуска промежуточного слоя OWIN для настройки проверки подлинности OpenID Connect. Этот класс будет выполняться автоматически при запуске процесса IIS.

> [!TIP]
> Если проект не содержит файл `Startup.cs` в корневой папке, сделайте следующее:
> 1. Щелкните правой кнопкой мыши корневой каталог проекта: > `Add` > `New Item...` > `OWIN Startup class`.<br/>
> 2. Назовите класс `Startup.cs`.
>
>> Выберите класс запуска OWIN, а не стандартный класс C#. Если вы выбрали нужный класс, вы должны увидеть `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` над пространством имен.

1. Добавьте в `Startup.cs` ссылки *OWIN* и *Microsoft.IdentityModel*.

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Вставьте в класс запуска приведенный ниже код.

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Дополнительные сведения
> Параметры, указанные в *OpenIDConnectAuthenticationOptions*, будут служить координатами приложения для взаимодействия с Azure AD. Так как промежуточный слой OpenID Connect использует файлы cookie в фоновом режиме, вам также необходимо настроить проверку подлинности для этих файлов, как показано в коде выше. Значение *ValidateIssuer* сообщает OpenIdConnect не ограничивать доступ для определенной организации.
<!--end-collapse-->

