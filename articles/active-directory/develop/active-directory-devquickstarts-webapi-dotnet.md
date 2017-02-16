---
title: "Приступая к работе с Azure AD для .NET | Документация Майкрософт"
description: "Практическое руководство по созданию веб-интерфейса API для .NET MVC, который интегрируется с Azure AD для выполнения проверки подлинности и авторизации."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: c401d473d3951ed5853170e8761a09a915329164


---
# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Защита веб-интерфейса API с помощью маркеров носителя из Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Если вы создаете приложение, которое предоставляет доступ к конфиденциальным ресурсам, необходимо знать, как защитить эти ресурсы от несанкционированного доступа.
Azure AD упрощает для разработчика процесс защиты веб-интерфейса API с помощью маркеров доступа OAuth Bearer 2.0 — потребуется добавить всего несколько строк кода.

В веб-приложениях ASP.NET это можно сделать с помощью реализации Майкрософт промежуточного слоя OWIN (открытого интерфейса для .NET, поддерживаемого сообществом разработчиков), включенного в .NET Framework 4.5.  Мы будем использовать OWIN для создания веб-API To Do List, который:

* указывает, какие API защищены;
* проверяет, содержат ли вызовы веб-API действительный маркер доступа.

Для этого вам необходимо:

1. зарегистрировать приложение в Azure AD;
2. Настроить приложение для использования конвейера проверки подлинности OWIN.
3. Настроить клиентское приложение для вызова методов веб-API приложения To Do List.

Чтобы начать работу, [скачайте схему приложения](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) или [скачайте готовый пример](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Каждый из них является решением Visual Studio 2013.  Вам также потребуется клиент Azure AD для регистрации приложения.  Если у вас нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## <a name="1----register-an-application-with-azure-ad"></a>1.    Регистрация приложения в Azure AD
Для защиты приложения необходимо сначала создать приложение в клиенте и предоставить Azure AD некоторую важную информации.

1. Войдите на [портал Azure](https://portal.azure.com).
2. На верхней панели щелкните учетную запись и в списке **Каталог** выберите клиент Active Directory, в котором хотите зарегистрировать приложение.
3. В левой области навигации щелкните **Другие службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений** и нажмите кнопку **Добавить**.
5. Следуйте инструкциям на экране, а затем создайте новое **веб-приложение и/или WebAPI**.
  * **Имя** приложения является его описанием для конечных пользователей.  Введите "Служба To Do List".
  * **URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения любых маркеров, запрошенных приложением. Укажите `https://localhost:44321/` для этого параметра.
  * В поле **AppID URI** (URI идентификатора приложения) введите идентификатор конкретного клиента, например `https://contoso.onmicrosoft.com/TodoListService`
6. Сохраните конфигурацию.  Не закрывайте портал, так как через некоторое время потребуется зарегистрировать клиентское приложение.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>2) Настройка в приложении проверки подлинности OWIN
Теперь после регистрации приложения в Azure AD необходимо настроить приложение для взаимодействия с Azure AD, чтобы выполнять проверку входящих запросов и маркеров.

* Для начала откройте решение и добавьте пакеты NuGet промежуточного слоя OWIN в проект TodoListService с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

* Добавьте класс OWIN Startup в проект TodoListService под именем `Startup.cs`.  Щелкните правой кнопкой мыши проект, выберите в контекстном меню пункты **Добавить** --> **Новый элемент** и найдите OWIN.  При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(…)` .
* Замените объявление класса на `public partial class Startup` — часть этого класса уже была реализована в другом файле.  В методе `Configuration(…)` добавьте вызов ConfgureAuth(...), чтобы настроить проверку подлинности для веб-приложения.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(…)`.  Параметры, указанные в `WindowsAzureActiveDirectoryBearerAuthenticationOptions` , будут служить координатами приложения для взаимодействия с Azure AD.

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

* Теперь можно использовать атрибуты `[Authorize]` для защиты контроллеров и действий с помощью проверки подлинности JWT.  Снабдите класс `Controllers\TodoListController.cs` тегом авторизации.  Пользователь будет вынужден войти в систему, прежде чем сможет обратиться к этой странице.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

* Когда авторизованный вызывающий объект успешно вызывает один из интерфейсов API `TodoListController` , может потребоваться доступ к сведениям о вызывающем объекте.  OWIN предоставляет доступ к утверждениям внутри маркера защиты посредством объекта `ClaimsPrincpal` .  
* Общим требованием для веб-интерфейсов API является проверка областей (scope) в маркере, чтобы гарантировать, что конечный пользователь предоставил разрешения, необходимые для доступа к службе To Do List:

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

* Наконец, откройте файл `web.config` в корне проекта TodoListService, а затем введите значения конфигурации в разделе `<appSettings>`.
  * `ida:Tenant` — это имя вашего клиента Azure AD, например "contoso.onmicrosoft.com".
  * Ваш `ida:Audience` — это URI идентификатора приложения, введенное на портале Azure.

## <a name="3-configure-a-client-application--run-the-service"></a>3. Настройка клиентского приложения и запуск службы
Чтобы увидеть службу To Do List в действии, необходимо настроить To Do List Client для получения маркеров Azure AD и выполнения вызовов службы.

* Вернитесь на [портал Azure](https://portal.azure.com)
* Создайте новое приложение в клиенте Azure AD и выберите **Собственное клиентское приложение** в конечном запросе.
  * **Имя** приложения отображает его описание конечным пользователям.
  * В качестве **URI перенаправления** введите значение `http://TodoListClient/`.
* После завершения регистрации служба Azure AD присваивает приложению уникальный **идентификатор приложения**. Это значение вам понадобится для следующих действий, поэтому скопируйте его на странице приложения.
* На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**.  Выберите TodoListService, добавьте разрешение **Access TodoListService** (Доступ к TodoListService) в списке **Делегированные разрешения** и щелкните **Готово**.

* В Visual Studio откройте файл `App.config` проекта TodoListClient и введите значения конфигурации в раздел `<appSettings>`.
  
  * `ida:Tenant` — это имя вашего клиента Azure AD, например "contoso.onmicrosoft.com".
  * `ida:ClientId` — это идентификатор вашего приложения, скопированный из портала Azure.
  * `todo:TodoListResourceId` — это код URI идентификатора приложения, введенный на портале Azure.

Наконец, выполните очистку, соберите и запустите каждый из проектов.  Если вы еще этого не сделали, создайте нового пользователя в своем клиенте с доменом *.onmicrosoft.com.  Войдите в клиент To Do List от имени данного пользователя и добавьте несколько задач в список To Do List.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Теперь можно приступить к дополнительным сценариям идентификации.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


