<properties
	pageTitle="Что произошло с моим проектом MVC в подключенной службе Visual Studio Azure Active Directory | Microsoft Azure"
	description="Описывает, что происходит с проектом MVC при подключении к приложению Azure AD с помощью подключенных служб Visual Studio"
	services="active-directory"
	documentationCenter="na"
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# Что произошло с моим проектом MVC в подключенной службе Visual Studio Azure Active Directory?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)



## Добавлены ссылки

### Ссылки на пакет NuGet

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### Ссылки на .NET

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## Добавлен код

### В проект добавлены файлы с кодом

К проекту добавлен класс запуска проверки подлинности **App\_Start/Startup.Auth.cs**, содержащий логику запуска для проверки подлинности на основе Azure AD. Кроме того, добавлен класс контролера Controllers/AccountController.cs, который содержит методы **SignIn()** и **SignOut()**. Наконец, добавлено частичное представление **Views/Shared/\_LoginPartial.cshtml**, содержащее ссылку действия для SignIn и SignOut.

### В проект добавлен код запуска

Если в проекте уже есть класс запуска, метод **Configuration** обновляется, чтобы добавить вызов **ConfigureAuth(app)**. В противном случае в проект добавляется класс запуска.

### В файл app.config или web.config добавлены значения конфигурации

Были добавлены следующие записи конфигурации.


	<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
	    <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
	    <add key="ida:Domain" value="The selected Azure AD Domain" />
	    <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
	    <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
	</appSettings>

### Создано приложение Azure Active Directory (AD)
В каталоге, выбранном в мастере, создано приложение Azure AD.

##Какие дополнительные изменения внесены в мой проект после установки флажка *Отключить проверку подлинности для отдельных учетных записей пользователей*?
Удалены ссылки на пакет NuGet. Для файлов созданы резервные копии, а сами файлы удалены. В зависимости от состояния проекта вам, возможно, придется вручную удалить дополнительные ссылки или файлы либо изменить код соответствующим образом.

### Ссылки на существующие пакеты NuGet удалены

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### Для существующих файлов кода созданы резервные копии, а сами файлы удалены.

Для каждого из следующих файлов создана резервная копия, а сами файлы удалены из проекта. Резервные копии файлов расположены в папке Backup в корневом каталоге проекта.

- **App\_Start\\IdentityConfig.cs**
- **Controllers\\ManageController.cs**
- **Models\\IdentityModels.cs**
- **Models\\ManageViewModels.cs**

### Для существующих файлов кода созданы резервные копии

Для каждого из следующих файлов создана резервная копия, после чего файлы были заменены. Резервные копии файлов расположены в папке Backup в корневом каталоге проекта.

- **Startup.cs.**
- **App\_Start\\Startup.Auth.cs**
- **Controllers\\AccountController.cs**
- **Views\\Shared\_LoginPartial.cshtml**

## Какие дополнительные изменения внесены в мой проект после установки флажка *Чтение данных каталога*?

Добавлены дополнительные ссылки.

###Дополнительные ссылки на пакет NuGet

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###Дополнительные ссылки на .NET

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###В проект добавлены дополнительные файлы с кодом

Для поддержки кэширования маркеров добавлены два файла: **Models\\ADALTokenCache.cs** и **Models\\ApplicationDbContext.cs**. Чтобы проиллюстрировать получение доступа к информации о пользовательском профиле с помощью интерфейсов API Graph Azure, были добавлены дополнительные контроллер и представление. Они содержатся в файлах **Controllers\\UserProfileController.cs** и **Views\\UserProfile\\Index.cshtml**.

###В проект добавлен дополнительный код запуска

В файле **startup.auth.cs** в элемент **Notifications**, который содержится в **OpenIdConnectAuthenticationOptions**, добавлен новый объект **OpenIdConnectAuthenticationNotifications**. Он был добавлен для включения возможности получения кода OAuth и его обмена на маркер доступа.

###В файл app.config или web.config внесены дополнительные изменения

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


###Обновлено приложение Azure Active Directory
В приложение Azure Active Directory добавлено разрешение *Чтение данных каталога*. Кроме того, создан дополнительный ключ, который затем использовался в качестве параметра *ida:ClientSecret* в файле **web.config**.

[Дополнительная информация о службе Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=Sept15_HO4-->