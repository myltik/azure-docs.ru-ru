<properties 
	pageTitle="Приступая к работе с проверкой подлинности на основе Active Directory. Что произошло?" 
	description="Описание произошедшего с проектом Azure Active Directory в Visual Studio" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Что произошло с моим проектом?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)

###<span id="whathappened">Что произошло с моим проектом?</span>
 
Добавлены ссылки.

#####Ссылки на пакет NuGet

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Ссылки на .NET

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####В проект добавлены файлы с кодом 

В проект добавлен класс запуска аутентификации, `App_Start/Startup.Auth.cs`, содержащий логику запуска для аутентификации на основе Azure AD. Также добавлен класс контроллера, Controllers/AccountController.cs, который содержит методы `SignIn()` и `SignOut()`. Наконец, добавлено частичное представление, `Views/Shared/_LoginPartial.cshtml`, содержащее ссылку для действий входа и выхода.

#####В проект добавлен код запуска
 
Если в проекте уже есть класс запуска, метод **Configuration** обновляется для добавления вызова `ConfigureAuth(app)`. В противном случае в проект добавляется класс запуска.

#####В файл app.config или web.config добавлены значения конфигурации 

Были добавлены следующие записи конфигурации. <pre> `<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/" /> 
	    <add key="ida:Domain" value="The selected Azure AD Domain" />
	    <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####Создано приложение Azure Active Directory (AD) 
В каталоге, выбранном в мастере, создано приложение Azure AD.

###Какие дополнительные изменения были внесены в мой проект из-за установки флажка *Чтение данных каталога*?
Добавлены дополнительные ссылки.

#####Дополнительные ссылки на пакет NuGet

- `EntityFramework`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `System.Spatial`

#####Дополнительные ссылки на .NET

- `EntityFramework`
- `EntityFramework.SqlServer`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms`
- `System.Spatial`

#####В проект добавлены дополнительные файлы с кодом 

Добавлены два файла для поддержки кэширования маркеров: `Models\ADALTokenCache.cs` и `Models\ApplicationDbContext.cs`. Чтобы проиллюстрировать получение доступа к информации о пользовательском профиле с помощью интерфейсов API Graph Azure, были добавлены дополнительные контроллер и представление. Они содержатся в файлах `Controllers\UserProfileController.cs` и `Views\UserProfile\Index.cshtml`.

#####В проект добавлен дополнительный код запуска
 
В файле `startup.auth.cs` в элемент `Notifications`, который содержится в `OpenIdConnectAuthenticationOptions`, был добавлен новый объект `OpenIdConnectAuthenticationNotifications`. Он был добавлен для включения возможности получения кода OAuth и его обмена на маркер доступа.

#####В файл app.config или web.config внесены дополнительные изменения

Были добавлены следующие дополнительные записи конфигурации. <pre> `<appSettings>
	    <add key="Ida:ClientSecret" value="Your Azure AD App's new client secret" /> 
	</appSettings>` </pre>

Были добавлены следующие разделы конфигурации и строка подключения. <pre> `<configSections>
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
	</entityFramework>`</pre>


#####Обновлено приложение Azure Active Directory
Ваше приложение Azure Active Directory было обновлено для включения разрешения *Чтение данных каталога*, а также был создан дополнительный ключ, который затем использовался в качестве параметра *ClientSecret* в файле `web.config`.

[Дополнительная информация о службе Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=62-->