<properties
   pageTitle="Защита серверного веб-API в мультитенантном приложении | Microsoft Azure"
   description="Защита серверного веб-API"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Защита серверного веб-API в мультитенантном приложении

Этот материал входит в [цикл статей]. К статьям прилагается полный [пример приложения].

Приложение [Tailspin Surveys] использует серверный веб-API для управления операциями CRUD в опросах. Например, когда пользователь щелкает "Мои опросы", веб-приложение отправляет HTTP-запрос в веб-API.

```
GET /users/{userId}/surveys
```

Веб-API возвращает объект JSON.

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

Веб-интерфейс API не допускает выполнение анонимных запросов, поэтому веб-приложение должно пройти проверку подлинности с помощью токенов носителя OAuth 2.

> [AZURE.NOTE] Это сценарий взаимодействия между серверами. Приложение не осуществляет AJAX-вызовы API из клиента браузера.

Доступны два основных подхода:

- Удостоверение делегированного пользователя. Веб-приложение выполняет проверку подлинности с помощью удостоверения пользователя.
- Удостоверение приложения. Веб-приложение выполняет проверку подлинности с помощью идентификатора клиента, используя поток учетных данных клиента OAuth2.

Приложение Tailspin реализует удостоверение делегированного пользователя. Их основные различия приведены ниже.

**Удостоверение делегированного пользователя**

- Токен носителя, отправляемый в веб-API, содержит удостоверение пользователя.
- Веб-API принимает решения об авторизации на основе удостоверения пользователя.
- Веб-приложение должно обрабатывать ошибки 403 ("Запрещено") из веб-API, если пользователь не авторизован для выполнения действия.
- Как правило, веб-приложение все еще принимает некоторые решения относительно авторизации, затрагивающие пользовательский интерфейс (например отображение или скрытие элементов пользовательского интерфейса).
- В рамках этого подхода веб-API может использоваться ненадежными клиентами, такими как приложение JavaScript или собственное клиентское приложение.

**Удостоверение приложения**

- Веб-API не получает сведения о пользователе.
- Веб-API не выполняет авторизацию на основе удостоверения пользователя. Все решения об авторизации принимает веб-приложение.  
- Веб-API нельзя использовать в ненадежном клиенте (в приложении JavaScript или собственном клиентском приложении).
- Этот подход проще реализовать, так как в веб-API отсутствует логика авторизации.

Независимо от подхода веб-приложение должно получить маркер доступа, представляющий учетные данные, необходимые для вызова веб-API.

- В случае с удостоверением делегированного пользователя маркер должен поступать от поставщика удостоверений, который выдает маркер от имени пользователя.

- В случае с учетными данными клиента приложение может получить маркер от поставщика удостоверений или разместить собственный сервер маркеров. (Не нужно создавать сервер маркеров с нуля —используйте протестированную платформу, например [IdentityServer3].) При проверке подлинности в Azure AD настоятельно рекомендуется получить маркер доступа из Azure AD, даже если используется поток учетных данных клиента.

В оставшейся части этой статьи предполагается, что приложение проходит проверку подлинности в Azure AD.

![Получение маркера доступа](media/guidance-multitenant-identity/access-token.png)

## Регистрация веб-API в Azure AD

Чтобы получить токен носителя для веб-API из Azure AD, необходимо выполнять ряд настроек в Azure AD.

1. [Зарегистрируйте веб-API в Azure AD].

2. Добавьте идентификатор клиента веб-приложения в манифест приложения веб-API в свойство `knownClientApplications`. См. статью [Обновление манифестов приложений].

3. [Предоставьте веб-приложению право на вызов веб-API].

  На портале управления Azure можно задать два типа разрешений: "Разрешения приложения" для удостоверения приложения (поток учетных данных клиента) или "Делегированные разрешения" для удостоверения делегированного пользователя.

  ![Делегированные разрешения](media/guidance-multitenant-identity/delegated-permissions.png)

## Получение маркера доступа

Перед вызовом веб-API веб-приложение получает маркер доступа из Azure AD. В приложении .NET нужно использовать [библиотеку ADAL для .NET][ADAL].

В потоке кода авторизации OAuth 2 приложение обменивается кодом авторизации для маркера доступа. Для получения маркера доступа приведенный ниже код использует ADAL. Этот код вызывается во время события `AuthorizationCodeReceived`.

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

Ниже указаны различные необходимые параметры.

