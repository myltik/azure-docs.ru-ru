---
title: Получение сертификата AppSource для Azure Active Directory | Документация Майкрософт
description: Сведения о получении сертификата AppSource приложения для Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: 844c19803f57987f4435d013cd12aa3d1ef8aeb8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257537"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Как получить сертификат AppSource для Azure Active Directory
С помощью [Microsoft AppSource](https://appsource.microsoft.com/) бизнес-пользователи могут обнаруживать бизнес-приложения SaaS (автономные приложения SaaS и дополнения к имеющимся продуктам SaaS Microsoft), использовать их и управлять ими.

Чтобы вывести список автономных приложений SaaS на AppSource, приложение должно принять единый вход из рабочих учетных записей любой компании или организации с Azure Active Directory. В процессе входа необходимо использовать протоколы [OpenID Connect](./active-directory-protocols-openid-connect-code.md) или [OAuth 2.0](./active-directory-protocols-oauth-code.md). Для сертификации AppSource интеграции SAML не принимается.

## <a name="guides-and-code-samples"></a>Руководства и примеры кода
Если вы хотите узнать о том, как интегрировать приложения с Azure Active Directory с помощью Open ID Connect, следуйте инструкциям и используйте примеры кода из [руководства разработчика Azure Active Directory](./active-directory-developers-guide.md#get-started "Начало работы").

## <a name="multi-tenant-applications"></a>Мультитенантные приложения

Приложение, принимающее вход пользователя из любой компании или организации с Azure Active Directory, не требуя отдельного экземпляра, конфигурации и развертывания, называется *многопользовательским приложением*. AppSource рекомендует реализовать поддержку мультитенантности в приложениях, чтобы добавить бесплатную пробную версию возможности входа *одним щелчком*.

Чтобы включить мультитенантность в приложении, сделайте следующее:
- Задайте для свойства `Multi-Tenanted` значение `Yes` в сведениях о регистрации приложения на [портале Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (по умолчанию, приложения, созданные на портале Azure, настроены как *приложения с одним клиентом*).
- Обновите код, чтобы отправлять запросы к конечной точке `common` (измените конечную точку *https://login.microsoftonline.com/{yourtenant}* на *https://login.microsoftonline.com/common*).
- Для некоторых платформ, например ASP.NET, необходимо также обновить код, чтобы принимать нескольких издателей.

Дополнительные сведения о мультитенантности см. в статье [Как реализовать вход любого пользователя Azure Active Directory (AD) с помощью шаблона мультитенантного приложения](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Приложения с одним клиентом
Приложения, которые принимают вход пользователей только из определенного экземпляра Azure Active Directory, называются *приложениями с одним клиентом*. Внешние пользователи (включая рабочие или учебные учетные записи из других организаций или личные учетные записи) могут войти в приложение с одним клиентом после добавления каждого пользователя в качестве *гостевой учетной записи* в экземпляр Azure Active Directory, в котором регистрируется приложение. В Azure Active Directory пользователей можно добавлять в качестве гостевых учетных записей через [*службу совместной работы Azure AD B2B*](../b2b/what-is-b2b.md). Это можно сделать [программным образом](../b2b/code-samples.md). При добавлении пользователя в качестве гостевой учетной записи в Azure Active Directory ему отправляется письмо с приглашением, которое он должен принять, щелкнув ссылку в этом письме. Дополнительному пользователю в приглашающей организации, который также входит в партнерскую организацию, не нужно принимать приглашение для входа.

В приложениях с одним клиентом можно включить возможность *Свяжитесь со мной*. Однако если требуется включить вход одним щелчком или пробную версию возможности входа, рекомендуемые AppSource, включите в приложении мультитенантность.


## <a name="appsource-trial-experiences"></a>Пробные версии возможностей AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Бесплатная пробная версия возможности (для заказчика) 
*Пробная версия возможности для заказчика* рекомендуется AppSource, так как она позволяет воспользоваться доступом к приложению одним щелчком. Ниже показано, как выглядит эта возможность.<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Пользователь находит ваше приложение на веб-сайте AppSource.</li><li>Затем выбирает "Бесплатная пробная версия".</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource перенаправляет пользователя по URL-адресу вашего веб-сайта.</li><li>На вашем веб-сайте автоматически запускается <i>единый вход</i> (при загрузке страницы).</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Пользователь перенаправляется на страницу входа Майкрософт.</li><li>Пользователь указывает учетные данные для входа.</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Пользователь дает согласие для приложения.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Вход выполняется и пользователь перенаправляется на ваш веб-сайт.</li><li>Пользователь запускает бесплатную пробную версию.</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Функция "Связаться со мной" (партнерская пробная версия)
*Партнерскую пробную версию* можно использовать для операций, выполняемых вручную, длительных операций для подготовки пользователя или компании, например, когда приложению необходимо подготовить виртуальные машины или экземпляры базы данных. В этом случае если пользователь нажмет кнопку *Request Trial* (Запросить пробную версию) и заполнит форму, AppSource отправит вам контактные данные пользователя. Получив эти сведения, вы подготавливаете среду и отправляете пользователю инструкции по получению доступа к пробной версии:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Пользователь находит ваше приложение на веб-сайте AppSource.</li><li>Выбирает параметр "Связаться со мной".</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Заполняет форму контактными данными.</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Вы получаете сведения о пользователе.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Настраиваете среду.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Связываетесь с пользователем по поводу информации о пробной версии.</td>
        </tr>
        </table><br/><br/>
        <ul><li>Вы получаете сведения пользователя и настраиваете пробный экземпляр.</li><li>Вы отправляете пользователю гиперссылку на получение доступа к приложению.</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Пользователь получает доступ к приложению и завершает процесс единого входа.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Пользователь дает согласие для приложения.</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Вход выполняется и пользователь перенаправляется на ваш веб-сайт.</li><li>Пользователь запускает бесплатную пробную версию.</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Дополнительные сведения
Дополнительные сведения о пробной версии AppSource см. [в этом видео](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о разработке приложений, поддерживающих вход с помощью Azure Active Directory, см. в статье [Сценарии аутентификации в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios). 

- Сведения о выводе списка приложений SaaS в AppSource см. на [странице сведений для партнеров AppSource](https://appsource.microsoft.com/partners).


## <a name="get-support"></a>Получение поддержки
Для интеграции Azure Active Directory мы используем [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource). Кроме того, участники сообщества могут предоставить поддержку. 

Мы настоятельно рекомендуем сначала задавать вопросы на сайте Stack Overflow и просмотреть имеющиеся проблемы, чтобы узнать, не задавал ли кто-то аналогичные вопросы раньше. Пометьте вопросы и комментарии тегами [`[azure-active-directory]` и `[appsource]`](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->