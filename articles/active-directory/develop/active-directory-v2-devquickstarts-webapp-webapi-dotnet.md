---
title: "Начало работы с веб-приложением Azure AD v2.0 .NET с поддержкой вызова API | Документация Майкрософт"
description: "Как создать веб-приложение .NET MVC, вызывающее веб-службы, используя личные учетные записи Майкрософт, а также рабочие и учебные учетные записи для входа."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 9cd676554542e4effef54790bf9095c5b7a8f75b
ms.openlocfilehash: c6cca8fda4ba6a26d5ee74b43e0bae37639865c5
ms.contentlocale: ru-ru
ms.lasthandoff: 02/03/2017


---
# <a name="calling-a-web-api-from-a-net-web-app"></a>Вызов веб-API из веб-приложения .NET
Конечная точка версии 2.0 позволяет быстро реализовать в веб-приложениях и веб-API аутентификацию с поддержкой личных учетных записей Майкрософт, а также рабочих и учебных учетных записей.  Мы создадим веб-приложение MVC, которое поддерживает вход пользователей с помощью OpenID Connect с использованием ПО промежуточного слоя OWIN Майкрософт.  Веб-приложение будет получать маркеры доступа OAuth 2.0 для веб-API, защищенного OAuth 2.0, который позволяет создавать, читать и удалять элементы "списка дел" данного пользователя.

Этот учебник в основном посвящен применению MSAL для получения и использования маркеров доступа в веб-приложении, что полностью описано [здесь](active-directory-v2-flows.md#web-apps).  Для начала вам может потребоваться изучить, как [добавить базовые возможности входа в веб-приложение](active-directory-v2-devquickstarts-dotnet-web.md) или как [правильно защитить веб-API](active-directory-v2-devquickstarts-dotnet-api.md).

> [!NOTE]
> Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0.  Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-sample-code"></a>Скачивание примера кода
Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Кроме того, можно [скачать завершенное приложение как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) или клонировать его.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или выполните [эти подробные указания](active-directory-v2-app-registration.md).  Не забудьте:

* Запишите назначенный вашему приложению **идентификатор приложения**. Он вскоре вам понадобится.
* Создайте **секрет приложения** типа **Пароль** и скопируйте его для дальнейшего использования.
* Добавьте **веб-платформу** для своего приложения.
* Введите правильный **универсальный код ресурса (URI) перенаправления**. URI перенаправления сообщает Azure AD, куда следует направлять ответы проверки подлинности. Значение по умолчанию в этом руководстве — `https://localhost:44326/`.

## <a name="install-owin"></a>Установка OWIN
Добавьте пакеты NuGet для ПО промежуточного слоя OWIN в проект `TodoList-WebApp` с помощью консоли диспетчера пакетов.  Кроме прочего, ПО промежуточного слоя OWIN будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Вход пользователя
Теперь настройте ПО промежуточного слоя OWIN для использования [протокола аутентификации OpenID Connect](active-directory-v2-protocols.md).  

* Откройте файл `web.config` в корне проекта `TodoList-WebApp` и введите значения конфигурации приложения в разделе `<appSettings>`.
  * `ida:ClientId` — это **идентификатор приложения** , присвоенный приложению на портале регистрации.
  * `ida:ClientSecret` — это **секрет приложения** , созданный на портале регистрации.
  * `ida:RedirectUri` — это **универсальный код ресурса (URI) перенаправления** , который вы указали на портале.
* Откройте файл `web.config` в корневом каталоге проекта `TodoList-Service` и замените `ida:Audience` **идентификатором приложения**, указанным выше.
* Откройте файл `App_Start\Startup.Auth.cs` и добавьте операторы `using` для библиотек, указанных выше.
* В том же файле реализуйте метод `ConfigureAuth(...)` .  Параметры, указанные в `OpenIDConnectAuthenticationOptions` , будут служить координатами приложения для взаимодействия с Azure AD.

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
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Использование MSAL для получения маркеров доступа
В уведомлении `AuthorizationCodeReceived` мы будем использовать [OAuth 2.0 вместе с OpenID Connect](active-directory-v2-protocols.md), чтобы использовать код авторизации маркера доступа для службы списка дел.  MSAL может упростить этот процесс.

* Сначала установите предварительную версию MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* Добавьте еще один оператор `using` в файл `App_Start\Startup.Auth.cs` для MSAL.
* Теперь добавьте новый метод `OnAuthorizationCodeReceived` обработчика событий.  Этот обработчик обращается к MSAL для получения маркера доступа к API списка дел и сохраняет этот маркер в кэше маркеров MSAL для последующего использования.

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* В веб-приложениях MSAL использует расширяемый кэш маркеров, который можно применять для хранения маркеров.  В этом примере реализуется `NaiveSessionCache` , использующий хранилище сеансов HTTP для кэширования маркеров.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Вызов веб-API
Теперь настало время использовать маркер доступа, полученный на шаге 3.  Откройте файл `Controllers\TodoListController.cs` веб-приложения, который выполняет все запросы CRUD к интерфейсу API списка дел.

* Здесь снова можно использовать MSAL маркеров доступа из кэша MSAL.  Сначала добавьте оператор `using` для MSAL в этот файл.
  
    `using Microsoft.Identity.Client;`
* В действии `Index` используйте метод `AcquireTokenSilentAsync` MSAL, чтобы получить маркер доступа, который может использоваться для чтения данных из службы списка дел.

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* Затем пример добавляет полученный маркер в запрос HTTP GET в качестве заголовка `Authorization`, который служба списка дел использует для проверки подлинности запроса.
* Если служба списка дел возвращает ответ `401 Unauthorized`, это значит, что по какой-то причине маркеры доступа в MSAL стали недействительными.  В этом случае следует удалить все маркеры доступа из кэша MSAL и показать пользователю сообщение о том, что требуется снова войти в систему. После этого входа перезапускается поток получения маркера.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* Аналогично, если MSAL не удалось вернуть маркер доступа по какой-либо причине, следует сообщить пользователю о необходимости повторного входа.  Для этого нужно всего лишь перехватить любой `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* Такой же вызов `AcquireTokenSilentAsync` реализован в действиях `Create` и `Delete`.  В веб-приложениях этот метод MSAL можно использовать для получения маркеров доступа, когда они требуются в приложении.  MSAL получает, кэширует и обновляет маркеры для вас.

Наконец, постройте и запустите свое приложение!  Выполните вход с учетной записью Майкрософт или учетной записью Azure AD и обратите внимание на то, как удостоверение пользователя отображается в верхней панели навигации.  Добавьте и удалите несколько элементов из списка дел пользователя, чтобы увидеть защищенные с помощью OAuth 2.0 вызовы API в действии.  Теперь у вас есть веб-приложение и веб-API, защищенные с помощью стандартных отраслевых протоколов, которые могут проверять подлинность пользователей с помощью личных, рабочих и учебных учетных записей.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные ресурсы:

* [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
* [Тег StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Получение обновлений системы безопасности для наших продуктов
Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).


