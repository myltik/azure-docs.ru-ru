---
title: "Создание бизнес-приложения Azure с аутентификацией AD FS | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как создать бизнес-приложение в службе приложений Azure, которая выполняет проверку подлинности с помощью службы маркеров безопасности. В этом руководстве рассматриваются службы федерации Acive Directory в качестве локальной службы STS."
services: app-service\web
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: 0fa9f7a1-37bd-4d11-845f-aeff6fc9e4ca
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: f9a8984400378d154a504af8a41609900128d052
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---
# <a name="create-a-line-of-business-azure-app-with-ad-fs-authentication"></a>Создание бизнес-приложения Azure с проверкой подлинности AD FS
Из этой статьи вы узнаете, как создать бизнес-приложение ASP.NET MVC в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) с использованием локальных [служб федерации Active Directory](http://technet.microsoft.com/library/hh831502.aspx) в качестве поставщика удостоверений. Этот сценарий можно использовать, когда необходимо создать бизнес-приложения в службе приложений Azure, но организация требует сохранять все данные каталога локально.

> [!NOTE]
> Обзор вариантов корпоративной аутентификации и авторизации для службы приложений Azure см. в статье [Проверка подлинности в приложении Azure с помощью локального каталога Active Directory](web-sites-authentication-authorization.md).
> 
> 

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>Что будет строиться
Вы создадите простое приложение ASP.NET в веб-приложениях службы приложений Azure со следующими возможностями:

* проверка подлинности пользователей в AD FS
* использование команды `[Authorize]` для авторизации различных действий пользователей;
* статическая настройка для отладки в Visual Studio и публикации в веб-приложениях службы приложений (после настройки отладка и публикация в любое время).  

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>Необходимые элементы
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Чтобы выполнить инструкции этого учебника, необходимо следующее:

* локальное развертывание AD FS; полное пошаговое руководство по лаборатории тестирования, которая используется здесь, см. в записи блога [Test Lab: Standalone STS with AD FS in Azure VM (for test only)](https://blogs.msdn.microsoft.com/cephalin/2014/12/21/test-lab-standalone-sts-with-ad-fs-in-azure-vm-for-test-only/) (Лаборатория тестирования: автономная служба маркеров безопасности c AD FS на виртуальной машине Azure (только для тестирования));
* разрешения для создания отношений доверия с проверяющей стороной в оснастке управления AD FS
* Visual Studio 2013 с обновлением 4 или более поздней версии.
* [Пакет SDK Azure, начиная с версии 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) .

<a name="bkmk_sample"></a>

## <a name="use-sample-application-for-line-of-business-template"></a>Использование примера приложения в качестве шаблона бизнес-приложения
Пример приложения в этом учебнике, [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), создала рабочая группа Azure Active Directory. Так как службы AD FS поддерживают WS-Federation, их можно использовать как шаблон для быстрого создания бизнес-приложений. Они имеют следующие возможности:

* использование [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) для аутентификации с помощью локального развертывания служб федерации Active Directory;
* функции входа и выхода
* Использование [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (вместо Windows Identity Foundation, то есть WIF). Этот интерфейс определяет будущее ASP.NET. Настроить его для аутентификации и авторизации гораздо проще, чем WIF.

<a name="bkmk_setup"></a>

## <a name="set-up-the-sample-application"></a>настройка простого приложения
1. Создайте клон или скачайте пример решения [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) в локальный каталог.
   
   > [!NOTE]
   > Следуйте указаниям в файле [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) , чтобы настроить приложение с помощью Azure Active Directory. Но так как в этом руководстве показано, как настраивать приложение при помощи AD FS, вам нужно выполнить действия, которые описаны здесь.
   > 
   > 
2. Откройте решение, а затем откройте файл Controllers\AccountController.cs в **обозревателе решений**.
   
   Вы увидите, что код просто выдает запрос проверки подлинности для проверки подлинности пользователя с помощью WS-Federation. Вся проверка подлинности настраивается в App_Start\Startup.Auth.cs.
3. Откройте App_Start\Startup.Auth.cs. В методе `ConfigureAuth` найдите строку:
   
       app.UseWsFederationAuthentication(
           new WsFederationAuthenticationOptions
           {
               Wtrealm = realm,
               MetadataAddress = metadata                                      
           });
   
   В среде OWIN этот фрагмент действительно является абсолютным минимумом, который необходим для настройки проверки подлинности с помощью WS-Federation. Это гораздо более простой и элегантный способ, чем WIF, когда Web.config вставляется с помощью XML по всей программе. Единственное, что нам потребуется, это идентификатор проверяющей стороны(RP) и URL-адрес файла метаданных службы AD FS. Ниже приведен пример:
   
   * Идентификатор RP: `https://contoso.com/MyLOBApp`
   * Адрес метаданных: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`
4. В App_Start\Startup.Auth.cs замените такие статические определения строк:  
   
   <pre class="prettyprint">
   private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
   <mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
   <mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
   <mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
   <mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>
   
   <mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
   </pre>
5. Теперь сделайте соответствующие изменения в файле Web.config. Откройте файл Web.config и измените такие параметры приложения:  
   
   <pre class="prettyprint">
   &lt;appSettings&gt;
   &lt;add key="webpages:Version" value="3.0.0.0" /&gt;
   &lt;add key="webpages:Enabled" value="false" /&gt;
   &lt;add key="ClientValidationEnabled" value="true" /&gt;
   &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" /&gt;
   <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /&gt;</del></mark>
   <mark><del>&lt;add key="ida:AADInstance" value="https://login.microsoftonline.com" /&gt;</del></mark>
   <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /&gt;</del></mark>
   <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /&gt;</mark>
   <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /&gt;</mark>
   
   &lt;/appSettings&gt;
   </pre>
   
   Заполните значения ключа на базе данных вашей среды.
6. Постройте приложение, чтобы убедиться в отсутствии ошибок.

Вот и все. Теперь учебное приложение готово к работе с AD FS. Позднее вам все-таки потребуется настроить отношения доверия проверяющей стороны с этим приложением в AD FS.

<a name="bkmk_deploy"></a>

## <a name="deploy-the-sample-application-to-azure-app-service-web-apps"></a>Развертывание примера приложения в веб-приложениях службы приложений Azure
Здесь вы научитесь публиковать приложение в веб-приложениях службы приложений, сохраняя при этом среду отладки. Обратите внимание, что публикация приложения будет выполняться до установления отношений доверия проверяющей стороны с AD FS, поэтому проверка подлинности еще не работает. Однако если вы опубликуете приложение сейчас, вы получите URL-адрес веб-приложения, который позднее также будет использоваться для настройки отношения доверия с проверяющей стороной.

1. Щелкните правой кнопкой мыши свой проект и выберите **Опубликовать**.
   
    ![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)
2. Щелкните **Служба приложений Microsoft Azure**.
3. Если вы еще не вошли в Azure, нажмите кнопку **Вход** и укажите данные учетной записи Майкрософт для своей подписки Azure.
4. После входа щелкните **Создать** , чтобы создать веб-приложение.
5. Заполните все обязательные поля. Подключение к локальным данным выполняется позднее, поэтому вам не нужно создавать базу данных для этого веб-приложения.
   
    ![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)
6. Щелкните **Создать**. После создания веб-приложения открывается диалоговое окно публикации в Интернете.
7. В поле **Конечный URL-адрес** замените **http** на **https**. Скопируйте весь URL-адрес в текстовый редактор для дальнейшего использования. Затем щелкните **Опубликовать**.
   
    ![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)
8. В Visual Studio откройте в своем проекте файл **Web.Release.config** . Вставьте следующий XML-код в тег `<configuration>` и замените значение ключа URL-адресом публикуемого веб-приложения.  
   
   <pre class="prettyprint">
   &lt;appSettings&gt;
   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;/appSettings&gt;</pre>

После завершения операции вы получите два идентификатора RP, настроенных в проекте: один для среды отладки в Visual Studio, а второй для опубликованного веб-приложения в Azure. Доверие с проверяющей стороной (RP) будет необходимо настроить для каждой из двух сред в AD FS. Во время отладки параметры приложения в файле Web.config используются, чтобы ваша конфигурация **отладки** работала с AD FS. При его публикации (по умолчанию публикуется конфигурация **Выпуск** ) отправляется измененный файл Web.config, в котором содержатся изменения параметров приложения в файле Web.Release.config.

Если требуется подключить опубликованное веб-приложение в Azure к отладчику (т.е. когда нужно отправить символы отладки кода в опубликованное веб-приложение), можно создать клон конфигурации отладки для отладки в Azure. Этот клон должен иметь собственный настраиваемый файл преобразования Web.config (например, Web.AzureDebug.config), в котором используются параметры приложения из файла Web.Release.config. Это дает возможность поддерживать статическую конфигурацию в различных средах.

<a name="bkmk_rptrusts"></a>

## <a name="configure-relying-party-trusts-in-ad-fs-management"></a>Настройка доверия с проверяющей стороной в оснастке управления AD FS
Теперь необходимо настроить отношение доверия с проверяющей стороной в оснастке управления AD FS, чтобы в вашем примере приложения действительно выполнялась проверка подлинности с помощью AD FS. Для этого необходимо настроить два различных отношения доверия с проверяющей стороной: одно для среды отладки и одно для опубликованного веб-приложения.

> [!NOTE]
> Не забудьте выполнить указанные ниже действия для обеих сред.
> 
> 

1. На сервере AD FS выполните вход с использование учетных данных, имеющих права управления для AD FS.
2. Откройте оснастку управления AD FS. Щелкните правой кнопкой мыши **AD FS\Trusted Relationships\Relying Party Trusts** и выберите **Добавить отношение доверия проверяющей стороны**.
   
   ![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)
3. На странице **Выбрать источник данных** выберите **Ввод данных о проверяющей стороне вручную**. 
   
   ![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)
4. На странице **Указание отображаемого имени** введите отображаемое имя для приложения и щелкните **Далее**.
5. На странице **Выбор протокола** щелкните **Далее**.
6. На странице **Настройка сертификата** щелкните **Далее**.
   
   > [!NOTE]
   > Так как уже должен использоваться протокол HTTPS, зашифрованные токены необязательны. Если действительно необходимо шифровать токены из AD FS на этой странице, нужно добавить логику расшифровки токенов в код. Дополнительную информацию см. в статье [Ручная настройка промежуточного слоя OWIN WS-Federation и прием зашифрованных маркеров](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
   > 
   > 
7. Прежде чем переходить к следующему шагу, потребуется часть информации и проекта Visual Studio. В свойствах проекта обратите внимание на **URL-адрес SSL** приложения. 
   
   ![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)
8. Перейдите снова в оснастку управления AD FS. На странице **Настройка URL-адреса** в **мастера добавления отношения доверия с проверяющей стороной** выберите **Включить поддержку пассивного протокола WS-Federation** и введите URL-адрес SSL проекта Visual Studio, который вы записали на предыдущем шаге. Затем щелкните **Далее**.
   
   ![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)
   
   > [!NOTE]
   > URL-адрес указывает, куда отправлять клиента после успешной проверки подлинности. Для среды отладки это должен быть <code>https://localhost:&lt;port&gt;/</code>. Для опубликованного веб-приложения это должен быть URL-адрес веб-приложения.
   > 
   > 
9. На странице **Настройка идентификаторов** убедитесь, что URL-адрес SSL проекта уже есть в списке, и щелкните **Далее**. Щелкайте **Далее** на всех страницах мастера до конца, выбирая значения по умолчанию.
   
   > [!NOTE]
   > В App_Start\Startup.Auth.cs проекта Visual Studio этот идентификатор сопоставляется со значением <code>WsFederationAuthenticationOptions.Wtrealm</code> во время федеративной аутентификации. По умолчанию URL-адрес приложения из предыдущего шага добавляется как идентификатор RP.
   > 
   > 
10. Настройка приложения RP для проекта в AD FS завершена. Далее необходимо настроить это приложение, чтобы оно отправляло утверждения, необходимые вашему приложению. При окончании работы мастера по умолчанию открывается диалоговое окно **Изменение правил для утверждений** и можно сразу приступить к настройке. Как минимум, настроим следующие утверждения (со схемами в круглых скобках):
    
    * Имя (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) — используется ASP.NET для расконсервации `User.Identity.Name`.
    * Имя участника-пользователя (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) — используется для уникальной идентификации пользователей в организации.
    * Членства в группах в качестве ролей (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) — могут использоваться с декорированием `[Authorize(Roles="role1, role2,...")]` для авторизации контроллеров или действий. Фактически этот подход может не обеспечивать максимальную производительность для авторизации роли. Если службой AD пользуются участники сотен групп безопасности, они генерируют сотни утверждений роли в токене SAML. В качестве альтернативного подхода можно отправить одной утверждение роли, условно зависящее от членства пользователя в определенной группе. Однако в нашем учебнике мы будем придерживаться простого подхода.
    * ИД имени (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) — используется для проверки защиты от подделки. Дополнительную информацию о настройке проверки защиты от подделки см. в разделе **Добавление функциональности бизнес-приложения к примеру приложения** статьи [Создание бизнес-приложения Azure с проверкой подлинности Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud).
    
    > [!NOTE]
    > Типы утверждений, которые необходимо настроить для приложения определяются его потребностями. Список утверждений, поддерживаемых приложениями Azure Active Directory (т. е. отношениями доверия RP), см. в разделе [Справочник по токенам в Azure AD](http://msdn.microsoft.com/library/azure/dn195587.aspx).
    > 
    > 
11. В диалоговом окне «Изменение правил для утверждений» щелкните **Добавить правило**.
12. Задайте имя, имя участника-пользователя и утверждения роли, ориентируясь на этот снимок экрана, и щелкните **Готово**.
    
    ![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)
    
    Теперь необходимо создать временное утверждение идентификатора имени, используя шаги, описанные в записи блога [Name Identifiers in SAML assertions](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)(Идентификаторы имен в утверждениях SAML).
13. Щелкните **Добавить правило** еще раз.
14. Выберите **Отправка утверждений с помощью настраиваемого правила** и нажмите кнопку **Далее**.
15. Вставьте следующий код правила в поле **Настраиваемое правило**, присвойте правилу имя **Per Session Identifier** (В соответствии с идентификатором сеанса) и нажмите кнопку **Готово**.  
    
    <pre class="prettyprint">
    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
     => add(
         store = "_OpaqueIdStore",
         types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
         query = "{0};{1};{2};{3};{4}",
         param = "useEntropy",
         param = c1.Value,
         param = c1.OriginalIssuer,
         param = "",
         param = c2.Value);
    </pre>
    
    Настраиваемое правило должно выглядеть, как на этом снимке экрана.
    
    ![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)
16. Щелкните **Добавить правило** еще раз.
17. Выберите **Преобразование входящего утверждения** и нажмите кнопку **Далее**.
18. Настройте правило, как показано на этом снимке экрана (используя тип утверждения, созданный в настраиваемом правиле), и нажмите кнопку **Готово**.
    
    ![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)
    
    Подробную информацию о поэтапном создании временного утверждения идентификатора имени см. в записи блога [Name Identifiers in SAML assertions](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx) (Идентификаторы имен в утверждениях SAML).
19. Щелкните **Применить** в диалоговом окне **Изменить правила утверждений**. Все должно выглядеть, как на снимке экрана ниже.
    
    ![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)
    
    > [!NOTE]
    > Не забудьте, что эти шаги необходимо повторить для среды отладки и опубликованного веб-приложения.
    > 
    > 

<a name="bkmk_test"></a>

## <a name="test-federated-authentication-for-your-application"></a>Тестирование федеративной проверки подлинности для приложения
Все готово для тестирования логики проверки подлинности приложения в AD FS. В моей лабораторной среде AD FS я использую тестового пользователя, который входит в состав тестовой группы в Active Directory (AD).

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Для аутентификации в отладчике достаточно просто ввести `F5`. Чтобы протестировать аутентификацию в опубликованном веб-приложении, перейдите по URL-адресу.

После загрузки веб-приложения щелкните **Вход**. Теперь должно появиться диалоговое окно входа или страница входа, обслуживаемые AD FS в зависимости от метода проверки подлинности который выбирается AD FS. Вот что получается в Internet Explorer 11.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

После входа с правами пользователя в домен AD развертывания AD FS должна опять отобразиться домашняя страница с сообщением **Hello, <User Name>!** ("Добро пожаловать, &lt;имя_пользователя&gt;!") в углу. Вот что у меня получается.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Итак, что нам удалось успешно сделать:

* приложение успешно получило доступ к AD FS, и в базе данных AD FS был найден совпадающий идентификатор RP
* AD FS успешно выполняют проверку подлинности пользователя AD и перенаправляют вас обратно на домашнюю страницу приложения
* Если AD FS успешно отправляет утверждение имени (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) приложению, это подтверждается тем, что имя пользователя отображается в углу. 

Если утверждение имени отсутствует, вы увидите **Здравствуйте, !**. В файле Views\Shared\_LoginPartial.cshtml можно увидеть, что для отображения имени пользователя здесь используется свойство `User.Identity.Name`. Как уже отмечалось ранее, ASP.NET заполняет это свойство утверждением имени пользователя, прошедшего проверку подлинности, если оно доступно в токене SAML. Чтобы просмотреть все утверждения, отправленные AD FS, вставьте точку останова в Controllers\HomeController.cs в методе действия индекса. После аутентификации пользователя проверьте коллекцию `System.Security.Claims.Current.Claims` .

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png) 

<a name="bkmk_authorize"></a>

## <a name="authorize-users-for-specific-controllers-or-actions"></a>Авторизация пользователей для определенных контроллеров и действий
Вы включили членство в группах в качестве утверждений ролей в вашей конфигурации отношения доверия с проверяющей стороной. Теперь вы можете использовать его непосредственно в оформлении `[Authorize(Roles="...")]` для контроллеров и действий. В бизнес-приложении с шаблоном создать- прочитать-обновить-удалить (CRUD) можно выполнять авторизацию определенных ролей для доступа к каждому действию. На сегодня просто попробуем, как работает эта функция на примере существующего контроллера Home.

1. Откройте Controllers\HomeController.cs.
2. Оформите методы действия `About` и `Contact` при помощи кода наподобие того, который показан ниже. При этом используйте членство в группе безопасности пользователя, прошедшего проверку подлинности.  
   
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
   
    Так как я добавил **тестового пользователя** в группу **Test Group** в своей лабораторной среде AD FS, я буду использовать эту тестовую группу для проверки авторизации на `About`. Для `Contact` я проведу тестирование отрицательного случая группы **Domain Admins**, к которой не принадлежит **тестовый пользователь**.
3. Запустите отладчик, нажав клавишу `F5` , и войдите в систему, а затем нажмите кнопку **О программе**. Должна отобразиться страница `~/About/Index` , если прошедший аутентификацию пользователь авторизован для этого действия.
4. Далее щелкните **Контакт**, что в моем случае не должно авторизовать **тестового пользователя** для этого действия. Тем не менее, браузер перенаправляется к AD FS, в результате чего, в конечном счете, показывается это сообщение:
   
    ![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)
   
    При анализе этой ошибки в средстве просмотра событий на сервере AD FS можно увидеть такое сообщение об исключении:  
   
    <pre class="prettyprint">
    Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>The same client browser session has made '6' requests in the last '11' seconds.</mark> Contact your administrator for details.
       at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
       at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
       at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
       at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
    </pre>
   
    Это происходит потому, что по умолчанию MVC возвращает сообщение об ошибке "401 — Не авторизовано", когда роли пользователя не авторизованы. В результате активируется запрос на повторную проверку подлинности к поставщику удостоверений(AD FS). Так как пользователь уж прошел проверку подлинности, AD FS возвращается на ту же страницу, которая издает другую ошибку 401, создавая цикл перенаправления. Можно переопределить метод `HandleUnauthorizedRequest` в AuthorizeAttribute с помощью простой логики, чтобы отображать более содержательную информацию вместо продолжения цикла перенаправления.
5. Создайте файл в проекте AuthorizeAttribute.cs и вставьте в него приведенный ниже код.
   
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
   
    Код переопределения отправляет сообщение об ошибке "HTTP 403 — запрещено" вместо "401 — не авторизовано", когда аутентификация выполнена, а авторизация не выполнена.
6. Запустите отладчик снова, нажав клавишу `F5`. Щелкнув **Контакт** , теперь можно увидеть более информативное (хотя и непривлекательное) сообщение об ошибке:
   
    ![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)
7. Опубликуйте приложение в веб-приложениях службы приложений еще раз и протестируйте поведение развернутого приложения.

<a name="bkmk_data"></a>

## <a name="connect-to-on-premises-data"></a>Подключение к локальным данным
Одна из причин реализации бизнес-приложения с AD FS вместо Azure Active Directory заключается в проблемах обеспечения соответствия, когда организации требуется сохранять данные локально. Это может также означать, что у веб-приложения в Azure должен быть доступ к локальной базе данных, так как вам запрещено использовать [Базу данных SQL](/services/sql-database/) в качестве уровня данных для своих веб-сайтов.

Веб-приложения службы приложений Azure поддерживают доступ к локальным базам данных двумя способами: с помощью [гибридных подключений](../biztalk-services/integration-hybrid-connection-overview.md) и [виртуальных сетей](web-sites-integrate-with-vnet.md). Дополнительную информацию см. в статье [Использование интеграции VNET и гибридных подключений с веб-приложениями службы приложений Azure](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>Дополнительные ресурсы
* [Проверка подлинности в приложении Azure с помощью локального каталога Active Directory](web-sites-authentication-authorization.md)
* [Создание бизнес-приложения Azure с проверкой подлинности Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md)
* [Использование локальной аутентификации в организации (AD FS) с использованием ASP.NET в Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
* [Миграция веб-проекта VS2013 из WIF в Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
* [Обзор служб федерации Active Directory](http://technet.microsoft.com/library/hh831502.aspx)
* [Спецификация WS-Federation 1.1](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)


