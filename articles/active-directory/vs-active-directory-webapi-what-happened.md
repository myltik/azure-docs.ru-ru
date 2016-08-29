.<properties
	pageTitle="Что произошло с моим проектом WebApi в подключенной службе Visual Studio Azure Active Directory | Microsoft Azure "
	description="Описывает, что происходит с проектом WebApi при подключении к приложению Azure AD с помощью Visual Studio"
  services="active-directory"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="tarcher"/>

# Что произошло с моим проектом WebApi в подключенной службе Visual Studio Azure Active Directory

> [AZURE.SELECTOR]
> - [Приступая к работе](vs-active-directory-webapi-getting-started.md)
> - [Что произошло?](vs-active-directory-webapi-what-happened.md)

##Добавлены ссылки

###Ссылки на пакет NuGet

- `Microsoft.Owin`
- .`Microsoft.Owin.Host.SystemWeb`
- .`Microsoft.Owin.Security`
- .`Microsoft.Owin.Security.ActiveDirectory`
- .`Microsoft.Owin.Security.Jwt`
- .`Microsoft.Owin.Security.OAuth`
- .`Owin`
- `System.IdentityModel.Tokens.Jwt`

###Ссылки на .NET

- `Microsoft.Owin`
- .`Microsoft.Owin.Host.SystemWeb`
- .`Microsoft.Owin.Security`
- .`Microsoft.Owin.Security.ActiveDirectory`
- .`Microsoft.Owin.Security.Jwt`
- .`Microsoft.Owin.Security.OAuth`
- .`Owin`
- `System.IdentityModel.Tokens.Jwt`

##Изменения в коде

###В проект добавлены файлы с кодом

К проекту добавлен класс запуска проверки подлинности **App\_Start/Startup.Auth.cs**, содержащий логику запуска для проверки подлинности на основе Azure AD.

###В проект добавлен код запуска

Если в проекте уже есть класс запуска, метод **Configuration** обновляется для добавления вызова `ConfigureAuth(app)`. В противном случае в проект добавляется класс запуска.


###Один из файлов app.config или web.config имеет новое значение конфигурации.

Были добавлены следующие записи конфигурации.
```
	`<appSettings>
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" />
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" />
    		<add key="ida:Audience" value="The App ID Uri from the wizard" />
	</appSettings>`
```

###Создано приложение Azure AD

Было создано приложение Azure AD в указанно в мастере каталоге.

[Дополнительная информация о службе Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##Какие дополнительные изменения внесены в мой проект после установки флажка *Отключить проверку подлинности для отдельных учетных записей пользователей*?
Удалены ссылки на пакет NuGet. Для файлов созданы резервные копии, а сами файлы удалены. В зависимости от состояния проекта вам, возможно, придется вручную удалить дополнительные ссылки или файлы либо изменить код соответствующим образом.

###Ссылки на существующие пакеты NuGet удалены

- `Microsoft.AspNet.Identity.Core`
- .`Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###Для существующих файлов кода созданы резервные копии, а сами файлы удалены.

Для каждого из следующих файлов создана резервная копия, а сами файлы удалены из проекта. Резервные копии файлов расположены в папке Backup в корневом каталоге проекта.

- `App_Start\IdentityConfig.cs`
- .`Controllers\AccountController.cs`
- .`Controllers\ManageController.cs`
- .`Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###Для существующих файлов кода созданы резервные копии

Для каждого из следующих файлов создана резервная копия, после чего файлы были заменены. Резервные копии файлов расположены в папке Backup в корневом каталоге проекта.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##Какие дополнительные изменения внесены в мой проект после установки флажка *Чтение данных каталога*?

###В файл app.config или web.config внесены дополнительные изменения

Были добавлены следующие дополнительные записи настройки.

```
	`<appSettings>
	    <add key="ida:Password" value="Your Azure AD App's new password" />
	</appSettings>`
```

###Обновлено приложение Azure Active Directory
В приложение Azure Active Directory добавлено разрешение *Чтение данных каталога*. Кроме того, создан дополнительный ключ, который затем использовался в качестве параметра *ida:Password* в файле `web.config`.

[Дополнительная информация о службе Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0817_2016-->