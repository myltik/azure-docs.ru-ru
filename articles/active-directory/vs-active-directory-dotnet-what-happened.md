---
title: "Что произошло с моим проектом MVC в подключенной службе Visual Studio Azure Active Directory | Документация Майкрософт"
description: "Описывает, что происходит с проектом MVC при подключении к приложению Azure AD с помощью подключенных служб Visual Studio"
services: active-directory
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: abe30bca0e1d305b5bd0300c63c037ced30be1f5


---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Что произошло с моим проектом MVC в подключенной службе Visual Studio Azure Active Directory?
> [!div class="op_single_selector"]
> * [Приступая к работе](vs-active-directory-dotnet-getting-started.md)
> * [Что произошло?](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Добавлены ссылки
### <a name="nuget-package-references"></a>Ссылки на пакет NuGet
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Ссылки на .NET
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel**
* **System.IdentityModel.Tokens.Jwt**
* **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Добавлен код
### <a name="code-files-were-added-to-your-project"></a>В проект добавлены файлы с кодом
К проекту добавлен класс запуска проверки подлинности **App_Start/Startup.Auth.cs**, содержащий логику запуска для проверки подлинности на основе Azure AD. Кроме того, добавлен класс контролера Controllers/AccountController.cs, который содержит методы **SignIn()** и **SignOut()**. Наконец, добавлено частичное представление **Views/Shared/_LoginPartial.cshtml**, содержащее ссылку действия для SignIn и SignOut.

### <a name="startup-code-was-added-to-your-project"></a>В проект добавлен код запуска
Если в проекте уже есть класс запуска, метод **Configuration** обновляется, чтобы добавить вызов **ConfigureAuth(app)**. В противном случае в проект добавляется класс запуска.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>В файл app.config или web.config добавлены значения конфигурации
Были добавлены следующие записи конфигурации.

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Было создано приложение Azure Active Directory (AD)
Было создано приложение Azure AD в указанно в мастере каталоге.

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Какие дополнительные изменения внесены в мой проект после установки флажка *Отключить проверку подлинности для отдельных учетных записей пользователей*?
Удалены ссылки на пакет NuGet. Для файлов созданы резервные копии, а сами файлы удалены. В зависимости от состояния проекта вам, возможно, придется вручную удалить дополнительные ссылки или файлы либо изменить код соответствующим образом.

### <a name="nuget-package-references-removed-for-those-present"></a>Ссылки на существующие пакеты NuGet удалены
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Для существующих файлов кода созданы резервные копии, а сами файлы удалены.
Для каждого из следующих файлов создана резервная копия, а сами файлы удалены из проекта. Резервные копии файлов расположены в папке Backup в корневом каталоге проекта.

* **App_Start\IdentityConfig.cs**
* **Controllers\ManageController.cs**
* **Models\IdentityModels.cs**
* **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Для существующих файлов кода созданы резервные копии
Для каждого из следующих файлов создана резервная копия, после чего файлы были заменены. Резервные копии файлов расположены в папке Backup в корневом каталоге проекта.

* **Startup.cs.**
* **App_Start\Startup.Auth.cs**
* **Controllers\AccountController.cs**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Какие дополнительные изменения внесены в мой проект после установки флажка *Чтение данных каталога*?
Добавлены дополнительные ссылки.

### <a name="additional-nuget-package-references"></a>Дополнительные ссылки на пакет NuGet
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>Дополнительные ссылки на .NET
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>В проект добавлены дополнительные файлы с кодом
Для поддержки кэширования маркеров добавлены два файла: **Models\ADALTokenCache.cs** и **Models\ApplicationDbContext.cs**.  Чтобы проиллюстрировать получение доступа к информации о пользовательском профиле с помощью интерфейсов API Graph Azure, были добавлены дополнительные контроллер и представление.  Они содержатся в файлах **Controllers\UserProfileController.cs** и **Views\UserProfile\Index.cshtml**.

### <a name="additional-startup-code-was-added-to-your-project"></a>В проект добавлен дополнительный код запуска
В файле **startup.auth.cs** в элемент **Notifications**, который содержится в **OpenIdConnectAuthenticationNotifications**, добавлен новый объект **OpenIdConnectAuthenticationOptions**.  Он был добавлен для включения возможности получения кода OAuth и его обмена на маркер доступа.

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>В файл app.config или web.config внесены дополнительные изменения
Были добавлены следующие дополнительные записи настройки.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Были добавлены следующие разделы конфигурации и строка подключения

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


### <a name="your-azure-active-directory-app-was-updated"></a>Обновлено приложение Azure Active Directory
В приложение Azure Active Directory добавлено разрешение *Чтение данных каталога*. Кроме того, создан дополнительный ключ, который затем использовался в качестве параметра *ida:ClientSecret* в файле **web.config**.

[Дополнительная информация о службе Azure Active Directory](https://azure.microsoft.com/services/active-directory/)




<!--HONumber=Nov16_HO3-->


