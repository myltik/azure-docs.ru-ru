---
title: "Azure Active Directory B2C | Документация Майкрософт"
description: "Как создать веб-приложение .NET и обращаться к веб-API с использованием Azure Active Directory B2C и маркеров доступа OAuth 2.0."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: mtillman
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 42b2b698493408f11ee23f06f99d9ba22860746a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: вызов веб-API .NET из веб-приложения .NET

Azure AD B2C позволяет добавлять в веб-приложения и веб-API мощные функции для управления удостоверениями. В этой статье описывается запрос маркера доступа и вызов веб-API .NET из веб-приложения со списком дел.

В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Если это еще не сделано, необходимо выполнить следующие действия.

* Изучите начало работы с [веб-приложением .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Изучите начало работы с [веб-API .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Предварительные требования

Чтобы создать веб-приложение, которое вызывает веб-API, выполните следующие действия.

1. [Создайте клиента Azure AD B2C](active-directory-b2c-get-started.md).
2. [Зарегистрируйте веб-API](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Зарегистрируйте веб-приложение](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Настройте политики](active-directory-b2c-reference-policies.md).
5. [Предоставьте веб-приложению права для использования веб-API](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> Клиентское приложение и веб-API должны использовать один и тот же каталог Azure AD B2C.
>

## <a name="download-the-code"></a>Загрузка кода

Код примеров для этого руководства размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Вы можете клонировать пример, выполнив такую команду:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу. Теперь решение содержит два проекта: `TaskWebApp` и `TaskService`. `TaskWebApp` — это веб-приложение MVC, с которым взаимодействует пользователь. `TaskService` — веб-API серверной части приложения, в котором хранится список дел для каждого пользователя. В этой статье не рассматривается создание веб-приложения `TaskWebApp` или веб-API `TaskService`. Чтобы узнать, как с помощью Azure AD B2C создать веб-приложение, см. [руководство по веб-приложениям .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Чтобы узнать, как с помощью Azure AD B2C создать и защитить веб-API, см. [руководство по веб-API .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Обновление конфигурации Azure AD B2C

В нашем примере настроено использование политик и идентификатора демонстрационного клиента. Если вы хотите использовать собственный клиент, выполните следующее.

1. Откройте `web.config` в проекте `TaskService` и замените следующие значения:

    * `ida:Tenant` именем своего клиента;
    * `ida:ClientId` идентификатором клиента для приложения веб-API;
    * `ida:SignUpSignInPolicyId` именем политики регистрации и входа в систему;

2. Откройте `web.config` в проекте `TaskWebApp` и замените следующие значения:

    * `ida:Tenant` именем своего клиента;
    * `ida:ClientId` идентификатором клиента для веб-приложения;
    * `ida:ClientSecret` секретным ключом веб-приложения;
    * `ida:SignUpSignInPolicyId` именем политики регистрации и входа в систему;
    * `ida:EditProfilePolicyId` именем политики "Изменение профиля";
    * `ida:ResetPasswordPolicyId` именем политики "Сброс профиля".



## <a name="requesting-and-saving-an-access-token"></a>Запрос и сохранение маркера доступа

### <a name="specify-the-permissions"></a>Установка разрешений

Чтобы направить вызов к веб-интерфейсу API, необходимо выполнить аутентификацию пользователя (в соответствии с политикой регистрации и входа) и [получить маркер доступа](active-directory-b2c-access-tokens.md) от Azure AD B2C. Чтобы получить маркер доступа, сначала требуется указать разрешения, которые он будет предоставлять. Разрешения указываются в параметре `scope` при выполнении запроса к конечной точке `/authorize`. Например, чтобы получить маркер доступа с разрешением на чтение для ресурсов приложения с URI идентификатора приложения `https://contoso.onmicrosoft.com/tasks`, нужно задать область `https://contoso.onmicrosoft.com/tasks/read`.

Чтобы изменить область в нашем примере, откройте файл `App_Start\Startup.Auth.cs` и определите переменную `Scope` в разделе OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Обмен кода авторизации на маркер доступа

Когда пользователь выполнит процесс регистрации или входа в систему, приложение получит код авторизации от Azure AD B2C. ПО промежуточного слоя OWIN OpenID Connect сохраняет этот код, но не обменивает его на маркер доступа. Для этого обмена можно использовать [библиотеку MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). Для нашего примера мы настроили обратный вызов уведомления, который ПО промежуточного слоя OpenID Connect выполняет при каждом получении кода авторизации. В функции обратного вызова с помощью функций MSAL код обменивается на маркер доступа, который сохраняется в кэш.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Вызов веб-API

В этом разделе описано, как использовать для доступа к защищенному веб-API маркер, полученный от Azure AD B2C при регистрации или входе в систему.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Использование сохраненного маркера в контроллерах

`TasksController` отвечает за взаимодействие с веб-API из отправку HTTP-запросов к API на чтение, создание и удаление задач. Так как API-интерфейс защищен с помощью Azure AD B2C, сначала нужно получить маркер, сохраненный на предыдущем этапе.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Чтение задач из веб-API

Теперь, когда у вас есть маркер, его можно вложить в HTTP-запрос `GET` в заголовке `Authorization`, чтобы безопасно вызвать `TaskService`:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Создание и удаление задач в веб-API

Используйте эту же схему при отправке запросов `POST` и `DELETE` к веб-API, получая маркера доступа из кэша с помощью MSAL.

## <a name="run-the-sample-app"></a>Запуск примера приложения

Выполните сборку обоих приложений и запустите их. Зарегистрируйтесь в приложении и войдите в него, а затем создайте задачи для пользователя, выполнившего вход. Выйдите и зарегистрируйтесь от имени другого пользователя. Создайте задачи для этого пользователя. Обратите внимание, что в API хранятся задачи для каждого пользователя, так как API извлекает удостоверение пользователя из получаемого маркера. Также выполните эксперименты с областями. Удалите разрешение на запись и попробуйте добавить задачу. После каждого изменения областей обязательно выполняйте выход из системы.

