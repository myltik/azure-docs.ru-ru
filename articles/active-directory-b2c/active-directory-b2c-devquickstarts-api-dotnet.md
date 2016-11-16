---
title: "Azure AD B2C | Документация Майкрософт"
description: "Как с помощью Azure Active Directory B2C создать веб-API .NET, защищенный маркерами доступа OAuth 2.0 для проверки подлинности."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 370978187cffa2e5a9544bf99e6a15e13f97ac53


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: создание веб-API .NET
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

С помощью Azure Active Directory (Azure AD) B2C можно защитить веб-API с помощью маркера доступа OAuth 2.0. Эти маркеры позволяют клиентским приложениям, использующим Azure AD B2C, проходить проверку подлинности для API. В этой статье показано, как создать интерфейс веб-API .NET "Список дел" с использованием модели Model-View-Controller (MVC), который позволяет пользователям выполнять задачи CRUD. Этот веб-API защищен с помощью Azure AD B2C. Управлять списком дел могут только пользователи, прошедшие проверку подлинности.

## <a name="create-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C
Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Прежде чем продолжать работу с руководством, при необходимости [создайте каталог B2C](active-directory-b2c-get-started.md).

## <a name="create-an-application"></a>Создание приложения
Затем необходимо создать приложение в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

* Включите в приложение **веб-приложение** или **веб-API**.
* Используйте **универсальный код ресурса перенаправления** `https://localhost:44316/` для веб-приложения. Это стандартное расположение клиента веб-приложения для этого примера.
* Скопируйте **идентификатор приложения** , назначенный приложению. Он понадобится вам позднее.
  
  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Создание политик
В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Клиент в этом примере кода включает три способа работы с идентификацией: регистрацию, вход в систему и изменение профиля. Вам потребуется создать по одной политике для каждого типа, как описано в [справочнике по политикам](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно выполните указанные ниже действия.

* В колонке поставщиков удостоверений выберите **Регистрация с помощью идентификатора пользователя** или **Регистрация по электронной почте**.
* В политике регистрации укажите **отображаемое имя** и другие атрибуты регистрации.
* В каждой политике в качестве утверждения приложения выберите утверждения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой созданной политики. Эти имена политик понадобятся вам через некоторое время.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

## <a name="download-the-code"></a>Загрузка кода
Код для этого руководства [размещен на портале GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Чтобы выполнить сборку примера, [скачайте схему проекта в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

Кроме того, можно скачать готовое приложение [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу. Теперь решение содержит два проекта: `TaskWebApp` и `TaskService`. `TaskWebApp` — это веб-приложение MVC, с которым взаимодействует пользователь. `TaskService` — веб-API серверной части приложения, в котором хранится список дел для каждого пользователя.

## <a name="configure-the-task-web-app"></a>Настройка веб-приложения для задач
Когда пользователь взаимодействует с `TaskWebApp`, клиент отправляет запросы в Azure AD и получает маркеры, которые можно использовать для вызова веб-API `TaskService`. Чтобы пользователь мог выполнить вход и получить токены, необходимо предоставить `TaskWebApp` определенные сведения о приложении. В корне проекта `TaskWebApp` откройте файл `web.config` и замените значения в разделе `<appSettings>`.  Можно не менять значения `AadInstance`, `RedirectUri` и `TaskServiceUrl`.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

В этой статье не описывается создание клиента `TaskWebApp`.  Сведения о создании веб-приложения с помощью Azure AD B2C см. в [этом руководстве по веб-приложениям .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Защита API
Теперь, когда у вас есть клиент, который вызывает API от имени пользователей, можно защитить `TaskService` с помощью токенов носителя OAuth 2.0. API может принимать и проверять токены с помощью библиотеки OWIN от Майкрософт.

### <a name="install-owin"></a>Установка OWIN
Сначала установите конвейер проверки подлинности OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Ввод данных B2C
В корне проекта `TaskService` откройте файл `web.config` и замените значения в разделе `<appSettings>`. Эти значения будут использоваться в API и библиотеке OWIN.  Можно оставить значение `AadInstance` без изменений.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Добавление класса запуска OWIN
Добавьте класс запуска OWIN в проект `TaskService` с именем `Startup.cs`.  Щелкните проект правой кнопкой мыши и выберите **Добавить** и **Новый элемент**, после чего найдите OWIN.

```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Настройка проверки подлинности OAuth 2.0
Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Настройка защиты контроллера задач
Теперь, когда приложение настроено для использования проверки подлинности OAuth 2.0, вы можете добавить тег `[Authorize]` к контроллеру задач для защиты веб-API. В этом контроллере выполняются все операции со списками дел, поэтому нужно защитить весь контроллер на уровне класса. Для более точного управления можно добавить тег `[Authorize]` к отдельным действиям.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Получение сведений о пользователе из токена.
`TasksController` сохраняет задачи в базе данных, где каждой задаче соответствует пользователь, являющийся ее владельцем. Владелец определяется **идентификатором объекта**пользователя. (Поэтому необходимо добавить идентификатор объекта в качестве утверждения приложения во все свои политики.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Запуск примера приложения
Наконец, выполните сборку и запустите `TaskWebApp` и `TaskService`. Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Создайте несколько задач в списке дел. Обратите внимание, что они сохраняются в API даже после остановки и перезапуска клиента.

## <a name="edit-your-policies"></a>Изменение политик
Теперь, когда у вас есть API, защищенный с помощью Azure AD B2C, можно поэкспериментировать с политиками приложения и понаблюдать, как это влияет (или не влияет) на API. Вы можете управлять утверждениями приложения в политиках и изменять сведения о пользователе, доступные в веб-API. Любые дополнительные утверждения, которые вы добавляете, будут доступны для веб-API .NET MVC в объекте `ClaimsPrincipal` , как описано ранее в этой статье.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Nov16_HO2-->


