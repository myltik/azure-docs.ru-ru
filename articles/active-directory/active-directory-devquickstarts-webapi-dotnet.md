<properties
	pageTitle="Приступая к работе с Azure AD для .NET | Microsoft Azure"
	description="Практическое руководство по созданию веб-интерфейса API для .NET MVC, который интегрируется с Azure AD для выполнения проверки подлинности и авторизации."
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


# Защита веб-интерфейса API с помощью маркеров носителя из Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Если вы создаете приложение, которое предоставляет доступ к конфиденциальным ресурсам, необходимо знать, как защитить эти ресурсы от несанкционированного доступа. Azure AD упрощает для разработчика процесс защиты веб-интерфейса API с помощью маркеров доступа OAuth Bearer 2.0 — потребуется добавить всего несколько строк кода.

В веб-приложениях ASP.NET это можно сделать с помощью реализации Майкрософт промежуточного слоя OWIN (открытого интерфейса для .NET, поддерживаемого сообществом разработчиков), включенного в .NET Framework 4.5. Мы будем использовать OWIN для создания веб-API To Do List, который:
-	указывает, какие API защищены;
-	проверяет, содержат ли вызовы веб-API действительный маркер доступа.

Для этого вам необходимо:

1. зарегистрировать приложение в Azure AD;
2. Настроить приложение для использования конвейера проверки подлинности OWIN.
3. Настроить клиентское приложение для вызова методов веб-API приложения To Do List.

Чтобы начать работу, [загрузите каркас приложения](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) или [завершенный пример](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Каждый из них является решением Visual Studio 2013. Вам также потребуется клиент Azure AD для регистрации приложения. Если у вас нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).


## *1. Регистрация приложения в Azure AD*
Для защиты приложения необходимо сначала создать приложение в клиенте и предоставить Azure AD некоторую важную информации.

-	Войдите на [Портал управления Azure](https://manage.windowsazure.com)
-	В левой панели навигации щелкните **Active Directory**.
-	Выберите клиента, в котором будет зарегистрировано приложение.
-	Перейдите на вкладку **Приложения** и нажмите кнопку **Добавить** в нижней панели.
-	Следуйте инструкциям на экране, а затем создайте новое **Веб-приложение и/или веб-API**.
    -	**Имя** приложения является его описанием для конечных пользователей. Введите "Служба To Do List".
    -	**URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения любых маркеров, запрошенных приложением. Укажите `https://localhost:44321/` для этого параметра.
-	После завершения регистрации перейдите на вкладку **Настройка** и найдите поле **URI кода приложения**. Укажите для этого параметра идентификатор, специфичный для клиента, например `https://contoso.onmicrosoft.com/TodoListService`
- Сохраните конфигурацию. Не закрывайте портал, так как через некоторое время потребуется зарегистрировать клиентское приложение.

## *2. Настройка приложения для использования конвейера проверки подлинности OWIN*

Теперь после регистрации приложения в Azure AD необходимо настроить приложение для взаимодействия с Azure AD, чтобы выполнять проверку входящих запросов и маркеров.

-	Для начала откройте решение и добавьте пакеты NuGet промежуточного слоя OWIN в проект TodoListService с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
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

-	Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(…)`. Параметры, указанные в `WindowsAzureActiveDirectoryBearerAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-	Теперь можно использовать атрибуты `[Authorize]` для защиты контроллеров и действий с помощью проверки подлинности JWT. Снабдите класс `Controllers\TodoListController.cs` тегом авторизации. Пользователь будет вынужден войти в систему, прежде чем сможет обратиться к этой странице.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Когда авторизованный вызывающий объект успешно вызывает один из интерфейсов API `TodoListController`, может потребоваться доступ к сведениям о вызывающем объекте. OWIN предоставляет доступ к утверждениям внутри маркера защиты посредством объекта `ClaimsPrincpal`.
- Общим требованием для веб-интерфейсов API является проверка областей (scope) в маркере, чтобы гарантировать, что конечный пользователь предоставил разрешения, необходимые для доступа к службе To Do List:

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-	Наконец, откройте файл `web.config` в корне проекта TodoListService, а затем введите значения конфигурации в разделе `<appSettings>`.
  -	`ida:Tenant` — это имя вашего клиента Azure AD, например "contoso.onmicrosoft.com".
  -	Ваш `ida:Audience` — это URI идентификатора приложения, введенное на портале Azure.

## *3. Настройка клиентского приложения и запуск службы*
Чтобы увидеть службу To Do List в действии, необходимо настроить To Do List Client для получения маркеров Azure AD и выполнения вызовов службы.

- Перейдите на [Портал управления Azure](https://manage.windowsazure.com)
- Создайте новое приложение в клиенте Azure AD и выберите **Собственное клиентское приложение** в конечном запросе.
    -	**Имя** приложения является его описанием для конечных пользователей.
    -	В качестве **URI переадресации** введите значение `http://TodoListClient/`.
- После завершения регистрации служба Azure AD присваивает приложению уникальный **Идентификатор клиента**. Это значение понадобится далее, поэтому его следует скопировать с вкладки "Настройка".
- Также на вкладке **Настройка** найдите раздел "Разрешения для других приложений". Щелкните "Добавить приложение". Из раскрывающегося списка "Показать" выберите "Все приложения" и щелкните флажок вверху. Найдите и щелкните службу To Do List, затем щелкните флажок внизу, чтобы добавить приложение. Выберите "Доступ к ’Служба To Do List’" из выпадающего меню "Делегированные разрешения" и сохраните конфигурацию.


- В Visual Studio откройте файл `App.config` проекта TodoListClient и введите значения конфигурации в раздел `<appSettings>`.
  -	`ida:Tenant` — это имя вашего клиента Azure AD, например "contoso.onmicrosoft.com".
  -	`ida:ClientId` — это идентификатор вашего приложения, скопированный из портала Azure.
  -	`todo:TodoListResourceId` — это код URI идентификатора приложения, введенный на портале Azure.

Наконец, выполните очистку, соберите и запустите каждый из проектов. Если вы еще этого не сделали, создайте нового пользователя в своем клиенте с доменом *.onmicrosoft.com. Войдите в клиент To Do List от имени данного пользователя и добавьте несколько задач в список To Do List.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Теперь можно приступить к дополнительным сценариям идентификации.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->