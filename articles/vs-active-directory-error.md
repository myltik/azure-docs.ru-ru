<properties title="Ошибка при обнаружении проверки подлинности" pageTitle="Ошибка при обнаружении проверки подлинности" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Ошибка при обнаружении проверки подлинности

Во время обнаружения предыдущего кода проверки подлинности мастер обнаружил несовместимый тип проверки подлинности.

##### Что проверяется?

Мастер пытает обнаружить версии кода проверки подлинности, настроенные в предыдущей версии Visual Studio. Эта ошибка означает, что проект содержит несовместимый тип проверки подлинности. Мастер определяет следующие типы проверок подлинности предыдущих версий Visual Studio:

-   Проверка подлинности Windows
-   Учетные записи индивидуальных пользователей
-   Учетные записи организации

Чтобы обнаружить проверку подлинности Windows в проекте MVC, мастер ищет элемент `authentication` в файле **web.config**.

<pre class="prettyprint">
    &lt;configuration&gt;
        &lt;system.web&gt;
            &lt;authentication mode=&quot;Windows&quot; /&gt;
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Чтобы обнаружить проверку подлинности Windows в проекте MVC, мастер ищет элемент `IISExpressWindowsAuthentication` в **CSPROJ**-файле вашего проекта.

<pre class="prettyprint">
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            &lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
        &lt;/PropertyGroup&gt;
    &lt;/Project&gt;
</pre>

Чтобы обнаружить проверку подлинности индивидуальных учетных записей, мастер ищет элемент пакета в файле **Packages.config**.

<pre class="prettyprint">
    &lt;packages&gt;
        &lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
    &lt;/packages&gt;
</pre>

Чтобы определить старый тип проверки подлинности учетной записи организации, мастер ищет в файле **web.config** следующий элемент:

<pre class="prettyprint">
    &lt;configuration*gt;
        &lt;appSettings&gt;
            &lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Чтобы изменить тип проверки подлинности, удалите несовместимый тип и повторно запустите мастер.

Дополнительные сведения см. в разделе [Сценарии проверки подлинности для Azure AD][Сценарии проверки подлинности для Azure AD].

  [Сценарии проверки подлинности для Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx
