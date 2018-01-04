---
title: "Приступая к работе веб-сервера Azure AD v1 ASP.NET | Документы Microsoft"
description: "Реализация входа Майкрософт в решении ASP.NET с традиционным браузерным приложением с использованием стандарта OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/08/2017
ms.author: andret
ms.openlocfilehash: b23afd26f7ac1828381a0410d2455206c8f43c88
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Добавление возможности входа в веб-приложение ASP.NET с помощью учетной записи Майкрософт

В этом руководстве описывается, как реализовать вход через учетную запись Майкрософт, используя решение ASP.NET MVC с традиционным браузерным приложением с помощью OpenID Connect. 

В конце данного руководства, приложение будет принимать входы работу и учебную учетные записи из организаций с интегрированным с Azure Active Directory.

> [!NOTE]
> Эта интерактивная программа установки поможет настроить входы из рабочих и учебную учетные записи в приложении ASP.NET. Если вы планируете включить входа в систему для личных учетных записей, а также работу и учебную учетные записи, можно использовать [v2 конечной точки](../active-directory-v2-compare.md). . В разделе [этого приложения ASP.NET интерактивной программы установки для конечной точки v2](./active-directory-aspnetwebapp.md) производительны, чем [в этом документе](../active-directory-v2-limitations.md) объясняющий текущих ограничений на конечной точке версии 2.
<br/><br/>

<!--separator-->

