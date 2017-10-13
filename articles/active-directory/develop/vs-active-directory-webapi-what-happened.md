---
title: "Изменения, вносимые в проект веб-API при подключении к Azure AD | Документация Майкрософт"
description: "Описывает, что происходит с проектом веб-API при подключении к Azure AD с помощью Visual Studio."
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 086e5a9622cad681cd282345d97e0c28ee7de2fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Что произошло с моим проектом WebApi в подключенной службе Visual Studio Azure Active Directory
> [!div class="op_single_selector"]
> * [Приступая к работе](vs-active-directory-webapi-getting-started.md)
> * [Что произошло?](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Добавлены ссылки
### <a name="nuget-package-references"></a>Ссылки на пакет NuGet
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>Ссылки на .NET
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>Изменения в коде
### <a name="code-files-were-added-to-your-project"></a>В проект добавлены файлы с кодом
К проекту добавлен класс запуска проверки подлинности **App_Start/Startup.Auth.cs**, содержащий логику запуска для проверки подлинности на основе Azure AD.

### <a name="startup-code-was-added-to-your-project"></a>В проект добавлен код запуска
Если в проекте уже есть класс запуска, метод **Configuration** обновляется для добавления вызова `ConfigureAuth(app)`. В противном случае в проект добавляется класс запуска.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Один из файлов app.config или web.config имеет новое значение конфигурации.
Были добавлены следующие записи конфигурации.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>Создано приложение Azure AD
Было создано приложение Azure AD в указанно в мастере каталоге.

[Дополнительная информация о службе Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Какие дополнительные изменения внесены в мой проект после установки флажка *Отключить проверку подлинности для отдельных учетных записей пользователей*?
Удалены ссылки на пакет NuGet. Для файлов созданы резервные копии, а сами файлы удалены. В зависимости от состояния проекта вам, возможно, придется вручную удалить дополнительные ссылки или файлы либо изменить код соответствующим образом.

### <a name="nuget-package-references-removed-for-those-present"></a>Ссылки на существующие пакеты NuGet удалены
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Для существующих файлов кода созданы резервные копии, а сами файлы удалены.
Для каждого из следующих файлов создана резервная копия, а сами файлы удалены из проекта. Резервные копии файлов расположены в папке Backup в корневом каталоге проекта.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>Для существующих файлов кода созданы резервные копии
Для каждого из следующих файлов создана резервная копия, после чего файлы были заменены. Резервные копии файлов расположены в папке Backup в корневом каталоге проекта.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Какие дополнительные изменения внесены в мой проект после установки флажка *Чтение данных каталога*?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>В файл app.config или web.config внесены дополнительные изменения
Были добавлены следующие дополнительные записи настройки.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Обновлено приложение Azure Active Directory
В приложение Azure Active Directory добавлено разрешение *Чтение данных каталога*. Кроме того, создан дополнительный ключ, который затем использовался в качестве параметра *ida:Password* в файле `web.config`.

## <a name="next-steps"></a>Дальнейшие действия
- [Дополнительная информация о службе Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

