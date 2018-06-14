---
title: Приступая к работе с веб-API .NET для Azure AD | Документация Майкрософт
description: Практическое руководство по созданию веб-API для .NET MVC, который интегрируется с Azure AD для аутентификации и авторизации.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: aa527f66035ce8ea95ecdf405ef307c1202a92a6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158294"
---
# <a name="azure-ad-net-web-api-getting-started"></a>Приступая к работе с Azure AD для веб-API .NET
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Если вы создаете приложение, которое предоставляет доступ к конфиденциальным ресурсам, то необходимо знать, как предотвратить несанкционированный доступ к этим ресурсам.
Azure Active Directory (Azure AD) упрощает для разработчика процесс защиты веб-API с помощью токенов носителя OAuth 2.0 — потребуется добавить всего несколько строк кода.

В веб-приложениях ASP.NET эту защиту можно реализовать с помощью поддерживаемого сообществом ПО промежуточного слоя OWIN корпорации Майкрософт, включенного в .NET Framework 4.5. Мы будем использовать OWIN для создания веб-API To Do List, который:

* указывает, какие API защищены;
* проверяет, содержат ли вызовы веб-API действительный маркер доступа.

Для сборки API списка дел сначала необходимо выполнить следующие действия:

1. зарегистрировать приложение в Azure AD;
2. Настроить приложение для использования конвейера аутентификации OWIN.
3. Настроить клиентское приложение для вызова веб-API.

Чтобы начать работу, [скачайте схему приложения](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) или [скачайте готовый пример](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Каждый из них является решением Visual Studio 2013. Вам также потребуется клиент Azure AD для регистрации приложения. Если у вас нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Шаг 1. Регистрация приложения в Azure AD
Для защиты приложения необходимо сначала создать приложение в клиенте и предоставить Azure AD некоторую важную информации.

1. Войдите на [портале Azure](https://portal.azure.com).

2. На верхней панели щелкните свою учетную запись. В списке **Каталог** выберите клиент Azure AD, в котором будет зарегистрировано приложение.

3. В области слева щелкните **Больше служб** и выберите **Azure Active Directory**.

4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.

5. Следуйте инструкциям на экране, а затем создайте **веб-приложение и (или) веб-API**.
  * **Имя** приложения является его описанием для пользователей. Введите **Служба списка дел**.
  * **URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения любых токенов, запрошенных приложением. Укажите `https://localhost:44321/` для этого параметра.

6. На странице **Параметры** -> **Свойства** приложения обновите его универсальный код ресурса (URI) идентификатора. Введите идентификатор конкретного клиента. Например, введите `https://contoso.onmicrosoft.com/TodoListService`.

7. Сохраните конфигурацию. Не закрывайте портал, так как через некоторое время потребуется зарегистрировать клиентское приложение.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Шаг 2. Настройка приложения для использования конвейера аутентификации OWIN
Чтобы проверять входящие запросы и токены, необходимо настроить приложение для взаимодействия с Azure AD.

1. Для начала откройте решение и добавьте пакеты NuGet для ПО промежуточного слоя OWIN в проект TodoListService с помощью консоли диспетчера пакетов.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Добавьте класс OWIN Startup в проект TodoListService под именем `Startup.cs`.  Щелкните проект правой кнопкой мыши и выберите **Добавить** > **Новый элемент**, после чего найдите **OWIN**. При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(…)` .

3. Замените объявление класса `public partial class Startup`. Часть этого класса уже была реализована в другом файле. В методе `Configuration(…)` отправьте вызов в `ConfgureAuth(…)`, чтобы настроить аутентификацию для веб-приложения.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(…)`. Параметры, указанные в `WindowsAzureActiveDirectoryBearerAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD.

    ```csharp
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

5. Теперь можно использовать атрибуты `[Authorize]` для защиты контроллеров и действий с помощью аутентификации носителей JSON Web Token (JWT). Снабдите класс `Controllers\TodoListController.cs` тегом авторизации. Пользователь будет вынужден войти в систему, прежде чем сможет обратиться к этой странице.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Когда авторизованный вызывающий объект успешно вызывает один из интерфейсов API `TodoListController` , может потребоваться доступ к сведениям о вызывающем объекте. OWIN предоставляет доступ к утверждениям внутри маркера защиты посредством объекта `ClaimsPrincpal` .  

6. Общим требованием для веб-API является проверка областей (scope) в токене. Это гарантирует, что пользователь предоставил разрешения, необходимые для доступа к службе списка дел.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
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

7. Откройте файл `web.config` в корне проекта TodoListService и введите значения конфигурации в разделе `<appSettings>`.
  * `ida:Tenant` — это имя вашего клиента Azure AD, например contoso.onmicrosoft.com.
  * `ida:Audience` — это URI идентификатора приложения, введенное на портале Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Шаг 3. Настройка клиентского приложения и запуск службы
Чтобы увидеть службу списка дел в действии, необходимо настроить клиент списка дел для получения токенов от Azure AD и выполнения вызовов службы.

1. Вернитесь на [портал Azure](https://portal.azure.com).

2. Создайте новое приложение в клиенте Azure AD и выберите **Собственное клиентское приложение** в конечном запросе.
  * **Имя** приложения является его описанием для пользователей.
  * В качестве **URI перенаправления** введите значение `http://TodoListClient/`.

3. Когда вы завершите регистрацию, Azure AD присвоит приложению уникальный идентификатор приложения. Это значение вам понадобится для следующих действий, поэтому скопируйте его на странице приложения.

4. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**. Найдите и выберите службу списка дел, добавьте разрешение **Access TodoListService** (Доступ к службе списка дел) в списке **Делегированные разрешения**, а затем нажмите кнопку **Готово**.

5. В Visual Studio откройте файл `App.config` в проекте TodoListClient и введите значения конфигурации в раздел `<appSettings>`.

  * `ida:Tenant` — это имя вашего клиента Azure AD, например contoso.onmicrosoft.com.
  * `ida:ClientId` — это идентификатор приложения, скопированный с портала Azure.
  * `todo:TodoListResourceId` — это URI идентификатора приложения службы списка дел, введенный на портале Azure.

## <a name="next-steps"></a>Дополнительная информация
Наконец, выполните очистку и сборку, а затем запустите каждый из проектов. Если вы еще этого не сделали, создайте нового пользователя в своем клиенте с доменом *.onmicrosoft.com. Войдите в клиент списка дел от имени данного пользователя и добавьте несколько задач в его список дел.

Готовый пример (для которого лишь осталось задать конфигурацию) можно найти в [репозитории GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Теперь можно приступить к другим сценариям идентификации.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
