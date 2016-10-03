<properties
	pageTitle="Веб-API .NET для Azure AD версии 2.0 | Microsoft Azure"
	description="Как создать веб-API .NET MVC, принимающий маркеры доступа личных учетных записей Майкрософт, а также рабочих и учебных учетных записей."
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

# Безопасность веб-API MVC

Используя конечную точку Azure Active Directory версии 2.0, вы можете защитить веб-API с помощью маркеров доступа [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) и позволить пользователям входить в систему с помощью личной, рабочей или учебной учетной записи Майкрософт для безопасного доступа к веб-API.

> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

В веб-API ASP.NET это можно делать с помощью ПО промежуточного уровня OWIN корпорации Microsoft, включенного в .NET Framework 4.5. Мы будем использовать OWIN для создания "списка задач" веб-API MVC, который позволяет клиентам создавать и читать задачи из списка дел пользователя. Этот веб-API будет проверять, содержат ли входящие запросы действительный маркер доступа, и отклонять запросы, которые не прошли проверку на защищенном маршруте.

## Загрузить
Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) или клонировать ее:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

Скелет приложения включает в себя весь код шаблона простого API, но в нем отсутствуют все компоненты, связанные с удостоверениями. Если не хотите следовать учебнику, вместо этого можно клонировать или [скачать готовый пример](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните [эти подробные указания](active-directory-v2-app-registration.md). Не забудьте:

- запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.

В этом решении Visual Studio также содержится клиент TodoListClient, который представляет собой простое приложение WPF. TodoListClient используется для того, чтобы показать, как пользователь входит в систему и как клиент может отправлять запросы к вашему веб-API. В этом случае и TodoListClient, и TodoListService представлены одним приложением. Чтобы настроить TodoListClient, вам нужно также:

- Добавьте для приложения **мобильную** платформу.
- Скопируйте с портала **универсальный код ресурса (URI) перенаправления**. Необходимо использовать стандартное значение `urn:ietf:wg:oauth:2.0:oob`.


## Установка OWIN

Зарегистрировав приложение, вам нужно настроить в нем обмен данными с конечной точкой v2.0. Это позволит проверять входящие запросы и маркеры.

- Для начала откройте решение и добавьте пакеты NuGet промежуточного слоя OWIN в проект TodoListService с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## Настройка аутентификации OAuth 2.0

- Добавьте класс OWIN Startup в проект TodoListService под именем `Startup.cs`. Щелкните правой кнопкой мыши проект и выберите в контекстном меню **Добавить**, затем **Новый элемент** и найдите OWIN. При запуске приложения промежуточный слой OWIN вызовет метод `Configuration(…)`.
- Замените объявление класса на `public partial class Startup` — часть этого класса уже была реализована в другом файле. В методе `Configuration(…)` добавьте вызов ConfgureAuth(...), чтобы настроить проверку подлинности для веб-приложения.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(…)`, который настраивает веб-API для приема маркеров доступа из конечной точки версии 2.0.

```C#
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
				// signed up for the app.  Here, we'll just turn it off.

				ValidateIssuer = false,
		};

		// Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
		// The options provided here tell the middleware about the type of tokens
		// that will be recieved, which are JWTs for the v2.0 endpoint.

		// NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
		// metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
		// OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
		// metadata document.

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{
				AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
		});
}
```

- Теперь можно использовать атрибуты `[Authorize]` для защиты контроллеров и действий с помощью аутентификации OAuth 2.0. Снабдите класс `Controllers\TodoListController.cs` тегом авторизации. Пользователь будет вынужден войти в систему, прежде чем сможет обратиться к этой странице.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Когда авторизованный вызывающий объект успешно вызывает один из интерфейсов API `TodoListController`, может потребоваться доступ к сведениям о вызывающем объекте. OWIN предоставляет доступ к утверждениям внутри маркера защиты посредством объекта `ClaimsPrincpal`.

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-	Наконец, откройте файл `web.config` в корне проекта TodoListService, а затем введите значения конфигурации в разделе `<appSettings>`.
  -	Ваш `ida:Audience` — это **идентификатор приложения**, введенный на портале.

## Настройка клиентского приложения
Чтобы увидеть службу To Do List в действии, необходимо настроить To Do List Client для получения маркеров от конечной точки версии 2.0 и выполнения вызовов службы.

- Откройте файл `App.config` проекта TodoListClient и введите значения конфигурации в разделе `<appSettings>`.
  -	Идентификатор приложения `ida:ClientId`, скопированный на портале.
	- `ida:RedirectUri` — это **универсальный код ресурса (URI) перенаправления** с портала.

Наконец, выполните очистку, соберите и запустите каждый из проектов. Теперь у вас есть веб-API .NET MVC, принимающий маркеры доступа личных учетных записей Майкрософт, а также рабочих и учебных учетных записей. Войдите в TodoListClient и вызовите веб-API для добавления задач в список дел пользователя.

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip) или клонировать с портала GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## Дальнейшие действия
Теперь можно приступить к изучению других разделов. Можно попробовать:

[Вызов веб-API из веб-приложения](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Дополнительные ресурсы:
- [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Тег StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0921_2016-->