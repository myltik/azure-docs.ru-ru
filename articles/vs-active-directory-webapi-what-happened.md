<properties 
	pageTitle="" 
	description="Описание изменений в проекте Visual Studio после завершения работы мастера Azure Active Directory" 
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
> - [Приступая к работе](vs-active-directory-webapi-getting-started.md)
> - [Что произошло?](vs-active-directory-webapi-what-happened.md)

###<span id="whathappened">Что произошло с моим проектом?</span>

Добавлены ссылки. 

#####Ссылки на пакет NuGet 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Ссылки на .NET 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt` 

#####В проект добавлены файлы с кодом 

К проекту добавлен класс запуска проверки подлинности **App_Start/Startup.Auth.cs**, содержащий логику запуска для проверки подлинности на основе Azure AD. 

#####В проект добавлен код запуска 

Если в проекте уже был класс запуска, к нему добавляется обновленный метод **Configuration**, содержащий вызов ConfigureAuth(app). В противном случае в проект добавляется класс запуска. 


#####Один из файлов app.config или web.config имеет новое значение конфигурации.

Были добавлены следующие записи конфигурации. 
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

###Создано приложение Azure AD 

В каталоге, выбранном в мастере, создано приложение Azure AD.


[Дополнительные сведения о службе Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
