<properties title="Error During Authentication Detection" pageTitle="Ошибка при обнаружении проверки подлинности" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />
  
<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

###Ошибка при обнаружении проверки подлинности 
Во время обнаружения предыдущего кода проверки подлинности мастер обнаружил несовместимый тип проверки подлинности.   

###Что проверяется?

####Типы проектов

Мастер проверяет, какого типа проект вы разрабатываете, поэтому он может внедрить в проект правильную логику проверки подлинности.  Если в проекте есть какой-либо контроллер, являющийся производным от ApiController, этот проект будет рассматриваться как WebAPI проекта.  Если в проекте есть только контроллеры, которые являются производными от MVC.Controller, этот проект будет рассматриваться как проект MVC.  Все остальные варианты мастером не поддерживаются.  Проекты веб-форм сейчас не поддерживаются.

#####Совместимый код проверки подлинности

Мастер также проверяет параметры проверки подлинности, ранее настроенные с помощью мастера или совместимые с ним.  Если все такие параметры присутствуют, это рассматривается как реентерабельный случай, и мастер сможет открыть и отобразить эти параметры.  Если присутствуют только некоторые из этих параметров, это рассматривается как случай ошибки.

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

Наконец, мастер пытается обнаружить версии кода проверки подлинности, настроенные в предыдущих версиях Visual Studio. Эта ошибка означает, что проект содержит несовместимый тип проверки подлинности. Мастер определяет следующие типы проверок подлинности из предыдущих версий Visual Studio.

* Проверка подлинности Windows 
* Учетные записи индивидуальных пользователей 
* Учетные записи организации 
 

Чтобы обнаружить проверку подлинности Windows в проекте MVC, мастер ищет элемент authentication в файле **web.config**.

<PRE class="prettyprint">
	&lt;configuration&gt;
	    &lt;system.web&gt;
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
	    &lt;/system.web&gt;
	&lt;/configuration&gt;
</pre>

Чтобы обнаружить проверку подлинности Windows в проекте веб-API, мастер ищет элемент IISExpressWindowsAuthentication в файле **.csproj** вашего проекта:

<PRE class="prettyprint">
	&lt;Project&gt;
	    &lt;PropertyGroup&gt;
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
	    &lt;/PropertyGroup>
	&lt;/Project&gt;
</PRE>

Чтобы обнаружить проверку подлинности индивидуальных учетных записей пользователей, мастер ищет элемент пакета в файле **Packages.config**.

<PRE class="prettyprint">
	&lt;packages&gt;
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
	&lt;/packages&gt;
</PRE>

Чтобы определить старый тип проверки подлинности учетной записи организации, мастер ищет в файле **web.config** следующий элемент:

<PRE class="prettyprint">
	&lt;configuration*gt;
	    &lt;appSettings&gt;
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
</PRE>

Чтобы изменить тип проверки подлинности, удалите несовместимый тип и повторно запустите мастер.

Подробнее см. в статье [Сценарии проверки подлинности для Azure AD](http://msdn.microsoft.com/library/azure/dn499820.aspx).
