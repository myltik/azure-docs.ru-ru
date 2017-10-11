---
title: "Как диагностировать ошибки с помощью мастера подключения к Azure Active Directory"
description: "Мастер подключения к Active Directory обнаружил несовместимый тип проверки подлинности"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/05/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 4f29f62b2996cae98b02c1ed5fcb59eca09301ef
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connection-wizard"></a>Диагностика ошибок с помощью мастера подключения к Azure Active Directory
Во время обнаружения предыдущего кода проверки подлинности мастер обнаружил несовместимый тип проверки подлинности.   

## <a name="what-is-being-checked"></a>Что проверяется?
**Примечание.** Чтобы правильно определить предыдущий код аутентификации в проекте, необходимо создать проект.  Если возникла эта ошибка и в вашем проекте нет предыдущего кода аутентификации, еще раз выполните сборку проекта и повторите попытку.

### <a name="project-types"></a>Типы проектов
Мастер проверяет, какого типа проект вы разрабатываете, поэтому он может внедрить в проект правильную логику аутентификации.  Если в проекте есть какой-либо контроллер, являющийся производным от `ApiController`, то этот проект рассматривается как проект веб-API.  Если в проекте есть какие-либо контроллеры, являющиеся производными от `MVC.Controller`, то этот проект рассматривается как проект MVC.  Все остальные варианты мастером не поддерживаются.

### <a name="compatible-authentication-code"></a>Совместимый код проверки подлинности
Мастер также проверяет параметры проверки подлинности, ранее настроенные с помощью мастера или совместимые с ним.  Если все параметры присутствуют, то это рассматривается как реентерабельный случай, и мастер может открыть и отобразить эти параметры.  Если присутствуют только некоторые параметры, это рассматривается как случай ошибки.

В проекте MVC мастер проверяет все перечисленные ниже параметры, которые были получены в результате предыдущего использования мастера.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Кроме того, мастер проверяет в проекте веб-API все перечисленные ниже параметры, которые были получены в результате предыдущего использования мастера.

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>Несовместимый код проверки подлинности
Наконец, мастер пытается обнаружить версии кода проверки подлинности, настроенные в предыдущих версиях Visual Studio. Эта ошибка означает, что проект содержит несовместимый тип проверки подлинности. Мастер определяет следующие типы проверок подлинности предыдущих версий Visual Studio:

* Проверка подлинности Windows 
* Учетные записи индивидуальных пользователей 
* Учетные записи организации 

Чтобы обнаружить аутентификацию Windows в проекте MVC, мастер ищет элемент `authentication` в файле **web.config** .

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Чтобы обнаружить аутентификацию Windows в проекте веб-API, мастер ищет элемент `IISExpressWindowsAuthentication` в **CSPROJ-файле** вашего проекта.

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

Чтобы обнаружить проверку подлинности индивидуальных учетных записей, мастер ищет элемент пакета в файле **Packages.config** .

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

Чтобы определить старый тип проверки подлинности учетной записи организации, мастер ищет в файле **web.config**следующий элемент:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Чтобы изменить тип проверки подлинности, удалите несовместимый тип и повторно запустите мастер.

Дополнительные сведения см. в статье [Сценарии аутентификации в Azure Active Directory](active-directory-authentication-scenarios.md).

#<a name="next-steps"></a>Дальнейшие действия
- [Сценарии аутентификации в Azure Active Directory](active-directory-authentication-scenarios.md)