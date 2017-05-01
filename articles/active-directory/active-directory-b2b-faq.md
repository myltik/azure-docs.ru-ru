---
title: "Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/13/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c06e8b98fea6036704b07b0f12397fc4bb59cb3f
ms.lasthandoff: 04/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B

Часто задаваемые вопросы периодически обновляются, чтобы отражать все новые интересы.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Эта функция доступна на классическом портале Azure?
Новые возможности службы совместной работы B2B доступны только на [портале Azure](https://portal.azure.com) и на новой панели доступа. 

### <a name="is-it-possible-to-customize-our-sign-in-page-so-its-more-intuitive-for-your-b2b-collaboration-guest-users"></a>Можно ли настроить более интуитивно понятную страницу входа для гостевых пользователей службы совместной работы B2B?
Конечно. См. сведения в [записи блога, описывающей эту функцию](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/), а также в статье [Добавление фирменной символики на страницу входа и панели доступа](active-directory-add-company-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Имеют ли пользователи службы совместной работы B2B доступ к SharePoint Online и OneDrive?
Да. Но возможность поиска существующих гостевых пользователей в средстве выбора людей в SharePoint Online отключена по умолчанию для согласования с поведением предыдущих версий. Вы можете включить эту функцию, используя параметр ShowPeoplePickerSuggestionsForGuestUsers на уровне коллекции клиента и сайта. Этот параметр можно задать с помощью командлетов SPOTenant и SPOSite, которые позволяют участникам искать всех существующих гостевых пользователей в каталоге. Изменения в области клиента не влияют на уже подготовленные сайты SharePoint Online.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>Поддерживается ли по-прежнему механизм отправки CSV-файла?
Да. Ознакомьтесь с [примером для PowerShell](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Как можно настроить сообщения с приглашением?
С помощью [интерфейсов API приглашения службы B2B](active-directory-b2b-api.md) можно настроить практически весь процесс приглашения.

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>Может ли приглашенный внешний пользователь покинуть организацию, в которую он был приглашен?
Сейчас это недоступно.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>Теперь, когда Многофакторная идентификация (MFA) доступна для гостевых пользователей, могут ли они также изменить метод MFA?
Да, таким же образом, как и обычные пользователи.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>Какая организации отвечает за лицензии MFA?
Приглашающая организация инициирует процесс и выполняет MFA. То есть приглашающая организация отвечает за то, чтобы для приглашенных пользователей службы B2B, выполняющих MFA, было достаточно лицензий.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>Что делать, если у нашей партнерской организации уже настроена Многофакторная идентификация? Можно ли доверять их проверке MFA и не применять нашу собственную?
Такая возможность будет реализована в будущих выпусках. Когда это произойдет, вы сможете выбрать конкретных партнеров и исключить их из своей проверки MFA (в приглашающей организации).

### <a name="how-can-i-achieve-delayed-invitations"></a>Как настроить оправку приглашений с отложенной доставкой?
Некоторым организациям требуется добавить пользователей службы совместной работы B2B, подготовить их для приложений, требующих подготовки, а затем отправить приглашения. Если вам требуется именно это, то воспользуйтесь интерфейсом API приглашения службы совместной работы B2B, чтобы настроить рабочий процесс регистрации.

### <a name="can-i-make-my-guest-users-limited-admins"></a>Можно ли назначить гостевых пользователей администраторами с ограниченными правами?
Конечно. Если это в интересах вашей организации, то узнайте, как это сделать, прочитав статью [Назначение пользователю ролей администратора в предварительной версии Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Можно ли с помощью службы совместной работы Azure AD B2B разрешить доступ пользователей службы B2B к порталу Azure?
Пользователям службы совместной работы B2B не нужен доступ к порталу Azure, только если им не назначена роль администратора с ограниченными правами или глобального администратора. В этом случае они имеют доступ к порталу. Если гостевой пользователь, не находящийся в этих ролях, получает доступ к порталу, то он может воспользоваться частью возможностей, так как у роли гостевого пользователя есть определенные разрешения в каталоге, как описано в предыдущих разделах.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Можно ли заблокировать доступ к порталу Azure для гостевых пользователей?
Да! Но будьте осторожны при настройке этой политики, чтобы случайно не заблокировать доступ для участников и администраторов.
Заблокировать доступ к [порталу Azure](https://portal.azure.com) для гостевых пользователей можно с помощью политики условного доступа в API управления службами Microsoft Azure. Для этого выполните следующие три действия:
1. Измените группу **Все пользователи** таким образом, чтобы в нее входили только участники. ![Снимок экрана, на котором показано, как изменить группу](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Создайте динамическую группу, в которую входят гостевые пользователи. ![Снимок экрана создания группы](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Настройте политику условного доступа, блокирующую доступ гостевых пользователей к порталу, как показано в следующем видео.
  
  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Когда начнется поддержка MFA и учетных записей электронной почты потребителей в службе совместной работы Azure AD B2B?
Поддержка MFA и учетных записей электронной почты потребителей уже реализована.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Планируется ли реализовать поддержку сброса паролей для пользователей службы совместной работы Azure AD B2B?
Да. Ниже приведены подробные сведения о самостоятельном сбросе пароля для пользователя B2B, приглашенного в клиент ресурса из своего клиента удостоверений.
 
* SSPR будет осуществляться только в клиенте удостоверений пользователя B2B.
* Если клиентом удостоверений является учетная запись Майкрософт, то используется механизм SSPR учетной записи Майкрософт.
* Когда клиентом удостоверений является JIT-клиент или вирусный клиент, будет отправлено сообщение электронной почты для сброса пароля.
* Для других пользователей будет выполнен стандартный процесс SSPR для пользователей B2B, как и для участников SSPR. Пользователи B2B будут заблокированы в контексте клиента ресурса.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>Эта функция также включена для пользователей в вирусном клиенте?
В настоящее время нет.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Предоставляет ли Microsoft CRM поддержку через Интернет для пользователей службы совместной работы B2B?
Мы работаем над тем, чтобы обеспечить поддержку этого сценария.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Какой срок действия у начального пароля для нового пользователя службы совместной работы B2B?
Azure AD имеет единые требования в отношении фиксированного набора знаков, надежности пароля и правил блокировки учетной записи, которые одинаково применяются ко всем облачным учетным записям Azure AD. Облачные учетные записи — это учетные записи, которые не входят в федерацию с другим поставщиком удостоверений, таким как учетная запись Майкрософт, Facebook, ADFS или даже другой облачный клиент (в случае службы совместной работы B2B). Для федеративных учетных записей политика паролей зависит от политики в локальном клиенте и от параметров учетной записи Майкрософт пользователя.

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>Приложениям требуется различать взаимодействие с пользователем клиента и гостевым пользователем. Есть ли стандартные рекомендации для этого? Можно ли использовать утверждение поставщика удостоверений в качестве оптимальной модели для этого?
 
Гостевой пользователь может использовать любой поставщик удостоверений для проверки подлинности, как описано в статье [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md). Поэтому с помощью свойства UserType можно определить тип пользователя. Утверждение UserType сейчас не включено в маркер. Приложения должны использовать API Graph, чтобы запросить пользователя в каталоге и получить для него свойство UserType.

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>Где можно найти сообщество службы совместной работы B2B для обмена решениями и публикации идей?

Мы всегда открыты для ваших отзывах о том, как улучшить службу совместной работы B2B. Мы приглашаем вас принять участие в обсуждении и делиться своими сценариями пользователей, рекомендациями и понравившимися возможностями службы совместной работы Azure AD B2B в [сообществе Microsoft Tech](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Мы также предлагаем вам отправлять свои идеи и голосовать за будущие функции на сайте [Azure Active Directory B2B Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) (Идеи для службы совместной работы Azure Active Directory B2B).

### <a name="is-there-a-way-to-invite-the-user-such-that-the-invitation-is-automatically-redeemed-and-the-user-is-just-ready-to-go-or-will-the-user-always-have-to-click-through-to-the-redemption-url"></a>Можно ли пригласить пользователя таким образом, чтобы приглашение активировалось автоматически и пользователь мог приступить к работе, или ему всегда нужно будет щелкать URL-адрес активации?
Приглашения, отправленные пользователем в приглашающей организации (который также является участником этой организации (организации пользователя службы B2B)), не требуют активации пользователем B2B.

Для этого мы советуем пригласить одного пользователя, который является участником приглашающей организации, в приглашающую организацию. [В ресурсной организации назначьте этому пользователю роль Guest Inviter](active-directory-b2b-add-guest-to-role.md). Пользователь может приглашать других пользователей в приглашающей организации через пользовательский интерфейс входа, сценарии PowerShell или API. При этом пользователям службы B2B этой организации не нужно активировать свои приглашения.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Как работает служба совместной работы B2B, когда приглашенный партнер использует федерацию для добавления собственной локальной аутентификации?
Если у партнера есть клиент Azure AD, включенный в федерацию с инфраструктурой локальной аутентификации, то локальный единый вход будет выполняться автоматически. Если у партнера нет клиента Azure AD, то для вошедших пользователей создается учетная запись Azure AD. 

### <a name="i-didnt-think-azure-ad-b2b-would-accept-gmailcom-and-outlookcom-email-addresses-b2c-was-what-you-used-for-those"></a>Меня удивило, что Azure AD B2B поддерживает адреса электронной почты gmail.com и outlook.com. Раньше для этого использовалась служба B2C.
Мы устраняем различия между B2B и B2C касательно поддерживаемых удостоверений. Используемое удостоверение не является веской причиной, чтобы выбрать между использованием B2B или B2C. Рекомендации по выбору типа службы см. в статье [Сравнение службы совместной работы B2B и B2C в Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Какие приложения и службы поддерживают гостевых пользователей Azure B2B?
Все приложения, интегрированные с Azure AD. 

### <a name="is-it-possible-to-force-mfa-for-b2b-guest-users-if-partners-have-no-mfa-enabled"></a>Можно ли принудительно выполнять MFA для гостевых пользователей B2B, если эта функция не включена у партнеров?
Да. Дополнительные сведения см. в статье [Многофакторная идентификация пользователей службы совместной работы Azure Active Directory B2B](active-directory-b2b-mfa-instructions.md).

### <a name="within-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-any-plans-to-extend-this-to-azure-or-across-all-of-office-365"></a>С помощью SharePoint можно определить список разрешений и запретов для внешних пользователей. Планируется ли внедрение этой функции в Azure или во всех приложениях Office 365?
Да. Служба совместной работы Azure AD B2B поддерживает функции allowlist и denylist. 

### <a name="what-licenses-are-needed-for-azure-ad-b2b"></a>Какие лицензии необходимы для Azure AD B2B?
См. сведения в статье [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Дальнейшие действия

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](active-directory-b2b-admin-add-users.md)
* [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](active-directory-b2b-iw-add-users.md)
* [Элементы сообщения с приглашением в службу совместной работы B2B](active-directory-b2b-invitation-email.md)
* [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
* [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md)
* [Устранение неполадок службы совместной работы Azure Active Directory B2B](active-directory-b2b-troubleshooting.md)
* [API службы совместной работы Azure Active Directory B2B и настройка](active-directory-b2b-api.md)
* [Многофакторная идентификация для пользователей службы совместной работы B2B](active-directory-b2b-mfa-instructions.md)
* [Добавление пользователей службы совместной работы B2B без приглашения](active-directory-b2b-add-user-without-invite.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)

