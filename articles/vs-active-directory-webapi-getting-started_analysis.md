<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Что произошло?

В проект добавлены ссылки

###### Ссылки на пакет NuGet

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### Ссылки на .NET

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### В проект добавлены файлы с кодом

Класс запуска проверки подлинности App\_Start/Startup.Auth.cs добавлен в проект, содержащий логикой запуска для проверки подлинности Azure AD.

###### В проект добавлен код запуска

Если в проекте уже был класс запуска, к нему добавляется обновленный метод Configuration(), содержащий вызов ConfigureAuth(app). В противном случает в проект добавляется класс запуска.

###### Один из файлов app.config или web.config имеет новое значение конфигурации

Были добавлены следующие записи конфигурации.
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

### Было создано приложение Azure Active Directory (AD)

Было создано приложение Azure AD в указанно в мастере каталоге.

### Начало работы с Azure Active Directory (AD)

Варианты возможных действий с добавленным кодом.

###### Требование проверки подлинности для доступа к контроллерам

Во всем контроллерам в проекте добавлен атрибут Authorize. Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к интерфейсам API, которые определяются этими контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут. Если необходимо задать разрешения на более детальном уровне, примените атрибут к каждому методу, требующему проверки подлинности, а не к классу контроллера.

