<properties
	pageTitle="Модель приложений 2.0 | Microsoft Azure"
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Предварительная версия модели приложений 2.0: защита веб-API MVC

Модель приложений версии 2.0 позволяет вам защитить веб-API с помощью маркеров доступа [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), а пользователям входить в систему с помощью личной, рабочей и учебной учетной записи Майкрософт для безопасного доступа к веб-API.

> [AZURE.NOTE]Эти сведения относятся к общедоступной предварительной версии модели приложений 2.0. Инструкции по интеграции с общедоступной службой Azure AD см. в статье [Руководство разработчика Azure Active Directory](active-directory-developers-guide.md).

В веб-API ASP.NET это можно делать с помощью ПО промежуточного уровня OWIN корпорации Microsoft, включенного в .NET Framework 4.5. Здесь мы используем OWIN для создания веб-API MVC приложения списка задач, который: разрешает клиентам создавать и читать задачи из списка задач пользователя; определяет, какие интерфейсы следует защищать; проверяет, чтобы вызовы веб-API содержали допустимый маркер доступа.

Чтобы сделать это, необходимо:

1. Регистрация приложения с помощью Azure AD
2. установить приложение для использования проверки подлинности OWIN;
3. Настройка клиентского приложения для вызова веб-API приложения To Do List.

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

Готовое приложение также приводится в конце этого руководства.


## 1. Регистрация приложения
Создайте приложение на сайте [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните инструкции, приведенные в [этой статье](active-directory-v2-app-registration.md). Обязательно сделайте вот что:

- запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.

В этом решении Visual Studio также содержится клиент TodoListClient, который представляет собой простое приложение WPF. TodoListClient используется для того, чтобы показать, как пользователь входит в систему и как клиент может отправлять запросы к вашему веб-API. В этом случае и TodoListClient, и TodoListService представлены одним приложением. Чтобы настроить TodoListClient, вам нужно также:

- добавить для своего приложения **мобильную** платформу;
- записать **URI перенаправления** с портала. Необходимо использовать стандартное значение `urn:ietf:wg:oauth:2.0:oob`.


## 2. Настройка в приложении проверки подлинности OWIN

Зарегистрировав приложение, вам нужно настроить в нем обмен данными с конечной точкой v2.0. Это позволит проверять входящие запросы и маркеры.

-	Сначала откройте решение и добавьте к проекту TodoListService пакеты NuGet ПО промежуточного слоя OWIN при помощи консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService 
```

-	Добавьте класс OWIN Startup в проект TodoListService под именем `Startup.cs`. Щелкните правой кнопкой мыши проект и выберите в контекстном меню **Добавить**, затем **Новый элемент** и найдите OWIN. При запуске приложения промежуточный слой OWIN вызовет метод `Configuration(…)`.
-	Замените объявление класса на `public partial class Startup` — часть этого класса уже была реализована в другом файле. В методе `Configuration(…)` добавьте вызов ConfgureAuth(...), чтобы настроить проверку подлинности для веб-приложения.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(…)`, который настраивает веб-API для приема маркеров доступа из конечной точки версии 2.0.

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

-	Теперь можно использовать атрибуты `[Authorize]` для защиты контроллеров и действий с помощью проверки подлинности OAuth 2.0. Снабдите класс `Controllers\TodoListController.cs` тегом авторизации. Пользователь будет вынужден войти в систему, прежде чем сможет обратиться к этой странице.

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

## 3\. Настройка клиентского приложения и запуск службы
Чтобы увидеть службу To Do List в действии, необходимо настроить To Do List Client для получения маркеров от конечной точки версии 2.0 и выполнения вызовов службы.

- Откройте файл `App.config` проекта TodoListClient и введите значения конфигурации в разделе `<appSettings>`.
  -	Идентификатор приложения `ida:ClientId`, скопированный из портала.
	- `ida:RedirectUri` — это **URI перенаправления** с портала.

Наконец, выполните очистку, соберите и запустите каждый из проектов. Теперь у вас есть веб-API .NET MVC, принимающий маркеры доступа личных учетных записей Майкрософт, а также рабочих и учебных учетных записей. Войдите в TodoListClient и вызовите веб-API для добавления задач в список дел пользователя.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) в виде ZIP-файла находится [здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip). Также можно клонировать его с GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## Дальнейшие действия
Теперь можно приступить к изучению других разделов. Можно попробовать:

[Вызов веб-API из веб-приложения с помощью модели приложения версии 2.0 >>](active-directory-devquickstarts-webapp-webapi-dotnet.md)

Дополнительные ресурсы: -[Предварительная версия модели приложений 2.0 >>](active-directory-appmodel-v2-overview.md) -[Тег StackOverflow "azure active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!----HONumber=August15_HO7-->