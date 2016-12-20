---
title: "Azure Active Directory B2C | Документация Майкрософт"
description: "Как с помощью Azure Active Directory B2C создать веб-приложение, которое вызывает веб-API."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: b235c7a773b1c7c2a5f4d5825c6a20c1b0afb7fb


---
# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure AD B2C: вызов веб-API из веб-приложения .NET
Azure Active Directory (Azure AD) B2C позволяет добавлять в веб-приложения и веб-API мощные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье показано, как создать веб-приложение .NET Model-View-Controller (MVC) "Список дел", которое вызывает веб-API с помощью токенов носителя.

В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Чтобы изучить основы Azure AD B2C, прочитайте [руководство по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md) .

## <a name="get-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C
Перед использованием Azure AD B2C необходимо создать каталог или клиент.  Каталог — это контейнер для всех пользователей, приложений, групп и т. д.  Прежде чем продолжать работу с руководством, [создайте каталог B2C](active-directory-b2c-get-started.md).

## <a name="create-an-application"></a>Создание приложения
Затем необходимо создать приложение в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. В этом случае и веб-приложение, и веб-API будут представлены одним **идентификатором приложения**, так как они включают одно приложение логики. Создайте приложение, выполнив [эти указания](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

* Включите в приложение **веб-приложение или веб-API** .
* Введите `https://localhost:44316/` в качестве **URL-адреса ответа**. Это URL-адрес по умолчанию для данного примера кода.
* Скопируйте **идентификатор приложения** , назначенный приложению. Этот идентификатор также потребуется позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Создание политик
В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Веб-приложение в этом примере кода включает три способа идентификации: регистрацию, вход в систему и изменение профиля. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). При создании трех необходимых политик обязательно сделайте следующее:

* В политике регистрации укажите **отображаемое имя** и другие атрибуты регистрации.
* Выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта** для каждой политики. Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой созданной политики. У него должен быть префикс `b2c_1_`. Имена политик понадобятся вам позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к сборке приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Загрузка кода
Код этого учебника [размещен на портале GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

Кроме того, можно скачать готовое приложение [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу.

## <a name="configure-the-task-web-app"></a>Настройка веб-приложения для задач
Чтобы приложение `TaskWebApp` взаимодействовало с Azure AD B2C, необходимо задать несколько общих параметров. В корне проекта `TaskWebApp` откройте файл `web.config` и замените значения в разделе `<appSettings>`. Можно не менять значения `AadInstance`, `RedirectUri` и `TaskServiceUrl`.

```
  <appSettings>

    ...

    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>Получение маркеров доступа и вызов API задачи
В этом разделе описано, как использовать маркер, полученный при входе в систему с помощью Azure AD B2C, для доступа к веб-API, также защищенного посредством Azure AD B2C.

Здесь не рассматриваются вопросы, связанные с защитой API. Сведения о том, как веб-API надежно выполняет проверку подлинности запросов с помощью Azure AD B2C, см. в [статье о начале работы с веб-API](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="save-the-sign-in-token"></a>Сохранение маркера входа
Сначала выполните проверку подлинности пользователя (с помощью любой из политик) и получите маркер входа из Azure AD B2C.  Если вы не знаете, как выполнять политики, изучите основы Azure AD B2C, прочитав [руководство по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md) .

Откройте файл `App_Start\Startup.Auth.cs`.  Существует одно важное изменение для `OpenIdConnectAuthenticationOptions` — необходимо задать значение `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

return new OpenIdConnectAuthenticationOptions
{
    // For each policy, give OWIN the policy-specific metadata address, and
    // set the authentication type to the id of the policy
    MetadataAddress = String.Format(aadInstance, tenant, policy),
    AuthenticationType = policy,

    // These are standard OpenID Connect parameters, with values pulled from web.config
    ClientId = clientId,
    RedirectUri = redirectUri,
    PostLogoutRedirectUri = redirectUri,
    Notifications = new OpenIdConnectAuthenticationNotifications
    {
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",

        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>Получение маркера в контроллерах
`TasksController` отвечает за взаимодействие с веб-API, а также отправляет в API HTTP-запросы на чтение, создание и удаление задач.  Так как API-интерфейс защищен с помощью Azure AD B2C, вам нужно сначала получить маркер, сохраненный на предыдущем этапе.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;

    ...
}
```

`BootstrapContext` содержит маркер входа, который вы получили, выполнив одну из политик B2C.

### <a name="read-tasks-from-the-web-api"></a>Чтение задач из веб-API
Теперь, когда у вас есть маркер, его можно вложить в HTTP-запрос `GET` в заголовке `Authorization`, чтобы безопасно вызвать `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Создание и удаление задач в веб-API
Следуйте этой схеме при отправке запросов `POST` и `DELETE` в веб-API с помощью `BootstrapContext` для получения маркера входа. Действие будет реализовано автоматически. Действие удаления можно завершить в `TasksController.cs`.

## <a name="run-the-sample-app"></a>Запуск примера приложения
Теперь можно собрать и запустить приложение. Зарегистрируйтесь в приложении и войдите в него, а затем создайте задачи для пользователя, выполнившего вход. Выйдите и зарегистрируйтесь от имени другого пользователя. Создайте задачи для этого пользователя. Обратите внимание, что в API хранятся задачи для каждого пользователя, так как API извлекает удостоверение пользователя из получаемого маркера.

Готовый пример [предоставляется в виде ZIP-файла](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->



<!--HONumber=Nov16_HO3-->


