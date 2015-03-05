<properties 
	pageTitle="Бизнес-приложения на веб-сайтах Azure с AD FS" 
	Описание="сведения о создании бизнес-приложения ASP.NET MVC на веб-сайтах Azure, которое выполняет проверку подлинности с помощью локальной службы токенов безопасности STS. В этом учебнике рассматриваются службы федерации Acive Directory в качестве локальной службы STS." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="02/12/2015" 
	ms.author="cephalin"/>

# Создание бизнес-приложения ASP.NET MVC на веб-сайтах Azure, которое выполняет проверку подлинности с помощью AD FS #

В этой статье вы узнаете, как создать бизнес-приложение ASP.NET MVC на веб-сайтах [Azure](http://azure.microsoft.com/services/websites/) с использованием локальных [служб федерации Active Directory](http://technet.microsoft.com/ library/hh831502.aspx) в качестве поставщика удостоверений. Этот сценарий можно использовать, когда необходимо создать бизнес-приложения на веб-сайтах Azure, но организации требуется сохранять все данные локально.

Обзор различных вариантов проверки подлинности и авторизации для предприятий на веб-сайтах Azure см. в разделе [Проверка подлинности и авторизация пользователей в бизнес-приложениях на веб-сайтах Azure](./web-sites-authentication-authorization).

<a name="bkmk_build"></a>
## Что будет строиться ##

Вы создадите простое приложение ASP.NET на веб-сайтах Azure со следующими возможностями:

- проверка подлинности пользователей в AD FS
- использование `[авторизации]` для авторизации пользователей для различных действий
- статическая настройка для отладки в Visual Studio и публикации на веб-сайтах Azure (после настройки отладка и публикация в любое время)  

<a name="bkmk_need"></a>
## Необходимые условия ##

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

Чтобы выполнить этот учебник, необходимо следующее:

- локальное развертывание AD FS (полное пошаговое руководство по лаборатории тестирования, которую я использую, см. в разделе [Лаборатория тестирования: автономная STS с AD FS в виртуальной машине Azure (только для тестирования)](TODO))
- разрешения для создания отношений доверия с проверяющей стороной в оснастке управления AD FS
- Visual Studio 2013

<a name="bkmk_sample"></a>
## Использование образца приложения в качестве шаблона бизнес-приложения ##

Учебное приложение в этом учебнике, [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet),создано рабочей группой Azure Active Directory. Так как AD FS поддерживают WS-Federation, можно без всяких проблем использовать их для создания новых бизнес-приложений. Они имеют следующие возможности:

- использование [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) для проверки подлинности с помощью локального развертывания AD FS
- функции входа и выхода
- использование [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (вместо Windows Identity Foundation, т.е. WIF). Этот интерфейс определяет будущее ASP.NET, он гораздо проще в настройке для проверки подлинности и авторизации, чем WIF

<a name="bkmk_setup"></a>
## настройка простого приложения ##

2.	Клонирование или загрузка примера решения на сайте [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) в локальный каталог.

	> [AZURE.NOTE] Инструкции в [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) показывают настройку приложения с помощью Azure Active, но в этом учебнике настройка будет выполняться с использованием AD FS. Поэтому вместо этого необходимо выполнять указанные здесь действия.

3.	Откройте решение, а затем откройте Controllers\AccountController.cs в **обозревателе решений**.

	Вы увидите, что код просто выдает запрос проверки подлинности для проверки подлинности пользователя с помощью WS-Federation. Вся проверка подлинности настраивается в App_Start\Startup.Auth.cs.

4.  Откройте App_Start\Startup.Auth.cs. В методе `ConfigureAuth` найдите строку:

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	в среде OWIN это действительно абсолютный минимум, который необходим для настройки проверки подлинности с помощью WS-Federation. Это гораздо более простой и элегантный способ, чем WIF, когда Web.config вставляется с помощью XML по всей программе. Единственное, что нам потребуется, это идентификатор проверяющей стороны(RP) и URL-адрес файла метаданных службы AD FS. Ниже приведен пример:

	-	идентификатор RP: `https://contoso.com/MyLOBApp`
	-	адрес метаданных: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	В App_Start\Startup.Auth.cs замените статические определения строки как показано ниже.  
	<pre class="prettyprint">
	private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
    <mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
    <mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
    <mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
    <mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>

    <mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
    </pre>

6.	Теперь необходимо сделать соответствующие изменения в файле Web.config. Откройте файл Web.config и измените параметры приложения как показано ниже.  
	<pre class="prettyprint">
	&lt;appSettings&gt;
	  &lt;add key="webpages:Version" value="3.0.0.0" /&gt;
	  &lt;add key="webpages:Enabled" value="false" /&gt;
	  &lt;add key="ClientValidationEnabled" value="true" /&gt;
	  &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" /&gt;
	  <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /&gt;</del></mark>
	  <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /&gt;</del></mark>
	  <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /&gt;</del></mark>
	  <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /&gt;</mark>
	  <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /&gt;</mark>

	&lt;/appSettings&gt;
	</pre>

	Заполните значения ключа на базе данных вашей среды.

7.	Постройте приложение, чтобы убедиться в отсутствии ошибок.

Вот и все. Теперь учебное приложение готово к работе с AD FS. Позднее все-таки потребуется настроить отношения доверия проверяющей стороны с этим приложением в AD FS.

<a name="bkmk_deploy"></a>
## Развертывание учебного приложения на веб-сайтах Azure

Здесь вам предстоит опубликовать приложение на веб-сайте Azure с сохранением среды отладки. Обратите внимание, что публикация приложения будет выполняться до установления отношений доверия проверяющей стороны с AD FS, поэтому проверка подлинности еще не работает. Однако, если это сделать сейчас, можно получить URL-адрес веб-сайта, который позднее также будет использоваться при настройке отношения доверия с проверяющей стороной.

1. Щелкните правой кнопкой мыши свой проект и выберите **Опубликовать**.

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. Выберите **Веб-сайты Microsoft Azure**.
3. Если еще не выполнен вход в Azure, щелкните **Вход** и используйте учетную запись Майкрософт для вашей подписки Azure, чтобы войти.
4. После входа щелкните **Создать** для создания нового веб-сайта.
5. Заполните все обязательные поля. Подключение к локальным данным будет выполняться позднее, поэтому вам не нужно создавать базу данных для этого веб-сайта.

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. Щелкните **Создать**. После создания веб-сайта открывается диалоговое окно Публикация веб-сайта.
7. В поле **URL-адрес назначения** замените **http** на **https**. Скопируйте весь URL-адрес в текстовый редактор. Он вам понадобится позднее. Затем щелкните **Опубликовать**.

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. В Visual Studio откройте файл **Web.Release.config** в вашем проекте. Вставьте следующий XML-код в тег `<configuration>` и замените значение ключа на URL-адрес веб-сайта для публикации  
	<pre class="prettyprint">
&lt;appSettings&gt;
   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

После завершения операции имеется два настроенных идентификатора RP в проекте: одни для среды отладки в Visual Studio, а второй для опубликованного веб-сайта в Azure. Доверие с проверяющей стороной (RP) будет необходимо настроить для каждой из двух сред в AD FS. Во время отладки параметры приложения в файле Web.config используются для того, чтобы конфигурация **Отладка** работала с AD FS. После его публикации (по умолчанию публикуется конфигурация **Выпуск**)отправляется измененный файл Web.config, в котором содержатся изменения параметров приложения в файле Web.Release.config.

При необходимости подключить опубликованный веб-сайт к отладчику (т.е. когда нужно отправить символы отладки кода в опубликованный веб-сайт) можно создать клон конфигурации отладки для отладки в Azure, но со своим собственным настраиваемым преобразованием файла Web.config (например, Web.AzureDebug.config), в котором используются параметры приложения из файла Web.Release.config. Это дает возможность поддерживать статическую конфигурацию в различных средах.

<a name="bkmk_rptrusts"></a>
## Настройка доверия с проверяющей стороной в оснастке управления AD FS ##

Теперь необходимо настроить отношение доверия с проверяющей стороной в оснастке управления AD FS для того, чтобы ваше простое приложение могло действительно выполнять проверку подлинности с помощью AD FS. Для этого необходимо настроить два различных отношения доверия с проверяющей стороной: одно для среды отладки и одно для опубликованного веб-сайта.

> [AZURE.NOTE] Не забудьте выполнить указанные ниже действия для обеих сред.

4.	На сервере AD FS выполните вход с использование учетных данных, имеющих права управления для AD FS.
5.	Откройте оснастку управления AD FS. Щелкните правой кнопкой мыши **AD FS\Доверенные отношения\Доверенные отношения с проверяющей стороной** и выберите **Добавить отношение доверия с проверяющей стороной**.

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	На странице **Выбор источника данных** выберите **Ввести данные о проверяющей стороне вручную**. 

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	На странице **Указание отображаемого имени** введите отображаемое имя для приложения и щелкните **Далее**.
7.	На странице **Выбор протокола** щелкните **Далее**.
8.	На странице **Настройка сертификата** щелкните **Далее**.

	> [AZURE.NOTE] Так как уже должен использоваться протокол HTTPS, зашифрованные токены необязательны. Если действительно необходимо шифровать токены из AD FS на этой странице, нужно добавить логику расшифровки токенов в код. Подробнее см. раздел [Ручная настройка промежуточного слоя OWIN WS-Federation и прием зашифрованных токенов](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
  
5.	Прежде чем переходить к следующему шагу, потребуется часть информации и проекта Visual Studio. В свойствах проекта обратите внимание на **URL-адрес SSL** приложения. 

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	Перейдите снова в оснастку управления AD FS. На странице **Настройка URL-адреса** **Мастера добавления отношения доверия с проверяющей стороной** выберите **Включить поддержку пассивного протокола WS-Federation** и введите URL-адрес SSL проекта Visual Studio, который вы записали на предыдущем шаге. Затем нажмите кнопку **Далее**.

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	> [AZURE.NOTE] URL-адрес указывает, куда отправлять клиента после успешной проверки подлинности. Для среды отладки это должен быть: <code>https://localhost:&lt;port&gt;/</code>. Для опубликованного веб-сайта это должен быть URL-адрес веб-сайта.

7.	На странице **Настройка идентификаторов** убедитесь, что URL-адрес SSL уже находится в списке и щелкните **Далее**. Нажимайте кнопку **Далее** на всех страницах до конца мастера, выбирая значения по умолчанию.

	> [AZURE.NOTE] В App_Start\Startup.Auth.cs проекта Visual Studio этот идентификатор сопоставляется со значением <code>WsFederationAuthenticationOptions.Wtrealm</code> во время федеративной проверки подлинности. По умолчанию URL-адрес приложения из предыдущего шага добавляется как идентификатор RP.

8.	Настройка приложения RP для проекта в AD FS завершена. Далее необходимо настроить это приложение, чтобы оно отправляло утверждения, необходимые вашему приложению. При окончании мастера по умолчанию открывается диалоговое окно **Изменение правил для утверждений** и можно сразу приступить к настройке. Как минимум, настроим следующие утверждения (со схемами в круглых скобках):

	-	Имя (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) - используется ASP.NET, чтобы заполнить `User.Identity.Name`.
	-	Имя участника-пользователя (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) - используется для идентификации пользователей в организации.
	-	Членства в группах как роли (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) - могут использоваться с оформлением `[Authorize(Roles="role1, role2,...")]` для авторизации контроллеров/ действий. На практике такой подход к авторизации ролей может оказаться не самым производительным, в особенности, если пользователи AD постоянно оказываются членами сотен групп безопасности, что преобразуется в сотни утверждения роли в токене SAML. В качестве альтернативного подхода можно отправить одной утверждение роли, условно зависящее от членства пользователя в определенной группе. Однако в нашем учебнике мы будем придерживаться простого подхода.
	-	Идентификатор имени (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) - может использоваться для проверки защиты от подделки. Подробнее о работе приложения с проверкой защиты от подделки см. в подразделе **Добавление функций к бизнес-приложению** раздела [Создание бизнес-приложения ASP.NET MVC на веб-сайтах Azure, которое выполняет проверку подлинности с помощью AD FS](../web-sites-dotnet-lob-application-adfs/#bkmk_crud).

	> [AZURE.NOTE] Типы утверждений, которые необходимо настроить для приложения определяются его потребностями. Список утверждений, поддерживаемых приложениями Azure Active Directory (т.е отношениями доверия RP), например, см. в разделе [Поддерживаемые типы токенов и утверждений](http://msdn.microsoft.com/library/azure/dn195587.aspx).

8.	В диалоговом окне Изменение правил для утверждений щелкните **Добавить правило**.
9.	Настройте имя, UPN и утверждения роли, как показано ниже, а затем щелкните **Готово**.

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	Далее необходимо создать временное утверждение идентификатора имени, используя шаги, описанные в разделе [Идентификаторы имени в утверждениях SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

9.	Щелкните **Добавить правило** еще раз.
10.	Выберите **Отправить утверждения с использованием настраиваемого правила** и щелкните **Далее**.
11.	Вставьте следующий язык правила в поле **Настраиваемое правило**, присвойте правилу имя **В соответствии с идентификатором сеанса** и нажмите кнопку **Готово**.  
	<pre class="prettyprint">
	c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
		add(
			store = "_OpaqueIdStore",
			types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
			query = "{0};{1};{2};{3};{4}",
			param = "useEntropy",
			param = c1.Value,
			param = c1.OriginalIssuer,
			param = "",
			param = c2.Value);
	</pre>

	Настраиваемое правило должно выглядеть следующим образом:

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	Щелкните **Добавить правило** еще раз.
10.	Выберите **Преобразование входящего утверждения** и щелкните **Далее**.
11.	Настройте правило, как указано ниже (используя тип утверждения, созданный в настраиваемом правиле) и нажмите кнопку **Готово**.

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	Подробнее о шагах по созданию временного утверждения идентификатора имени см. в разделе [Идентификаторы имени в утверждениях SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

12.	Щелкните **Применить** в диалоговом окне **Изменение правил для утверждений**. Все должно выглядеть как на снимке экрана ниже:

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	> [AZURE.NOTE] Напоминаю, что эти шаги необходимо повторить для среды отладки и опубликованного веб-сайта.

<a name="bkmk_test"></a>
## Тестирование федеративной проверки подлинности для приложения

Все готово для тестирования логики проверки подлинности приложения в AD FS. В моей лабораторной среде AD FS я использую тестового пользователя, который входит в состав тестовой группы в Active Directory (AD).

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Для проверки подлинности в отладчике достаточно просто ввести `F5`. При необходимости протестировать проверку подлинности в опубликованном веб-сайте нужно перейти к URL-адресу.

После загрузки веб-приложения щелкните **Вход**. Теперь должно появиться диалоговое окно входа или страница входа, обслуживаемые AD FS в зависимости от метода проверки подлинности который выбирается AD FS. Вот что получается в Internet Explorer 11.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

После входа с правами пользователя в домен AD развертывания AD FS вы снова должны увидеть домашнюю страницу с сообщением **Здравствуйте, <имя пользователя>!** в углу. Вот что у меня получается.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Итак, что нам удалось успешно сделать:

- приложение успешно получило доступ к AD FS, и в базе данных AD FS был найден совпадающий идентификатор RP
- AD FS успешно выполняют проверку подлинности пользователя AD и перенаправляют вас обратно на домашнюю страницу приложения
- AD FS успешно отправляет утверждение имени (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) приложению. Это подтверждается тем, что имя пользователя отображается в углу. 

Если утверждение имени отсутствует, вы бы увидели **Здравствуйте, !**. Если посмотреть на Views\Shared\_LoginPartial.cshtml, можно увидеть, что здесь используется `User.Identity.Name` для отображения имени пользователя. Как уже отмечалось ранее, ASP.NET заполняет это свойство утверждением имени прошедшего проверку подлинности пользователя, если оно доступно в токене SAML. Чтобы просмотреть все утверждения, отправленные AD FS, вставьте точку останова в Controllers\HomeController.cs в методе действия индекса. После выполнения проверки подлинности пользователя проверьте коллекцию `System.Security.Claims.Current.Claims`.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png) 

<a name="bkmk_authorize"></a>
## Авторизация пользователей для определенных контроллеров и действий

С момента включения членств в группах как утверждений роли в конфигурацию отношения доверия с проверяющей стороной, можно их использовать напрямую в оформлении `[Authorize(Roles="...")]` для контроллеров и действий. В бизнес-приложении с шаблоном создать- прочитать-обновить-удалить (CRUD) можно выполнять авторизацию определенных ролей для доступа к каждому действию. На сегодня просто попробуем, как работает эта функция на примере существующего контроллера Home.

1. Откройте Controllers\HomeController.cs.
2. Оформите методы действия `About` и `Contact` аналогично тому, как указано ниже, используя членства в группах безопасности, которые имеются у пользователя, прошедшего проверку подлинности.  
	<pre class="prettyprint">
    <mark>[Authorize(Roles="Test Group")]</mark>
    public ActionResult About()
    {
        ViewBag.Message = "Your application description page.";

        return View();
    }

    <mark>[Authorize(Roles="Domain Admins")]</mark>
    public ActionResult Contact()
    {
        ViewBag.Message = "Your contact page.";

        return View();
    }
	</pre>

	Поскольку я добавил **Тестового пользователя** в **Тестовую группу** в своей лабораторной среде AD FS, я буду использовать тестовую группу для тестирования на ней авторизации `About`. Для `Contact` я проведу тестирование отрицательного случая группы **Администраторы домена**, к которой не принадлежит **Тестовый пользователь**.

3. Запустите отладчик, введя `F5`, и выполните вход, а затем нажмите кнопку **Сведения о**. Вы должны теперь видеть страницу `~/Сведения о/Индекс`, если прошедший проверку подлинности пользователь авторизован для этого действия.
4. Далее нажмите кнопку **Контакт**, что в моем случае не должно авторизовать **Тестового пользователя** для этого действия. Тем не менее, браузер перенаправляется к AD FS, в результате чего, в конечном счете, показывается это сообщение:

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	При анализе этой ошибки в средстве просмотра событий на сервере AD FS можно увидеть это сообщение об исключении:  
	<pre class="prettyprint">
	Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>The same client browser session has made '6' requests in the last '11' seconds.</mark> Contact your administrator for details.
	   at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
	   at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
	</pre>

	Это происходит потому, что по умолчанию MVC возвращает ошибку "401 Неавторизован", когда роли пользователя неавторизованы. В результате активируется запрос на повторную проверку подлинности к поставщику удостоверений(AD FS). Так как пользователь уж прошел проверку подлинности, AD FS возвращается на ту же страницу, которая издает другую ошибку 401, создавая цикл перенаправления. Можно переопределить метод AuthorizeAttribute's `HandleUnauthorizedRequest` с помощью простой логики, чтобы отображать более осмысленную информацию вместо продолжения цикла перенаправления.

5. Создайте файл в проекте с именем AuthorizeAttribute.cs и вставьте в него приведенный ниже код.

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	Код переопределения отправляет ошибку HTTP 403 (Запрещено) вместо HTTP 401 (неавторизованный) в случаях "прошедший проверку подлинности, но не авторизованный"

6. Запустите отладчик снова с помощью `F5`. Нажатие кнопки **Контакт** теперь показывает более информативное (хотя и непривлекательное) сообщение об ошибке:

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. Опубликуйте приложение на веб-сайте Azure еще раз и протестируйте поведение развернутого приложения.

<a name="bkmk_data"></a>
## Подключение к локальным данным

Одна из причин реализации бизнес-приложения с AD FS вместо Azure Active Directory заключается в проблемах обеспечения соответствия, когда организации требуется сохранять данные локально. Это может также означать, что веб-сайт Azure должен иметь доступ к локальной базе данных, поскольку запрещено использование [Базы данных SQL](http://azure.microsoft.com/services/sql-database/) в качестве уровня данных для ваших веб-сайтов.

Веб-сайты Azure поддерживают доступ в локальным базам данных двумя способами. [Гибридные подключения](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-overview/) и [Виртуальные сети](http://azure.microsoft.com/documentation/articles/web-sites-integrate-with-vnet/). Подробнее см. раздел [использование интеграции VNET и гибридных подключений с веб-сайтами Azure](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>
## Дополнительные ресурсы

- [Защита приложения с помощью SSL и атрибута авторизации](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Проверка подлинности и авторизация пользователей в бизнес-приложениях на веб-сайтах Azure ](../web-sites-authentication-authorization/)
- [Создание бизнес-приложения ASP.NET MVC на веб-сайтах Azure, выполняющего проверку подлинности с помощью Azure Active Directory](../web-sites-dotnet-lob-application-azure-ad/)
- [Использование локальной проверки подлинности в организации(ADFS)с использованием ASP.NET в Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Блог Витторио Берточчи](http://blogs.msdn.com/b/vbertocci/)
- [Миграция веб-проекта VS2013 из WIF в Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Обзор служб федерации Active Directory](http://technet.microsoft.com/ library/hh831502.aspx)
- [Спецификация WS-Federation 1.1](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)

<!--HONumber=45--> 
