<properties 
	pageTitle="Приступая к работе с проверкой подлинности на основе Active Directory. Что произошло?" 
	description="Описание произошедшего с проектом Azure Active Directory в Visual Studio" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-active-directory-dotnet-getting-started/)
> - [Что произошло?](/documentation/articles/vs-active-directory-dotnet-what-happened/)

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
- `System`
- `System.Data`
- `System.Drawing`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####В проект добавлены файлы с кодом 

Класс запуска проверки подлинности App_Start/Startup.Auth.cs добавлен в проект, содержащий логику запуска для проверки подлинности Azure AD. Также добавлен класс контроллера Controllers/AccountController.cs, который содержит методы SignIn() и SignOut(). Наконец, добавлено частичное представление Views/Shared/_LoginPartial.cshtml, содержащее ссылку действия для SignIn и SignOut. 

#####В проект добавлен код запуска
 
Если в проекте уже был класс запуска, к нему добавляется обновленный метод **Configuration**, содержащий вызов ConfigureAuth(app). В противном случае в проект добавляется класс запуска. 

#####В файл app.config или web.config добавлены значения конфигурации 

Были добавлены следующие записи конфигурации. 
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####Создано приложение Azure Active Directory (AD) 
В каталоге, выбранном в мастере, создано приложение Azure AD. 

[Дополнительные сведения о службе Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
