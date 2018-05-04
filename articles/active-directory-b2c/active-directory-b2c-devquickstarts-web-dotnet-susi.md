---
title: Аутентификация, регистрация и сброс пароля в Azure Active Directory B2C для ASP.NET
description: Сведения по созданию веб-приложений, позволяющих пользователям выполнять вход, изменять профиль, регистрироваться и сбрасывать пароль с помощью Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.openlocfilehash: 052d81f8170273bb0e40572322349550e7219363
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Создание веб-приложения ASP.NET с возможностями регистрации, входа, редактирования профиля и сброса пароля Azure Active Directory B2C

В этом учебнике описаны следующие процедуры.

> [!div class="checklist"]
> * Добавление функций управления удостоверениями Azure AD B2C в веб-приложение
> * Регистрация веб-приложения в каталоге Azure AD B2C
> * Создание политики регистрации, входа, изменения профиля и сброса пароля пользователя для веб-приложения

## <a name="prerequisites"></a>предварительным требованиям

- Подключите клиент B2C к учетной записи Azure. Вы можете создать бесплатную учетную запись Azure [здесь](https://azure.microsoft.com/).
- Вам требуется [Microsoft Visual Studio](https://www.visualstudio.com/) или аналогичная программа для просмотра и изменения примера кода.

## <a name="create-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Прежде чем продолжать работу с руководством, создайте каталог B2C, если вы его еще не создали.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Необходимо подключить клиент B2C к подписке Azure. Выбрав **Создать**, выберите параметр **Связывание существующего B2C-клиента Azure AD с вашей подпиской Azure**, а затем в раскрывающемся списке **Клиент B2C Azure AD** выберите клиент, которого требуется связать.

## <a name="create-and-register-an-application"></a>Создание и регистрация приложения

Затем необходимо создать и зарегистрировать приложение в каталоге B2C. Так вы получаете информацию, необходимую Azure AD B2C для безопасного взаимодействия с вашим приложением. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

По окончании у вас будет API и собственное приложение в параметрах приложения.

## <a name="create-policies-on-your-b2c-tenant"></a>Создание политик в клиенте B2C

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Этот пример кода включает три способа идентификации: **регистрацию и вход в систему**, **изменение профиля**, а также **сброс пароля**.  Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md). Для каждой политики выберите атрибут или утверждение отображаемого имени, а также скопируйте имя политики для последующего использования.

### <a name="add-your-identity-providers"></a>Добавление поставщиков удостоверений

В разделе параметров щелкните **Поставщики удостоверений** и выберите вход с помощью имени пользователя или электронной почты.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Создание политики регистрации и входа в систему

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Создание политики изменения профиля

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Создание политики сброса паролей

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

После создания политик можно приступать к сборке приложения.

## <a name="download-the-sample-code"></a>Скачивание примера кода

Код примеров для этого руководства размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Вы можете клонировать пример, выполнив такую команду:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу. Теперь решение содержит два проекта: `TaskWebApp` и `TaskService`. `TaskWebApp` — это веб-приложение MVC, с которым взаимодействует пользователь. `TaskService` — веб-API серверной части приложения, в котором хранится список дел для каждого пользователя. В этой статье рассматривается только приложение `TaskWebApp`. Сведения о создании `TaskService` с помощью Azure AD B2C см. в [этом руководстве по веб-приложениям .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Обновление кода для использования клиента и политик

В нашем примере настроено использование политик и идентификатора демонстрационного клиента. Чтобы подключить его к своему клиенту, необходимо открыть файл `web.config` в проекте `TaskWebApp` и заменить следующие значения:

* `ida:Tenant` именем своего клиента;
* `ida:ClientId` идентификатором клиента для веб-приложения;
* `ida:ClientSecret` секретным ключом веб-приложения;
* `ida:SignUpSignInPolicyId` именем политики регистрации и входа в систему;
* `ida:EditProfilePolicyId` именем политики "Изменение профиля";
* `ida:ResetPasswordPolicyId` именем политики "Сброс профиля".

## <a name="launch-the-app"></a>Запуск приложения
Из среды Visual Studio запустите приложение. Перейдите на вкладку To-Do List (Список задач) и обратите внимание на URL-адрес: https://login.microsoftonline.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*&client_id=*YourclientID*.

Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Выйдите из системы, а затем войдите и измените профиль или сбросьте пароль. Выйдите и зарегистрируйтесь от имени другого пользователя. 

## <a name="add-social-idps"></a>Добавление поставщиков удостоверений социальных сетей

Сейчас приложение поддерживает регистрацию и вход пользователя с помощью **локальных учетных записей**, которые хранятся в каталоге B2C и используют имя пользователя и пароль. С помощью Azure AD B2C можно добавить поддержку для других **поставщиков удостоверений** (IDP), не изменяя код.

Чтобы добавить в приложение поставщиков удостоверений социальных сетей, следуйте подробным инструкциям, приведенным в указанных ниже статьях. Для каждого поставщика удостоверений, поддержку которого нужно добавить, необходимо зарегистрировать приложение в соответствующей системе и получить идентификатор клиента.

* [Настройка Facebook как поставщика удостоверений](active-directory-b2c-setup-fb-app.md)
* [Настройка Google как поставщика удостоверений](active-directory-b2c-setup-goog-app.md)
* [Настройка Amazon как поставщика удостоверений](active-directory-b2c-setup-amzn-app.md)
* [Настройка LinkedIn как поставщика удостоверений](active-directory-b2c-setup-li-app.md)

Добавив поставщики удостоверений в каталог B2C, внесите соответствующие изменения в три политики, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md). Сохраните политики и перезапустите приложение.  Добавленные поставщики удостоверений должны отобразиться в виде вариантов при входе и регистрации.

Вы можете свободно экспериментировать с политиками, например: добавлять или удалять поставщиков удостоверений, управлять утверждениями приложений или изменять атрибуты регистрации, а также наблюдать эффект в примере приложения. Эксперименты помогают увидеть связь между политиками, запросами на проверку подлинности и библиотекой OWIN.

## <a name="sample-code-walkthrough"></a>Пошаговое руководство по примеру кода
В следующих разделах показано, как настроить пример кода приложения. Вы можете использовать это руководство при разработке приложения в будущем.

### <a name="add-authentication-support"></a>Добавление поддержки проверки подлинности

Теперь можно настроить приложение для использования Azure AD B2C. Приложение взаимодействует с Azure AD B2C, отправляя запросы проверки подлинности по протоколу OpenID Connect. В запросах задана политика, регулирующая взаимодействие пользователя с приложением. Вы можете использовать библиотеку OWIN от Майкрософт. С ее помощью можно отправлять запросы, выполнять политики, управлять сеансом пользователя и решать другие задачи.

#### <a name="install-owin"></a>Установка OWIN

Сначала добавьте пакеты NuGet ПО промежуточного слоя OWIN в проект с помощью консоли диспетчера пакетов Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>Добавление класса запуска OWIN

Добавьте класс запуска OWIN в API с именем `Startup.cs`.  Щелкните проект правой кнопкой мыши и выберите **Добавить** и **Новый элемент**, после чего найдите OWIN. При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(…)` .

В нашем примере мы изменили объявление класса на `public partial class Startup` и реализовали другую часть класса в `App_Start\Startup.Auth.cs`. Внутри метода `Configuration` мы добавили вызов `ConfigureAuth`, определенный в `Startup.Auth.cs`. После внесения изменений `Startup.cs` выглядит следующим образом:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>Настройка ПО промежуточного слоя для проверки подлинности

Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`. Параметры, указанные в разделе `OpenIdConnectAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD B2C. Если не задать определенные параметры, будут использоваться значения по умолчанию. Например, в примере не был указан параметр `ResponseType`, поэтому для каждого исходящего запроса к Azure AD B2C будет использоваться значение по умолчанию `code id_token`.

Также будет необходимо настроить проверку подлинности для файлов Cookie — промежуточный слой OpenID Connect использует файлы cookie для поддержки сеанса пользователя и выполнения других задач.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

В приведенном выше разделе `OpenIdConnectAuthenticationOptions` мы определили набор функций обратного вызова для специальных уведомлений, получаемых с помощью ПО промежуточного слоя OpenID Connect. Эти поведения определяются с помощью объекта `OpenIdConnectAuthenticationNotifications` и хранятся в переменной `Notifications`. В этом примере мы определяем три различных обратных вызова в зависимости от события.

### <a name="using-different-policies"></a>Использование различных политик

При каждом запросе к Azure AD B2C инициируется уведомление `RedirectToIdentityProvider`. Если требуется использовать другую политику, в функции обратного вызова `OnRedirectToIdentityProvider` нужно проверять исходящий вызов. Чтобы сбросить пароль или изменить профиль, необходимо использовать соответствующую политику, например политику сброса паролей, вместо политики регистрации или входа, заданной по умолчанию.

В этом примере, если пользователь хочет сбросить пароль или изменить профиль, мы добавляем политику, которую рекомендуется использовать в контексте OWIN. Для этого необходимо сделать следующее:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

Вы можете реализовать функцию обратного вызова `OnRedirectToIdentityProvider`, выполнив следующую команду:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Обработка кодов авторизации

При получении кода авторизации инициируется уведомление `AuthorizationCodeReceived`. ПО промежуточного слоя OpenID Connect не поддерживает обмен кодов на маркеры доступа. Вы можете вручную выполнить обмен кода на маркер в функции обратного вызова. Дополнительные сведения см. в [этой документации](active-directory-b2c-devquickstarts-web-api-dotnet.md).

### <a name="handling-errors"></a>Обработка ошибок

При сбое проверки подлинности инициируется уведомление `AuthenticationFailed`. С помощью метода обратного вызова можно обрабатывать ошибки по своему усмотрению. Тем не менее необходимо добавить проверку кода ошибки `AADB2C90118`. Во время выполнения политики регистрации и входа пользователь может щелкнуть ссылку **Забыли пароль?**. В этом случае Azure AD B2C отправляет код ошибки, указывающий, что приложение должно выполнить запрос с помощью политики сброса пароля.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Отправка запросов на проверку подлинности в Azure AD

Теперь приложение настроено на взаимодействие с Azure AD B2C с использованием протокола проверки подлинности OpenID Connect. OWIN берет на себя создание сообщений аутентификации, проверку маркеров из Azure AD B2C и поддержку сеанса пользователя. Осталось инициировать все потоки пользователей.

Когда пользователь выбирает **Регистрация или Вход**, **Изменить профиль** или **Сброс пароля** в веб-приложении, в `Controllers\AccountController.cs` вызывается соответствующее действие:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

Для выхода пользователя из приложения также можно использовать OWIN. В `Controllers\AccountController.cs` у нас есть следующее:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Помимо явного вызова политики, можно использовать тег `[Authorize]` в контроллерах, что приведет к выполнению политики, если пользователь не выполнил вход. Откройте файл `Controllers\HomeController.cs` и добавьте тег `[Authorize]` в контроллер утверждений.  OWIN выберет последнюю настроенную политику при вызове тега `[Authorize]`.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Отображение сведений о пользователе

При проверке подлинности пользователей с помощью OpenID Connect служба Azure AD B2C возвращает в приложение маркер идентификатора, который содержит **утверждения**. о пользователе. Эти утверждения можно использовать для персонализации приложения.

Откройте файл `Controllers\HomeController.cs` . Для доступа к утверждениям о пользователе в своих контроллерах можно использовать объект субъекта безопасности `ClaimsPrincipal.Current` .

```CSharp
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
