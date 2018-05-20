---
title: Добавление функции входа в веб-API .NET MVC с помощью конечной точки Azure AD версии 2.0 | Документация Майкрософт
description: Как создать веб-API .NET MVC, принимающий маркеры доступа личных учетных записей Майкрософт, а также рабочих и учебных учетных записей.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: aa73e918cbd49fee850e402859708ba0c4185a19
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="secure-an-mvc-web-api"></a>Безопасность веб-API MVC
Используя конечную точку Azure Active Directory версии 2.0, вы можете защитить веб-API с помощью маркеров доступа [OAuth 2.0](active-directory-v2-protocols.md) и позволить пользователям входить в систему с помощью личной, рабочей или учебной учетной записи Майкрософт для безопасного доступа к веб-API.

> [!NOTE]
> Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
>
>

В веб-API ASP.NET это можно делать с помощью ПО промежуточного уровня OWIN корпорации Microsoft, включенного в .NET Framework 4.5. Мы будем использовать OWIN для создания "списка задач" веб-API MVC, который позволяет клиентам создавать и читать задачи из списка дел пользователя. Этот веб-API будет проверять, содержат ли входящие запросы действительный маркер доступа, и отклонять запросы, которые не прошли проверку на защищенном маршруте. Этот пример создан с помощью Visual Studio 2015.

## <a name="download"></a>Загрузка
Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) или клонировать ее:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

Скелет приложения включает в себя весь код шаблона простого API, но в нем отсутствуют все компоненты, связанные с удостоверениями. Если не хотите следовать учебнику, вместо этого можно клонировать или [скачать готовый пример](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или выполните [эти подробные указания](active-directory-v2-app-registration.md). Не забудьте:

* Запишите назначенный вашему приложению **идентификатор приложения**. Он вскоре вам понадобится.

В этом решении Visual Studio также содержится клиент TodoListClient, который представляет собой простое приложение WPF. TodoListClient используется для того, чтобы показать, как пользователь входит в систему и как клиент может отправлять запросы к вашему веб-API. В этом случае и TodoListClient, и TodoListService представлены одним приложением. Чтобы настроить TodoListClient, вам нужно также:

* Добавьте для приложения **мобильную** платформу.

## <a name="install-owin"></a>Установка OWIN
Зарегистрировав приложение, вам нужно настроить в нем обмен данными с конечной точкой v2.0. Это позволит проверять входящие запросы и маркеры.

* Для начала откройте решение и добавьте пакеты NuGet промежуточного слоя OWIN в проект TodoListService с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
PM> Install-Package Microsoft.IdentityModel.Protocol.Extensions -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Настройка аутентификации OAuth 2.0
* Добавьте класс OWIN Startup в проект TodoListService под именем `Startup.cs`. Щелкните правой кнопкой мыши проект, выберите в контекстном меню пункты **Добавить** --> **Новый элемент** и найдите OWIN. При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(…)` .
* Замените объявление класса на `public partial class Startup` — часть этого класса уже была реализована в другом файле. В методе `Configuration(…)` добавьте вызов ConfgureAuth(...), чтобы настроить проверку подлинности для веб-приложения.

```csharp
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(…)`, который настраивает веб-API для приема маркеров доступа из конечной точки версии 2.0.

```csharp
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app. Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint. Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* Теперь можно использовать атрибуты `[Authorize]` для защиты контроллеров и действий с помощью аутентификации OAuth 2.0. Снабдите класс `Controllers\TodoListController.cs` тегом авторизации. Пользователь будет вынужден войти в систему, прежде чем сможет обратиться к этой странице.

```csharp
[Authorize]
public class TodoListController : ApiController
{
```

* Когда авторизованный вызывающий объект успешно вызывает один из интерфейсов API `TodoListController` , может потребоваться доступ к сведениям о вызывающем объекте. OWIN предоставляет доступ к утверждениям внутри маркера защиты посредством объекта `ClaimsPrincipal` . 

```csharp
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call. In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Наконец, откройте файл `web.config` в корне проекта TodoListService, а затем введите значения конфигурации в разделе `<appSettings>`.
  * Ваш `ida:Audience` — это **идентификатор приложения** , введенный на портале.

## <a name="configure-the-client-app"></a>Настройка клиентского приложения
Чтобы увидеть службу To Do List в действии, необходимо настроить To Do List Client для получения маркеров от конечной точки версии 2.0 и выполнения вызовов службы.

* Откройте файл `App.config` проекта TodoListClient и введите значения конфигурации в разделе `<appSettings>`.
  * Идентификатор приложения `ida:ClientId` , скопированный на портале.

Наконец, выполните очистку, соберите и запустите каждый из проектов.  Теперь у вас есть веб-API .NET MVC, принимающий маркеры доступа личных учетных записей Майкрософт, а также рабочих и учебных учетных записей. Войдите в TodoListClient и вызовите веб-API для добавления задач в список дел пользователя.

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip)или клонировать с портала GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Дополнительная информация
Теперь можно приступить к изучению других разделов. Можно попробовать:

[Вызов веб-API из веб-приложения >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Дополнительные ресурсы:

* [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
* [Тег StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Получение обновлений системы безопасности для наших продуктов
Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).