- `authority`. Производный от идентификатора клиента выполнившего вход пользователя (не идентификатор клиента поставщика SaaS).  
- `authorizationCode`. Код проверки подлинности, полученный от поставщика удостоверений.
- `clientId`. Идентификатор клиента веб-приложения.
- `clientSecret`. Секрет клиента веб-приложения.
- `redirectUri`. URI перенаправления, заданный для OpenID. Здесь поставщик удостоверений выполняет обратный вызов маркера.
- `resourceID`. URI идентификатора приложения для веб-API, который был создан при регистрации веб-API в Azure AD.
- `tokenCache`. Объект, который кэширует маркеры доступа. См. статью [Кэширование маркеров].

Если `AcquireTokenByAuthorizationCodeAsync` завершается успешно, ADAL кэширует маркер. Чтобы получить маркер из кэша позднее, вызовите AcquireTokenSilentAsync:

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

где `userId` является идентификатором объекта пользователя, который находится в утверждении `http://schemas.microsoft.com/identity/claims/objectidentifier`.

## Использование маркера доступа для вызова веб-API

Получив маркер, отправьте его в веб-API в заголовке авторизации HTTP-запроса.

```
Authorization: Bearer xxxxxxxxxx
```

Приведенный далее метод расширения из приложения Surveys задает заголовок авторизации в HTTP-запросе с помощью класса **HttpClient**.

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] См. [HttpClientExtensions.cs].

## Проверка подлинности в веб-API

Веб-API должен проверить подлинность токена носителя. В ASP.NET Core 1.0 можно использовать пакет [Microsoft.AspNet.Authentication.JwtBearer][JwtBearer]. Этот пакет содержит ПО промежуточного слоя, который позволяет приложению получать токены носителя OpenID Connect.

Зарегистрируйте ПО промежуточного слоя в классе `Startup` веб-API.

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] См. [Startup.cs].

- **Audience**. Задайте в качестве значения URL-адрес идентификатора приложения для веб-API, который был создан при регистрации веб-API в Azure AD.
- **Authority**. В мультитенантном приложении задайте этому параметру значение https://login.microsoftonline.com/common/``.
- **TokenValidationParameters**. В мультитенантном приложении задайте параметру **ValidateIssuer** значение "false". Это означает, что приложение будет проверять издателя.
- **Events** — это класс, производный от **JwtBearerEvents**.

### Проверка издателя

Издатель маркера проверяется в событии **JwtBearerEvents.ValidatedToken**. Издатель отправляется в утверждении "iss".

В приложении Surveys веб-API не обрабатывает [регистрацию клиента]. Таким образом, он только проверяет, находится ли издатель в базе данных приложения. Если издатель отсутствует, создается исключение, которое приводит к ошибке проверки подлинности.

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] См. [SurveysJwtBearerEvents.cs].

Можно также использовать событие **ValidatedToken** для [преобразования утверждений]. Следует помнить, что утверждения поступают непосредственно из Azure AD, поэтому преобразования утверждений, выполненные в веб-приложении, не отражаются в токене носителя, получаемом веб-API.

## Авторизация

Общие сведения об авторизации см. в статье [Авторизация]. Основное отличие для веб-API заключается в том, что обработку ответов авторизации выполняет ПО промежуточного слоя JwtBearer.

Например, разрешите доступ к действию контроллера пользователям, прошедшим проверку подлинности:

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```


Возвращает код состояния 401, если пользователь не прошел проверку подлинности.

Ограничьте действие контроллера по политике авторизации:

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

Возвращает код состояния 401, если пользователь не прошел проверку подлинности, и 403, если пользователь прошел проверку подлинности, но не авторизован. Зарегистрируйте политику при запуске:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```


## Дополнительные ресурсы

- [Сценарии аутентификации в Azure Active Directory][auth-scenarios]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[auth-scenarios]: ../active-directory/active-directory-authentication-scenarios.md/#web-application-to-web-api
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[цикл статей]: guidance-multitenant-identity.md
[Tailspin Surveys]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Зарегистрируйте веб-API в Azure AD]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[Обновление манифестов приложений]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Предоставьте веб-приложению право на вызов веб-API]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[Кэширование маркеров]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[регистрацию клиента]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[преобразования утверждений]: guidance-multitenant-identity-claims.md#claims-transformations
[Авторизация]: guidance-multitenant-identity-authorize.md
[пример приложения]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->