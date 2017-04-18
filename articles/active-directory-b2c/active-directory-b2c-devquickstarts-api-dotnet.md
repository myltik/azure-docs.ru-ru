---
title: "Azure AD B2C | Документация Майкрософт"
description: "Как с помощью Azure Active Directory B2C создать веб-API .NET, защищенный маркерами доступа OAuth 2.0 для проверки подлинности."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: d67043f2e0a062796f4d6167b28774ce494027c2
ms.lasthandoff: 04/10/2017


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: создание веб-API .NET

С помощью Azure Active Directory (Azure AD) B2C можно защитить веб-API с помощью маркера доступа OAuth 2.0. Эти маркеры позволяют клиентским приложениям проходить проверку подлинности для API. В этой статье показано, как создать интерфейс веб-API .NET "Список дел" с использованием модели Model-View-Controller (MVC), который позволяет пользователям клиентского приложения выполнять задачи CRUD. Этот веб-API защищен с помощью Azure AD B2C. Управлять списком дел могут только пользователи, прошедшие проверку подлинности.

## <a name="create-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Прежде чем продолжать работу с руководством, [создайте каталог B2C](active-directory-b2c-get-started.md), если вы его еще не создали.

> [!NOTE]
> Клиентское приложение и веб-API должны использовать один и тот же каталог Azure AD B2C.
>

## <a name="create-a-web-api"></a>Создание веб-API

Теперь необходимо создать приложение веб-API в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

* Включите в приложение **веб-приложение** или **веб-API**.
* Используйте **URI перенаправления** `https://localhost:44332/` для веб-приложения. Это стандартное расположение клиента веб-приложения для этого примера.
* Скопируйте **идентификатор приложения** , назначенный приложению. Он понадобится вам позднее.
* Введите идентификатор приложения в поле **URI кода приложения**.
* Добавьте разрешения с помощью меню **Published scopes** (Опубликованные области).

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Вам понадобится создать политику для взаимодействия с Azure AD B2C. Мы советуем использовать объединенную политику регистрации и входа, как описано в статье [Azure Active Directory B2C: расширяемая инфраструктура политик](active-directory-b2c-reference-policies.md). При создании политики обязательно сделайте следующее:

* В политике укажите **отображаемое имя** и другие атрибуты регистрации.
* В каждой политике в качестве утверждения приложения выберите утверждения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой созданной политики. Эти имена политик понадобятся вам позже.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав политику, можно приступать к созданию приложения.

## <a name="download-the-code"></a>Загрузка кода

Код примеров для этого руководства размещен на портале [GitHub](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi). Вы можете клонировать пример, выполнив такую команду:

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу. Теперь решение содержит два проекта: `TaskWebApp` и `TaskService`. `TaskWebApp` — это веб-приложение MVC, с которым взаимодействует пользователь. `TaskService` — веб-API серверной части приложения, в котором хранится список дел для каждого пользователя. В этой статье рассматривается только приложение `TaskService`. Сведения о создании `TaskWebApp` с помощью Azure AD B2C см. в [этом руководстве по веб-приложениям .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Обновление конфигурации Azure AD B2C

В нашем примере настроено использование политик и идентификатора клиента демонстрационного клиента. Если вы хотите использовать собственный клиент, необходимо сделать следующее:

1. Откройте `web.config` в проекте `TaskService` и замените следующие значения:
    * `ida:Tenant` именем своего клиента;
    * `ida:ClientId` идентификатором клиента для приложения веб-API;
    * `ida:SignUpSignInPolicyId` именем политики регистрации и входа в систему.

2. Откройте `web.config` в проекте `TaskWebApp` и замените следующие значения:
    * `ida:Tenant` именем своего клиента;
    * `ida:ClientId` идентификатором клиента для веб-приложения;
    * `ida:ClientSecret` секретным ключом веб-приложения;
    * `ida:SignUpSignInPolicyId` именем политики регистрации и входа в систему;
    * `ida:EditProfilePolicyId` именем политики "Изменение профиля";
    * `ida:ResetPasswordPolicyId` именем политики "Сброс профиля".


## <a name="secure-the-api"></a>Защита API

Теперь, когда у вас есть клиент, который вызывает API, можно защитить API (например, `TaskService`) с помощью токенов носителя OAuth 2.0. Это гарантирует, что каждый запрос к API будет действителен, только если содержит токен носителя. API может принимать и проверять токены носителя с помощью библиотеки OWIN от Майкрософт.

### <a name="install-owin"></a>Установка OWIN

Начните с установки конвейера проверки подлинности OWIN OAuth с помощью консоли диспетчера пакетов Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Будет выполнена установка ПО промежуточного слоя OWIN, которое будет принимать и проверять токены носителя.

### <a name="add-an-owin-startup-class"></a>Добавление класса запуска OWIN

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

### <a name="configure-oauth-20-authentication"></a>Настройка проверки подлинности OAuth 2.0

Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`. Это может выглядеть следующим образом:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Настройка защиты контроллера задач

Теперь, когда приложение настроено для использования проверки подлинности OAuth 2.0, вы можете добавить тег `[Authorize]` к контроллеру задач для защиты веб-API. В этом контроллере выполняются все операции со списками дел, поэтому нужно защитить весь контроллер на уровне класса. Для более точного управления можно добавить тег `[Authorize]` к отдельным действиям.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Получение сведений о пользователе из токена.

`TasksController` сохраняет задачи в базе данных, где каждой задаче соответствует пользователь, являющийся ее владельцем. Владелец определяется **идентификатором объекта**пользователя. (Поэтому необходимо добавить идентификатор объекта в качестве утверждения приложения во все свои политики.)

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Проверка разрешений в токене

Общим требованием для веб-API является проверка областей (scope) в токене. Это гарантирует, что пользователь предоставил разрешения, необходимые для доступа к службе списка дел.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Наконец, выполните сборку и запустите `TaskWebApp` и `TaskService`. Создайте несколько задач в списке дел. Обратите внимание, что они сохраняются в API даже после остановки и перезапуска клиента.

## <a name="edit-your-policies"></a>Изменение политик

Теперь, когда у вас есть API, защищенный с помощью Azure AD B2C, можно поэкспериментировать с политиками регистрации и входа и понаблюдать, как это влияет (или не влияет) на API. Вы можете управлять утверждениями приложения в политиках и изменять сведения о пользователе, доступные в веб-API. Любые дополнительные утверждения, которые вы добавляете, будут доступны для веб-API .NET MVC в объекте `ClaimsPrincipal` , как описано ранее в этой статье.

