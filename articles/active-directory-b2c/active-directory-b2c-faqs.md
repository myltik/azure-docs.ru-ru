---
title: "Azure Active Directory B2C: часто задаваемые вопросы | Документация Майкрософт"
description: "Часто задаваемые вопросы по Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 351149296a6d7dfa801b295ec21fc04215c7b051
ms.openlocfilehash: ac2730935d206ddf9079395384d46a43fdd740cb


---
# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C: часто задаваемые вопросы
На этой странице вы найдете ответы на часто задаваемые вопросы об Azure Active Directory (Azure AD) B2C. Следите за обновлениями.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Можно ли использовать возможности Azure AD B2C в существующем клиенте Azure AD для работы с сотрудниками?
В настоящее время функции Azure AD B2C нельзя активировать в существующем клиенте Azure AD. Чтобы воспользоваться функциями Azure AD B2C для работы с потребителями, рекомендуется создать отдельный клиент.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Можно ли использовать Azure AD B2C для входа в Office 365 с помощью учетных данных социальных сетей (Facebook и Google+)?
Службу Azure AD B2C нельзя использовать с Microsoft Office 365. В общем случае она не может использоваться для проверки подлинности в приложениях SaaS (O365, Salesforce, Workday и т. д.). С ее помощью можно лишь управлять удостоверениями и доступом в веб-приложениях и мобильных приложениях для работы с клиентами. Она не предназначена для использования с решениями для работы с сотрудниками или партнерами.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Что такое локальные учетные записи в Azure AD B2C? Чем они отличаются от рабочих или учебных учетных записей в Azure AD?
В клиенте Azure AD все пользователи (кроме тех, у кого есть учетная запись Майкрософт) выполняют вход с помощью адреса электронной почты в формате `<xyz>@<tenant domain>`, где `<tenant domain>` — один из проверенных доменов в клиенте или исходный домен `<...>.onmicrosoft.com`. Тип этой учетной записи — рабочая или учебная учетная запись.

В клиенте Azure AD B2C большинство приложений поддерживают вход с использованием любого электронного адреса (например, joe@comcast.net,, bob@gmail.com,, sarah@contoso.com, или jim@live.com)). Учетная запись такого типа называется локальной учетной записью. Сейчас в качестве локальных учетных записей также поддерживаются произвольные имена пользователей (строки обычного текста), например joe, bob, sarah или jim. Для использования в службе Azure AD B2C можно выбрать один из этих двух типов локальных учетных записей.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Какие поставщики удостоверений социальных сетей поддерживаются в настоящее время? Каких из них вы планируете поддерживать в будущем?
В настоящее время поддерживаются Facebook, Google+, LinkedIn и Amazon. Мы добавим поддержку других популярных поставщиков удостоверений социальных сетей с учетом запросов клиентов.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Можно ли настроить в разделе "Области" сбор дополнительной информации о клиентах от различных поставщиков удостоверений социальных сетей?
Нет, но мы планируем добавить эту функцию в будущем. Области по умолчанию, используемые для поддерживаемых поставщиков удостоверений социальных сетей:

* Facebook: адрес электронной почты;
* Google+: адрес электронной почты;
* Учетная запись Майкрософт: профиль электронной почты OpenID
* Amazon: профиль;
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Нужно ли запускать приложение в Azure, чтобы оно работало с Azure AD B2C?
Нет, приложение можно разместить где угодно (в облаке или локально). Для взаимодействия с Azure AD B2C необходима только возможность отправки и получения HTTP-запросов в общедоступных конечных точках.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>У меня есть несколько каталогов Azure AD B2C. Как управлять ими в портале Azure?
В портале Azure для каждого клиента Azure AD B2C есть своя колонка с функциями B2C. Чтобы узнать, как перейти к колонке функций B2C конкретного клиента на портале Azure, см. статью [Azure Active Directory B2C: регистрация приложения](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade). В большинстве браузеров после переключения между каталогами Azure AD B2C в портале Azure колонка функций B2C закрывается.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Как настроить проверочные сообщения электронной почты (содержание сообщения и поле "От:"), которые отправляет служба Azure AD B2C?
Вы можете использовать [функцию фирменной символики](../active-directory/active-directory-add-company-branding.md) , чтобы настроить содержимое проверочных электронных сообщений. В частности, можно настроить следующие два элемента электронного сообщения:

* **баннер с логотипом**: отображается в правом нижнем углу;
* **цвет фона**: отображается вверху.
  
    ![Снимок экрана с настроенным проверочным электронным сообщением](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

Подпись электронного сообщения содержит имя клиента B2C, которое было указано при создании клиента B2C. Это имя можно изменить, выполнив приведенные ниже инструкции.

* Войдите на [классический портал Azure](https://manage.windowsazure.com/) как администратор подписки.
* Перейдите к клиенту B2C.
* Откройте вкладку **Настройка** .
* Измените значение поля **Имя** в разделе **Свойства каталога**.
* В нижней части страницы нажмите кнопку **Сохранить** .

В настоящее время нет возможности изменить значение поля "От:" электронного сообщения. Если вы заинтересованы в данной возможности и полной настройке текста проверочного электронного сообщения, проголосуйте за это на сайте [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Как перенести в Azure AD B2C существующие имена пользователей, пароли и профили из своей базы данных?
Средство миграции можно создать с помощью API Graph Azure AD. Подробные сведения см. в разделе с [примером API Graph](active-directory-b2c-devquickstarts-graph-dotnet.md). В будущем мы планируем предоставлять различные готовые варианты и средства миграции.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Какая политика паролей используется для локальных учетных записей в Azure AD B2C?
Политика паролей Azure AD B2C для локальных учетных записей основана на Azure AD. В политиках регистрации, входа и сброса пароля Azure AD B2C предусмотрено использование надежного пароля, срок действия которого не истекает. Дополнительные сведения см. в статье [Политика паролей в Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Можно ли с помощью Azure AD Connect перенести в Azure AD B2C удостоверения клиентов, хранящиеся в локальном каталоге Active Directory?
Нет служба Azure AD Connect не предназначена для работы с Azure AD B2C. В будущем мы планируем предоставлять различные готовые варианты и средства миграции.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Может ли приложение открыть страницы Azure AD B2C в iFrame?
Нет, по соображениям безопасности страницы Azure AD B2C, которые обслуживаются в домене `login.microsftonline.com`, не открываются в iFrame. При любом взаимодействии с клиентами требуется перенаправление в Azure AD B2C.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Работает ли служба Azure AD B2C с системами CRM, например Microsoft Dynamics?
В настоящее время нет. Мы планируем интеграцию этих систем в будущем.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Работает ли Azure AD B2C с программой SharePoint On-Premises 2016 или более ранних версий?
В настоящее время нет. Azure AD B2C не поддерживает токены SAML 1.1, необходимые для работы порталов и приложений электронной коммерции, созданных на основе SharePoint On-Premises. Обратите внимание, что служба Azure AD B2C не используется для предоставления общего доступа внешнему партнеру SharePoint; для этого вам нужно решение [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) .

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Использовать службу Azure AD B2C или B2B для управления внешними удостоверениями?
Прочтите статью о том, как применять соответствующие функции в сценариях с использованием [внешних удостоверений](../active-directory/active-directory-b2b-compare-external-identities.md) .

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Какие функции отчетности и аудита есть в Azure AD B2C? Соответствуют ли они аналогичным функциям в Azure AD Premium?
Нет, Azure AD B2C не поддерживает набор отчетов, используемый в Azure AD Premium. В ближайшее время будут выпущены API базовой отчетности и аудита для Azure AD B2C.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Можно ли локализовать пользовательский интерфейс страниц Azure AD B2C? Какие языки поддерживаются?
В настоящее время служба Azure AD B2C доступна только на английском языке. Мы планируем развернуть функции локализации в ближайшее время.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Могу ли я использовать свои URL-адреса на страницах регистрации и входа в Azure AD B2C? Например, можно ли изменить URL-адрес с login.microsoftonline.com на login.contoso.com?
В настоящее время нет. Мы планируем добавить эту функцию в будущем. Учтите также, что вы не сможете проверить домен на вкладке **Домены** клиента на классическом портале Azure.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Как удалить клиент Azure AD B2C?
Для удаления клиента Azure AD B2C выполните следующие действия.

* Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) на портале Azure.
* Откройте колонки **Приложения**, **Поставщики удостоверений** и **Все политики**, а затем удалите из них все записи.
* Войдите на [классический портал Azure](https://manage.windowsazure.com/) как администратор подписки. Это та же рабочая или учебная учетная запись либо учетная запись Майкрософт, которая использовалась для регистрации в Azure.
* Перейдите к расширению Active Directory слева и щелкните свой клиент B2C.
* Откройте вкладку **Пользователи** .
* Затем выберите всех пользователей (кроме пользователя, от имени которого вы вошли, — администратора подписки). В нижней части страницы щелкните **Удалить**, а затем при появлении запроса нажмите кнопку **Да**.
* Откройте вкладку **Приложения** .
* В раскрывающемся поле **Показать** выберите **Приложения, которыми владеет моя компания** и щелкните флажок.
* В открывшемся списке вы увидите приложение с именем **b2c-extensions-app** . В нижней части страницы щелкните **Удалить**, а затем при появлении запроса нажмите кнопку **Да**.
* Еще раз перейдите к расширению Active Directory и выберите свой клиент B2C.
* Щелкните **Удалить** в нижней части страницы. Для завершения процесса следуйте инструкциям на экране.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Можно ли получить Azure AD B2C в составе Enterprise Mobility Suite?
Нет, Azure AD B2C — это служба Azure с оплатой по мере использования, не входящая в предложение Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Как сообщать о проблемах с Azure AD B2C?
Ознакомьтесь со статьей [Azure Active Directory B2C: регистрация запросов в службу поддержки](active-directory-b2c-support.md).

## <a name="more-information"></a>Дополнительные сведения
Ознакомьтесь также с действующими [ограничениями для текущей версии службы](active-directory-b2c-limitations.md).




<!--HONumber=Jan17_HO4-->


