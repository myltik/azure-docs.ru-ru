---
title: Как диагностировать ошибки с помощью подключенной службы Azure Active Directory
description: Подключенная служба Active Directory обнаружила несовместимый тип аутентификации
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 8cd15c59bbe536dba9adb6f44c07844eaf030b55
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31784893"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Диагностика ошибок с помощью подключенной службы Azure Active Directory

При попытке определить существующий код аутентификации сервер подключения Azure Active Directory обнаружил несовместимый тип аутентификации.

Чтобы правильно определить существующий в проекте код аутентификации, необходимо создать проект.  Если возникла эта ошибка и в вашем проекте нет предыдущего кода аутентификации, еще раз выполните сборку проекта и повторите попытку.

## <a name="project-types"></a>Типы проектов

Подключенная служба проверяет тип проекта, над которым вы работаете, чтобы внедрить в него правильную логику аутентификации. Если в проекте есть какой-либо контроллер, являющийся производным от `ApiController`, то этот проект рассматривается как проект веб-API. Если в проекте есть какие-либо контроллеры, являющиеся производными от `MVC.Controller`, то этот проект рассматривается как проект MVC. Подключенная служба не поддерживает другие типы проектов.

## <a name="compatible-authentication-code"></a>Совместимый код аутентификации

Также подключенная служба проверяет параметры аутентификации, настроенные ранее или совместимые с этой службой. Если присутствуют все необходимые параметры, то проект рассматривается как подключаемый повторно, и подключенная служба отображает эти параметры при открытии.  Если присутствуют только некоторые параметры, это рассматривается как случай ошибки.

В проекте MVC подключенная служба проверяет все перечисленные ниже параметры, которые могли быть созданы при предыдущем применении этой службы.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Кроме того, подключенная служба проверяет в проекте веб-API все перечисленные ниже параметры, которые могли быть созданы при предыдущем применении этой службы.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Несовместимый код аутентификации

И в завершение подключенная служба пытается обнаружить версии кода аутентификации, настроенные для предыдущих версий Visual Studio. Эта ошибка означает, что проект содержит несовместимый тип проверки подлинности. Подключенная служба обнаруживает следующие типы аутентификации от предыдущих версий Visual Studio:

* Проверка подлинности Windows
* Учетные записи индивидуальных пользователей
* Учетные записи организации

Чтобы обнаружить аутентификацию Windows в проекте MVC, подключенная служба ищет элемент `authentication` в файле `web.config`.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Чтобы обнаружить аутентификацию Windows в проекте Web API, подключенная служба ищет элемент `IISExpressWindowsAuthentication` в файле `.csproj`.

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Чтобы обнаружить аутентификацию по учетным записям отдельных пользователей, подключенная служба ищет элемент пакета в файле `packages.config`.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

Чтобы обнаружить старый тип аутентификации по корпоративной учетной записи, подключенная служба ищет в файле `web.config` следующий элемент:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

Чтобы изменить тип аутентификации, удалите несовместимый тип аутентификации и повторно добавьте подключенную службу.

Дополнительные сведения см. в статье [Сценарии аутентификации в Azure Active Directory](active-directory-authentication-scenarios.md).