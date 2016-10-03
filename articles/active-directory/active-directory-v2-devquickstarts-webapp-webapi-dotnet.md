<properties
	pageTitle="Веб-приложение .NET для Azure AD версии 2.0 | Microsoft Azure"
	description="Как создать веб-приложение .NET MVC, вызывающее веб-службы, используя личные учетные записи Майкрософт, а также рабочие и учебные учетные записи для входа."
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

# Вызов веб-API из веб-приложения .NET

Конечная точка версии 2.0 позволяет быстро реализовать в веб-приложениях и веб-API аутентификацию с поддержкой личных учетных записей Майкрософт, а также рабочих и учебных учетных записей. Мы создадим веб-приложение MVC, которое поддерживает вход пользователей с помощью OpenID Connect с использованием ПО промежуточного слоя OWIN Майкрософт. Веб-приложение будет получать маркеры доступа OAuth 2.0 для веб-API, защищенного OAuth 2.0, который позволяет создавать, читать и удалять элементы "списка дел" данного пользователя.

> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

Этот учебник в основном посвящен применению ADAL для получения и использования маркеров доступа в веб-приложении, что полностью описано [здесь](active-directory-v2-flows.md#web-apps). Для начала вам может потребоваться изучить, как [добавить базовые возможности входа в веб-приложение](active-directory-v2-devquickstarts-dotnet-web.md) или как[правильно защитить веб-API](active-directory-v2-devquickstarts-dotnet-api.md).

## Скачивание примера кода

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Кроме того, можно [скачать завершенное приложение как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) или клонировать его.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните [эти подробные указания](active-directory-v2-app-registration.md). Не забудьте:

- запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.
- Создайте **секрет приложения** типа **Пароль** и скопируйте его.
- Добавьте **веб-платформу** для своего приложения.
- Введите правильный **универсальный код ресурса (URI) перенаправления**. URI перенаправления сообщает Azure AD, куда следует направлять ответы проверки подлинности. Значение по умолчанию в этом руководстве — `https://localhost:44326/`.


## Установка OWIN
Добавьте пакеты NuGet для ПО промежуточного слоя OWIN в проект `TodoList-WebApp` с помощью консоли диспетчера пакетов. Кроме прочего, ПО промежуточного слоя OWIN будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## Вход пользователя
Теперь настройте ПО промежуточного слоя OWIN для использования [протокола аутентификации OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow).

-	Откройте файл `web.config` в корне проекта `TodoList-WebApp` и введите значения конфигурации приложения в разделе `<appSettings>`.
    -	`ida:ClientId` — это **идентификатор приложения**, присвоенный приложению на портале регистрации.
	- `ida:ClientSecret`— это **секрет приложения**, созданный на портале регистрации.
    -	`ida:RedirectUri` — это **универсальный код ресурса (URI) перенаправления**, который вы указали на портале.
- Откройте файл `web.config` в корневом каталоге проекта `TodoList-Service` и замените `ida:Audience` на **идентификатор приложения**, указанный выше.


- Откройте файл `App_Start\Startup.Auth.cs` и добавьте операторы `using` для библиотек, указанных выше.
- В том же файле реализуйте метод `ConfigureAuth(...)`. Параметры, указанные в `OpenIDConnectAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD.

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
...
```

## Использование ADAL для получения маркеров доступа
В уведомлении `AuthorizationCodeReceived` мы будем использовать [OAuth 2.0 вместе с OpenID Connect](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow), чтобы использовать код авторизации маркера доступа для службы списка дел. ADAL может упростить этот процесс.

- Сначала установите предварительную версию ADAL:

```PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoList-WebApp -IncludePrerelease```
- Добавьте еще один оператор `using` в файл `App_Start\Startup.Auth.cs` для ADAL.
- Теперь добавьте новый метод `OnAuthorizationCodeReceived` обработчика событий. Этот обработчик будет обращаться к ADAL для получения маркера доступа к API списка дел и сохранять этот маркер в кэше маркеров ADAL для последующего использования.

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
		string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
		string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
		ClientCredential cred = new ClientCredential(clientId, clientSecret);

		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		var authContext = new Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(authority, new NaiveSessionCache(userObjectId));
		var authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), cred, new string[] { clientId });
}
...
```

- В веб-приложениях ADAL использует расширяемый кэш маркеров, который можно применять для хранения маркеров. В этом примере реализуется `NaiveSessionCache`, использующий хранилище сеансов HTTP для кэширования маркеров.

<!-- TODO: Token Cache article -->


## 4\. Вызов веб-API
Теперь настало время использовать маркер доступа, полученный на шаге 3. Откройте файл `Controllers\TodoListController.cs` веб-приложения, который выполняет все запросы CRUD к интерфейсу API списка дел.

- Здесь снова можно использовать ADAL маркеров доступа из кэша ADAL. Сначала добавьте оператор `using` для ADAL в этот файл.

    `using Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory;`

- В действии `Index` используйте метод `AcquireTokenSilentAsync` ADAL, чтобы получить маркер доступа, который может использоваться для чтения данных из службы списка дел.

```C#
...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser);
...
```

- Затем пример добавляет полученный маркер в запрос HTTP GET в качестве заголовка `Authorization`, который служба списка дел использует для проверки подлинности запроса.
- Если служба списка дел возвращает ответ `401 Unauthorized`, по какой-либо причине маркеры доступа в ADAL стали недействительными. В этом случае следует удалить все маркеры доступа из кэша ADAL и показать пользователю сообщение о том, что требуется снова войти в систему, после чего будет перезапущен поток получения маркера.

```C#
...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
		var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
		foreach (TokenCacheItem tci in todoTokens)
				authContext.TokenCache.DeleteItem(tci);

		return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
...
```

- Аналогично, если ADAL не удалось вернуть маркер доступа по какой-либо причине, следует сообщить пользователю о необходимости повторного входа. Для этого нужно всего лишь перехватить любой `AdalException`:

```C#
...
catch (AdalException ee)
{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
...
```

- Такой же вызов `AcquireTokenSilentAsync` реализован в действиях `Create` и `Delete`. В веб-приложениях этот метод ADAL можно использовать для получения маркеров доступа, когда они требуются в приложении. ADAL позаботится о получении, кэшировании и обновлении маркеров.

Наконец, постройте и запустите свое приложение! Выполните вход с учетной записью Майкрософт или учетной записью Azure AD и обратите внимание на то, как удостоверение пользователя отображается в верхней панели навигации. Добавьте и удалите несколько элементов из списка дел пользователя, чтобы увидеть защищенные с помощью OAuth 2.0 вызовы API в действии. Теперь у вас есть веб-приложение и веб-API, защищенные с помощью стандартных отраслевых протоколов, которые могут проверять подлинность пользователей с помощью личных, рабочих и учебных учетных записей.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).

## Дальнейшие действия

Дополнительные ресурсы:
- [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Тег StackOverflow "adal" >>](http://stackoverflow.com/questions/tagged/adal)

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0921_2016-->