> Для работы с этим руководством требуется Visual Studio 2015 с обновлением 3 или Visual Studio 2017.  У вас его нет?  [Скачайте Visual Studio 2017 бесплатно.](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Принцип работы с руководством

![Принцип работы с руководством](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

Это руководство основано на сценарии, в котором браузер обращается к веб-сайту ASP.NET. При этом пользователь должен выполнить проверку подлинности с помощью кнопки входа. В этом сценарии большая часть работы по отображению веб-страницы происходит на стороне сервера.

> [!NOTE]
> Интерактивной программы установки показано, как выполнить вход пользователей на веб-приложения ASP.NET, начиная с пустого шаблона и включают действия, такие как добавление знак в кнопки и каждый контроллер и методов, при также объясняющий некоторые основные понятия. Кроме того, можно также создать проект для входа в Azure Active Directory пользователей (рабочих и учебную учетные записи) с помощью [веб-шаблона Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) и выбрав *учетные записи организации* и затем один из параметров облака - этот параметр использует шаблон обширные возможности с дополнительных контроллеров, методы и представления.

## <a name="libraries"></a>Библиотеки

В этом руководстве использует следующие пакеты:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Промежуточный слой, который позволяет приложению использовать OpenIDConnect для проверки подлинности.|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Промежуточный слой, который позволяет приложению поддерживать пользовательский сеанс с помощью файлов cookie.|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Позволяет приложениям на основе OWIN работать на платформе IIS с помощью конвейера запросов ASP.NET.|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Настройка проекта

В этом разделе описаны шаги по установке и настройке конвейера проверки подлинности через промежуточный слой OWIN в проекте ASP.NET с помощью OpenID Connect. 

> Предпочитаете скачать этот пример проекта Visual Studio? [Скачайте проект](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) и перейдите к [настройке](#configure-your-webconfig-and-register-an-application), чтобы настроить пример кода перед выполнением.

## <a name="create-your-aspnet-project"></a>Создание проекта ASP.NET
1. В Visual Studio выберите `File` > `New` > `Project`.<br/>
2. В разделе *Visual C#\Web* выберите `ASP.NET Web Application (.NET Framework)`.
3. Присвойте имя приложению и нажмите кнопку *ОК*.
4. Выберите `Empty` и установите флажок, чтобы добавить `MVC` ссылки

## <a name="add-authentication-components"></a>Добавление компонентов проверки подлинности

1. В Visual Studio выберите `Tools` > `Nuget Package Manager` > `Package Manager Console`.
2. Добавьте *пакеты NuGet для промежуточного слоя OWIN*, введя следующие команды в окне консоли диспетчера пакетов:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Об этих пакетах
>Библиотеки, приведенные выше, позволяют выполнять единый вход с помощью OpenID Connect через проверку подлинности на основе файлов cookie. После завершения проверки подлинности и отправки приложению маркера, представляющего пользователя, промежуточный слой OWIN создаст файл cookie сеанса. Браузер затем использует этот файл cookie при последующих запросах, поэтому пользователю не нужны принудительную проверку подлинности и требуется дополнительная проверка.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Настройка конвейера для проверки подлинности
Следующие действия используются для создания по промежуточного слоя OWIN *класс запуска* для настройки проверки подлинности OpenID Connect. Этот класс будет выполнена автоматически.

> [!TIP]
> Если проект не содержит файл `Startup.cs` в корневой папке, сделайте следующее:<br/>
> 1. Щелкните правой кнопкой мыши в корневой папке проекта: >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Назовите класс `Startup.cs`.<br/>
>
>> Выберите класс запуска OWIN, а не стандартный класс C#. Если вы выбрали нужный класс, вы должны увидеть `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` над пространством имен.


1. Добавить *OWIN* и *Microsoft.IdentityModel* пространства имен `Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Замените класс запуска с помощью следующего кода:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> Параметры, указанные в *OpenIDConnectAuthenticationOptions*, будут служить координатами приложения для взаимодействия с Azure AD. Поскольку OpenID Connect по промежуточного слоя использует файлы cookie, необходимо также настроить файл cookie проверки подлинности, как показано в предыдущем коде. Значение *ValidateIssuer* сообщает OpenIdConnect не ограничивать доступ для определенной организации.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Добавление контроллера для обработки запросов на вход и выход

На этом шаге рассматривается создание контроллера для предоставления методов входа и выхода.

1.  Щелкните правой кнопкой мыши `Controllers` и выберите пункт`Add` > `Controller`
2.  Выберите `MVC (.NET version) Controller – Empty`.
3.  Щелкните *Добавить*.
4.  Присвойте имя контроллеру `HomeController`, а затем щелкните *Добавить*.
5.  Добавить *OWIN* пространства имен для класса:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Добавьте следующие методы для обработки входа и выхода из системы к контроллеру, инициирует запрос проверки подлинности через код:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Создание домашней страницы приложения для выполнения входа пользователями с помощью кнопки "Вход"

Создайте в Visual Studio новое представление, где будет содержаться кнопка входа, а также будут отображаться сведения о пользователе после проверки подлинности:

1.  Щелкните правой кнопкой мыши `Views\Home` и выберите пункт`Add View`
2.  Назовите его `Index`.
3.  Добавьте следующий код HTML, включающий кнопку входа, в файл:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> С помощью этой страницы можно добавить кнопку входа в формате SVG с черным фоном:<br/>![Вход с учетной записью Майкрософт](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> Дополнительные кнопки входа, см. в [эту страницу](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "рекомендации по фирменной символике").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Отображение утверждения пользователя путем добавления контроллера
Этот контроллер демонстрирует использование атрибута `[Authorize]` для защиты. Этот атрибут разрешает доступ к контроллеру только тем пользователям, которые прошли проверку подлинности. В следующем коде используется атрибута для отображения утверждения пользователей, которые были получены в рамках входа в систему.

1.  Щелкните правой кнопкой мыши `Controllers` папки:`Add` > `Controller`
2.  Выберите `MVC {version} Controller – Empty`.
3.  Щелкните *Добавить*.
4.  Назовите класс `ClaimsController`.
5.  Замените код класса контроллера следующий код — это добавляет `[Authorize]` к классу атрибут:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Из-за использования атрибута `[Authorize]` все методы контроллера выполняются, только если пользователь прошел проверку подлинности. Если пользователь не прошел проверку подлинности и пытается получить доступ к контроллеру, OWIN инициирует запрос проверки подлинности и принуждения пользователя для проверки подлинности. Приведенный выше код просматривает коллекцию утверждений пользователя для конкретных атрибутов, включенных в маркер пользователя. Эти атрибуты включают имя и фамилию пользователя, а также субъект глобального идентификатора пользователя. Этот субъект содержит *идентификатор клиента*, который представляет идентификатор организации пользователя. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Создание представления для отображения утверждений пользователя

В Visual Studio создайте новое представление для отображения утверждений пользователя на веб-странице:

1.  Щелкните правой кнопкой мыши `Views\Claims` папки и:`Add View`
2.  Назовите его `Index`.
3.  Добавьте в файл следующий код HTML:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Настройка вашего *web.config* и регистрация приложения

1. В Visual Studio, добавьте следующий код в `web.config` (расположенный в корневой папке) в разделе `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. В обозревателе решений выберите проект и посмотрите <i>свойства</i> окна (Если вы не видите окно «Свойства», нажмите клавишу F4)
3. Для параметра "SSL включен" измените значение на <code>True</code>.
4. Скопируйте URL-адрес SSL проекта в буфер обмена:<br/><br/>![Свойства проекта](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. В <code>web.config</code>, замените <code>Enter_the_Redirect_URL_here</code> на URL-адрес SSL проекта 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Зарегистрировать приложение на портале Azure, а затем добавить в его данных *web.config*

1. Последовательно выберите пункты [портал Microsoft Azure — регистрации приложения](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) для регистрации приложения
2. Выберите `New application registration`
3. Введите имя для вашего приложения
4. Вставьте в проект Visual Studio *URL-адрес SSL* в `Sign-on URL` (этот URL-адрес также добавляется автоматически в список URL-адреса ответа для приложения, вы регистрируете)
5. Щелкните `Create` для регистрации приложения. Это действие выполняется переход в списке приложений
6. Теперь поиска и/или выберите приложение, вы только что создали его свойств
7. Скопируйте идентификатор guid в `Application ID` в буфер обмена
8. Вернитесь в Visual Studio и, в `web.config`, замените `Enter_the_Application_Id_here` с Идентификатором приложения из приложения, только что зарегистрирован

> [!TIP]
> Если ваша учетная запись настроена на доступ к несколько каталогов, убедитесь, что вы выбрали каталога для организации требуется его для регистрации, щелкнув имя учетной записи в верхней правой на портале Azure и последующей проверки Выбранный каталог в соответствии:<br/>![Выбор каталога](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>Настроить параметры входа

Можно настроить приложение, чтобы разрешить только пользователям, принадлежащим к одной организации Azure Active Directory экземпляр вход в систему, или примите входы из пользователей, принадлежащих к любой организации. Следуйте инструкциям по одной из следующих вариантов:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Настроить приложение так, чтобы разрешить входы работу и учебные учетные записи из любого компании или организации (мультитенантное)

Выполните следующие действия, если вы хотите принять входы работу и учебные учетные записи из любого компания или организация, интегрированное с Azure Active Directory. Это очень распространенный сценарий для *приложений SaaS*:

1. Вернитесь к [портал Microsoft Azure — регистрации приложения](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) и найдите только что зарегистрированного приложения
2. В разделе `All Settings` выберите`Properties`
3. Изменение `Multi-tenanted` свойства `Yes` и нажмите кнопку`Save`

Дополнительные сведения о данном параметре и концепцию приложения с несколькими клиентами см. в разделе [в этой статье](../active-directory-devhowto-multi-tenant-overview.md "обзор нескольких клиентов").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Запретить пользователям организации только один экземпляр Active Directory для входа приложение (одного клиента)

Этот параметр представляет собой обычный сценарий для *бизнес-приложений*: Если требуется, чтобы приложение использует для входа в систему только из учетных записей, относящихся к определенному экземпляру Azure Active Directory (включая *гостевой учетной записи*этого экземпляра), замените `Tenant` параметр в *web.config* из `Common` организации — именем клиента примере *contoso.onmicrosoft.com*. После этого изменить `ValidateIssuer` аргумента в вашей [ *класс запуска OWIN* ](#configure-the-authentication-pipeline) для `true`.

Чтобы разрешить вход для пользователей из списка определенных организаций, задайте параметру `ValidateIssuer` значение true и используйте параметр `ValidIssuers`, чтобы указать список организаций.

Другой вариант заключается в реализации пользовательского метода для проверки с помощью издателей *IssuerValidator* параметра. Дополнительные сведения о `TokenValidationParameters`, см. в разделе [в этой статье MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "статьи TokenValidationParameters MSDN").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->
## <a name="test-your-code"></a>Тестирование кода

Щелкните `F5`, чтобы запустить проект в Visual Studio. Откроется браузер и перехода к *http://localhost: {port}* показывающий *вход с Microsoft* кнопки. Щелкните ее, чтобы выполнить вход.

Когда все будет готово для тестирования, используйте для входа в рабочую учетную запись (Azure Active Directory). 

![Войти в окне браузера Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![Войти в окне браузера Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Ожидаемые результаты
После входа в систему пользователь перенаправляется на домашнюю страницу веб-сайта, который является URL-адрес HTTPS, указываются в регистрационных данных приложения на портале регистрации приложения Microsoft. Эта страница будет выглядеть *Hello {User}* и ссылку, чтобы выйти из системы и ссылку, чтобы увидеть утверждений пользователя — которого является ссылкой на контроллер Authorize, созданного ранее.

### <a name="see-users-claims"></a>Просмотр утверждений пользователя
Выберите гиперссылку, чтобы просмотреть утверждения пользователя. Это действие позволяет контроллер и представление, которое доступно только для пользователей, которые выполняют проверку подлинности.

#### <a name="expected-results"></a>Ожидаемые результаты
 Вы должны увидеть таблицу, содержащую основные свойства пользователя, выполнившего вход.

| Свойство | Значение | ОПИСАНИЕ|
|---|---|---|
| ИМЯ | {Полное имя пользователя} | Имя и фамилия пользователя
|Имя пользователя | <span>user@domain.com</span>| Имя пользователя, используемое для его идентификации
| Субъект| {Тема}|Строка для уникальной идентификации входа пользователя в Интернете|
| Tenant ID| Guid| Значение *guid* используется для однозначного представления организации пользователя Azure Active Directory.|

Кроме того вы видите таблицу, включая все утверждения пользователей, включенных в запрос проверки подлинности. Список всех утверждений в маркер идентификатора и их описание см. в разделе это [статьи](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "список утверждения в маркер идентификатора").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Проверка доступа к методу, включающему атрибут *[Authorize]* (необязательно)
На этом шаге проверить доступ к контроллеру как анонимный пользователь утверждений:<br/>
Выберите ссылку для выхода пользователя и завершения процесса выхода.<br/>
Теперь в окне браузера введите http://localhost: {port} / утверждений для вашего контроллера с защитой доступа к `[Authorize]` атрибута

#### <a name="expected-results"></a>Ожидаемые результаты
Вы должны получить запрос на выполнение проверки подлинности, чтобы открылось представление.

## <a name="additional-information"></a>Дополнительная информация

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Защита всего веб-сайта
Для защиты всего веб-сайта добавьте `AuthorizeAttribute` для `GlobalFilters` в метод `Global.asax` `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->