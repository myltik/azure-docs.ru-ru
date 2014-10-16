<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Что произошло?

В проект добавлены ссылки

###### Ссылки на пакет NuGet

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System.IdentityModel.Tokens.Jwt

###### Ссылки на .NET

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System, System.Data, System.Drawing, System.IdentityModel, System.IdentityModel.Tokens.Jwt, System.Runtime.Serialization

###### В проект добавлены файлы с кодом

Класс запуска проверки подлинности App\_Start/Startup.Auth.cs добавлен в проект, содержащий логикой запуска для проверки подлинности Azure AD. Также добавлен класс контроллера Controllers/AccountController.cs, который содержит методы SignIn() и SignOut(). Наконец, добавлено частичное представление Views/Shared/\_LoginPartial.cshtml, содержащее ссылку действия для SignIn и SignOut.

###### В проект добавлен код запуска

Если в проекте уже был класс запуска, к нему добавляется обновленный метод Configuration(), содержащий вызов ConfigureAuth(app). В противном случает в проект добавляется класс запуска.

###### Один из файлов app.config или web.config имеет новое значение конфигурации

Были добавлены следующие записи конфигурации.
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

###### Было создано приложение Azure Active Directory (AD)

Было создано приложение Azure AD в указанно в мастере каталоге.

## Начало работы с Azure Active Directory (AD)

Варианты возможных действий с добавленным кодом.

###### Требование проверки подлинности для доступа к контроллерам

Во всем контроллерам в проекте добавлен атрибут [Authorize]. Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут.

###### Добавление элементов управления SignIn и SignOut

Чтобы добавить элементы управления SignIn и SignOut, можно использовать частичное представление \_LoginPartial.cshtml для добавления функциональности одному из представлений. Вот пример функциональности, добавленной стандартному представлению \_Layout.cshtml view:
	<pre> 
	`<!DOCTYPE html> 
	<html> 
	<head> 
	    <meta charset="utf-8" /> 
	    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
	    <title>@ViewBag.Title - My ASP.NET Application</title> 
	    @Styles.Render("~/Content/css") 
	    @Scripts.Render("~/bundles/modernizr") 
	</head> 
	<body> 
	    <div class="navbar navbar-inverse navbar-fixed-top"> 
	        <div class="container"> 
	            <div class="navbar-header"> 
	                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                </button> 
	                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
	            </div> 
	            <div class="navbar-collapse collapse"> 
	                <ul class="nav navbar-nav"> 
	                    <li>@Html.ActionLink("Home", "Index", "Home")</li> 
	                    <li>@Html.ActionLink("About", "About", "Home")</li> 
	                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li> 
	                </ul> 
	                @Html.Partial("_LoginPartial") 
	            </div> 
	        </div> 
	    </div> 
	    <div class="container body-content"> 
	        @RenderBody() 
	        <hr /> 
	        <footer> 
	            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p> 
	        </footer> 
	    </div> 
	    @Scripts.Render("~/bundles/jquery") 
	    @Scripts.Render("~/bundles/bootstrap") 
	    @RenderSection("scripts", required: false) 
	</body> 
	</html>` </pre>
