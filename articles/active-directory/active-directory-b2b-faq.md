---
title: Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B.
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/06/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2f039361bce9615e8b8b9aa62dc152db504a6dc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B

Эти вопросы о службе совместной работы Azure Active Directory (Azure AD) B2B периодически обновляются для добавления новых разделов.

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Можно ли настроить более интуитивно понятную страницу входа для гостевых пользователей службы совместной работы B2B?
Конечно. Ознакомьтесь с [записью блога об этой функции](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Дополнительные сведения о настройке страницы входа вашей организации см. в статье [Добавление фирменной символики на страницу входа и панели доступа](customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Имеют ли пользователи службы совместной работы B2B доступ к SharePoint Online и OneDrive?
Да. Но возможность поиска имеющихся гостевых пользователей в средстве выбора людей в SharePoint Online **отключена** по умолчанию. Чтобы включить поиск имеющихся гостевых пользователей, установите для параметра **ShowPeoplePickerSuggestionsForGuestUsers** значение **Включено**. Вы можете включить этот параметр на уровне клиента или коллекции сайта. Его можно изменить с помощью командлетов Set-SPOTenant и Set-SPOSite. С помощью этих командлетов участники могут выполнить в каталоге поиск всех имеющихся гостевых пользователей. Изменения в области клиента не влияют на уже подготовленные сайты SharePoint Online.

### <a name="is-the-csv-upload-feature-still-supported"></a>Поддерживается ли по-прежнему функция отправки CSV-файла?
Да. Дополнительные сведения об использовании функции отправки CSV-файла см. в статье [Примеры кода и команд PowerShell для службы совместной работы Azure Active Directory B2B](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Как можно настроить сообщения с приглашением?
С помощью [интерфейсов API приглашения службы B2B](active-directory-b2b-api.md) можно настроить практически весь процесс приглашения.

### <a name="can-an-invited-external-user-leave-the-organization-after-being-invited"></a>Может ли приглашенный внешний пользователь покинуть организацию после приглашения?
Администратор приглашающей организации может удалить гостевого пользователя службы совместной работы B2B из каталога, но гостевой пользователь не может сам покинуть каталог приглашающей организации. 

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Могут ли гостевые пользователи сбросить настройки метода Многофакторной идентификации?
Да. Они могут это сделать таким же образом, как и обычные пользователи.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Какая организация отвечает за лицензии Многофакторной идентификации?
Многофакторную идентификацию выполняет приглашающая организация. То есть приглашающая организация отвечает за то, чтобы для приглашенных пользователей службы B2B, выполняющих MFA, было достаточно лицензий.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Что делать, если в партнерской организации уже настроена Многофакторная идентификация? Можно ли доверять их MFA и не применять собственную?
Мы планируем реализовать такую возможность в будущих выпусках. Когда это произойдет, вы сможете выбрать конкретных партнеров и исключить их из своей многофакторной проверки подлинности (в приглашающей организации).

### <a name="how-can-i-use-delayed-invitations"></a>Как настроить оправку приглашений с отложенной доставкой?
Организации может потребоваться добавить пользователей службы совместной работы B2B, подготовить их для приложений (при необходимости), а затем отправить приглашения. Вы можете воспользоваться API-интерфейсом приглашения службы совместной работы B2B, чтобы настроить рабочий процесс подключения.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Можно ли назначить гостевого пользователя администратором с ограниченными правами?
Конечно. Дополнительные сведения см. в статье [Назначение пользователю ролей администратора в предварительной версии Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Можно ли с помощью службы совместной работы Azure AD B2B разрешить доступ пользователей службы B2B к порталу Azure?
Пользователям службы совместной работы B2B не нужен доступ к порталу Azure, только если им не назначена роль администратора с ограниченными правами или глобального администратора. В противном случае они имеют доступ к порталу. Кроме того, если гостевой пользователь, которому не назначены эти роли, получает доступ к порталу, он может воспользоваться частью возможностей, так как у роли гостевого пользователя есть некоторые разрешения в каталоге.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Можно ли заблокировать доступ к порталу Azure для гостевых пользователей?
Да! Но будьте осторожны при настройке этой политики, чтобы случайно не заблокировать доступ для участников и администраторов.
Чтобы заблокировать доступ к [порталу Azure](https://portal.azure.com) для гостевых пользователей, используйте политику условного доступа в API классической модели развертывания Microsoft Azure.
1. Измените группу **Все пользователи** таким образом, чтобы в нее входили только участники.
  ![Снимок экрана, на котором показано, как изменить группу](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Создайте динамическую группу, в которую входят гостевые пользователи.
  ![Снимок экрана создания группы](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Настройте политику условного доступа, блокирующую доступ гостевых пользователей к порталу, как показано в следующем видео.
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Поддерживает ли служба совместной работы Azure AD B2B Многофакторную идентификацию и учетные записи электронной почты потребителей?
Да. В этой службе реализована поддержка MFA и учетных записей электронной почты потребителей.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Планируется ли реализовать поддержку сброса паролей для пользователей службы совместной работы Azure AD B2B?
Да. Ниже приведены важные сведения о самостоятельном сбросе пароля (SSPR) для пользователя B2B, приглашенного партнерской организацией.
 
* SSPR осуществляется только в клиенте удостоверений пользователя B2B.
* Если клиентом удостоверений является учетная запись Майкрософт, используется механизм SSPR учетной записи Майкрософт.
* Если клиентом удостоверений является JIT-клиент или вирусный клиент, будет отправлено электронное сообщение для сброса пароля.
* В других клиентах выполняется стандартный процесс SSPR для пользователей B2B. Так как пользователи B2B являются участниками SSPR, в контексте ресурса их участие в клиенте блокируется. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>Доступен ли сброс паролей в JIT- или вирусном клиенте для пользователей, принявших приглашения через рабочий или учебный электронный адрес, но у которых нет учетной записи Azure AD?
Да. Вы можете отправить электронное сообщение для сброса пароля, с помощью которого пользователь сможет сбросить пароль в JIT-клиенте.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Предоставляет ли Microsoft Dynamics 365 поддержку через Интернет для пользователей службы совместной работы Azure AD B2B?
Да, Dynamics 365 (Интернет) обеспечивает поддержку службы совместной работы Azure AD B2B. Дополнительные сведения см. в статье о Dynamics 365 [Приглашение пользователей с помощью службы совместной работы Azure Active Directory B2B](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Какой срок действия у начального пароля для нового пользователя службы совместной работы B2B?
Azure AD имеет единые требования в отношении фиксированного набора знаков, надежности пароля и правил блокировки учетной записи, которые одинаково применяются ко всем облачным учетным записям Azure AD. Облачные учетные записи — это учетные записи, которые не входят в федерацию с другими поставщиками удостоверений, такими как: 
* Учетная запись Майкрософт
* Facebook
* службы федерации Active Directory;
* другой облачный клиент (в случае службы совместной работы B2B).

Для федеративных учетных записей политика паролей зависит от политики, применяемой в локальном клиенте, и от параметров учетной записи Майкрософт пользователя.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Организации может потребоваться предоставлять различные возможности в приложениях для пользователей клиентов и гостевых пользователей. Есть ли стандартные рекомендации для этого? Можно ли использовать утверждение поставщика удостоверений в качестве оптимальной модели?
 Чтобы пройти проверку подлинности, гостевой пользователь может использовать любой поставщик удостоверений. Дополнительные сведения см. в статье [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md). Используйте свойство **UserType**, чтобы определить возможности для пользователя. Утверждение **UserType** сейчас не включено в токен. Приложения должны использовать API Graph, чтобы запросить пользователя в каталоге и получить для него свойство UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Где можно найти сообщество службы совместной работы B2B для обмена решениями и публикации идей?
Мы всегда открыты для ваших отзывов о том, как улучшить службу совместной работы B2B. Мы приглашаем вас поделиться своими сценариями пользователей, рекомендациями и понравившимися возможностями службы совместной работы Azure AD B2B. Примите участие в обсуждении в [сообществе Microsoft Tech](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Мы также предлагаем вам отправлять свои идеи и голосовать за будущие функции на сайте с [идеями для службы совместной работы B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Можно ли отправить активированное автоматически приглашение таким образом, чтобы пользователь мог приступить к работе? Или ему всегда нужно будет щелкать URL-адрес активации?
Приглашения, отправленные пользователем из приглашающей организации, который также является участником партнерской организации, не требуют активации пользователем B2B.

Мы рекомендуем пригласить одного пользователя, который является участником партнерской организации, в приглашающую организацию. [В ресурсной организации назначьте этому пользователю роль Guest Inviter](active-directory-b2b-add-guest-to-role.md). Пользователь может приглашать других пользователей в партнерскую организацию, используя пользовательский интерфейс входа, скрипты PowerShell или API-интерфейсы. При этом пользователям службы совместной работы B2B этой организации не нужно активировать свои приглашения.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Как работает служба совместной работы B2B, когда приглашенный партнер использует федерацию для добавления собственной локальной аутентификации?
Если у партнера есть клиент Azure AD, включенный в федерацию с инфраструктурой локальной проверки подлинности, локальный единый вход будет выполняться автоматически. Если у партнера нет клиента Azure AD, для новых пользователей создается учетная запись Azure AD. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Действительно ли служба Azure AD B2B не поддерживает электронные адреса gmail.com и outlook.com, ведь для этих типов учетных записей используется служба B2C?
Мы устраняем различия между службами совместной работы B2B и B2C в поддерживаемых удостоверениях. Используемое удостоверение не является веской причиной в пользу выбора B2B или B2C. Дополнительные сведения о выборе варианта совместной работы см. в статье [Сравнение службы совместной работы B2B и B2C в Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Какие приложения и службы поддерживают гостевых пользователей Azure B2B?
Все приложения, интегрированные с Azure AD, поддерживают гостевых пользователей Azure B2B. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Можно ли принудительно выполнять Многофакторную идентификацию для гостевых пользователей B2B, если эта функция не включена у партнеров?
Да. Дополнительные сведения см. в статье [Условный доступ пользователей в службе совместной работы B2B](active-directory-b2b-mfa-instructions.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>С помощью SharePoint можно определить список разрешений и запретов для внешних пользователей. Возможно ли это сделать в Azure?
Да. Служба совместной работы Azure AD B2B поддерживает списки разрешений и запретов. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Какие лицензии необходимо использовать в Azure AD B2B?
Дополнительные сведения о лицензиях, необходимых вашей организации для использования Azure AD B2B, см. в статье [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Дополнительная информация

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
