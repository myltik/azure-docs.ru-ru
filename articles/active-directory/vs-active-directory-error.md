<properties 
	pageTitle="Ошибка при обнаружении проверки подлинности" 
	description="Мастер подключения к Active Directory обнаружил несовместимый тип проверки подлинности" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Ошибка при обнаружении проверки подлинности

Во время обнаружения предыдущего кода проверки подлинности мастер обнаружил несовместимый тип проверки подлинности.

###Что проверяется?

**Примечание**. Чтобы правильно определить предыдущий код аутентификации в проекте, необходимо создать проект. Если возникла эта ошибка и в вашем проекте нет предыдущего кода аутентификации, создайте проект снова и повторите попытку.

####Типы проектов

Мастер проверяет, какого типа проект вы разрабатываете, поэтому он может внедрить в проект правильную логику проверки подлинности. Если в проекте есть какой-либо контроллер, являющийся производным от `ApiController`, этот проект будет рассматриваться как проект веб-API. Если в проекте есть только контроллеры, являющиеся производными от `MVC.Controller`, этот проект будет рассматриваться как проект MVC. Все остальные варианты мастером не поддерживаются. Проекты веб-форм сейчас не поддерживаются.

#####Совместимый код проверки подлинности

Мастер также проверяет параметры проверки подлинности, ранее настроенные с помощью мастера или совместимые с ним. Если все такие параметры присутствуют, это рассматривается как реентерабельный случай, и мастер сможет открыть и отобразить эти параметры. Если присутствуют только некоторые параметры, это рассматривается как случай ошибки.

В проекте MVC мастер проверяет все перечисленные ниже параметры, которые были получены в результате предыдущего использования мастера.

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

Кроме того, мастер проверяет в проекте веб-API все перечисленные ниже параметры, которые были получены в результате предыдущего использования мастера.

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

#####Несовместимый код проверки подлинности

Наконец, мастер пытается обнаружить версии кода проверки подлинности, настроенные в предыдущих версиях Visual Studio. Эта ошибка означает, что проект содержит несовместимый тип проверки подлинности. Мастер определяет следующие типы проверок подлинности предыдущих версий Visual Studio:

* Проверка подлинности Windows 
* Учетные записи индивидуальных пользователей 
* Учетные записи организации 
 

Чтобы обнаружить аутентификацию Windows в проекте MVC, мастер ищет элемент `authentication` в файле **web.config**.

<PRE class="prettyprint">
	&lt;configuration&gt;
	    &lt;system.web&gt;
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
	    &lt;/system.web&gt;
	&lt;/configuration&gt;
</pre>

Чтобы обнаружить аутентификацию Windows в проекте веб-API, мастер ищет элемент `IISExpressWindowsAuthentication` в **CSPROJ-файле** вашего проекта.

<PRE class="prettyprint">
	&lt;Project&gt;
	    &lt;PropertyGroup&gt;
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
	    &lt;/PropertyGroup>
	&lt;/Project&gt;
</PRE>

Чтобы обнаружить проверку подлинности индивидуальных учетных записей, мастер ищет элемент пакета в файле **Packages.config**.

<PRE class="prettyprint">
	&lt;packages&gt;
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
	&lt;/packages&gt;
</PRE>

Чтобы определить старый тип проверки подлинности учетной записи организации, мастер ищет в файле **web.config** следующий элемент:

<PRE class="prettyprint">
	&lt;configuration&gt;
	    &lt;appSettings&gt;
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
</PRE>

Чтобы изменить тип проверки подлинности, удалите несовместимый тип и повторно запустите мастер.

Дополнительные сведения см. в разделе [Сценарии проверки подлинности для Azure AD](active-directory-authentication-scenarios.md).
 

<!---HONumber=62-->