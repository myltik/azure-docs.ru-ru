<properties
	pageTitle="Службы синхронизации Azure AD Connect: атрибуты, синхронизируемые с Azure Active Directory | Microsoft Azure"
	description="В этой статье перечислены атрибуты, которые синхронизируются с Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="markusvi;andkjell"/>


# Службы синхронизации Azure AD Connect: атрибуты, синхронизируемые с Azure Active Directory

В этой статье перечислены атрибуты, которые синхронизируются при помощи служб синхронизации Azure AD Connect.<br> Атрибуты сгруппированы по соответствующим приложениям Azure AD.


## Office 365 профессиональный плюс.

| Имя атрибута| Пользователь| Комментарий |
| --- | :-: | --- |
| AccountEnabled| X| Определяет, включена ли учетная запись.|
| cn| X| |
| displayName| X| |
| objectSID| X| Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD.|
| pwdLastSet| X| Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется как в синхронизации паролей, так и в федерации.|
| sourceAnchor| X| Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD.|
| usageLocation| X| Механическое свойство. Страна пользователя. Используется для назначения лицензии.|
| userPrincipalName| X| Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail].|


## Exchange Online

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Определяет, включена ли учетная запись.|
| assistant| X| X| | |
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| |
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| |
| msExchExtensionCustomAttribute2| X| X| X| |
| msExchExtensionCustomAttribute3| X| X| X| |
| msExchExtensionCustomAttribute4| X| X| X| |
| msExchExtensionCustomAttribute5| X| X| X| |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg-IsOrganizational| | | X| |
| objectSID| X| | X| Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется как в синхронизации паролей, так и в федерации.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Производный от groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| TelephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | Механическое свойство. Страна пользователя. Используется для назначения лицензии.|
| userCertificate| X| X| | |
| userPrincipalName| X| | | Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail].|
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |



## SharePoint Online

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Определяет, включена ли учетная запись.|
| authOrig| X| X| X| |
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется как в синхронизации паролей, так и в федерации.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Производный от groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| TelephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | Механическое свойство. Страна пользователя. Используется для назначения лицензии.|
| userPrincipalName| X| | | Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail].|
| wWWHomePage| X| X| | |

## Lync Online

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Определяет, включена ли учетная запись.|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD.|
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется как в синхронизации паролей, так и в федерации.|
| securityEnabled| | | X| Производный от groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| TelephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | Механическое свойство. Страна пользователя. Используется для назначения лицензии.|
| userPrincipalName| X| | | Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail].|
| wWWHomePage| X| X| | |


## Azure RMS

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Определяет, включена ли учетная запись.|
| cn| X| | X| Общее имя или псевдоним. Чаще всего префикс значения [mail].|
| displayName| X| X| X| Строка, представляющая имя. Часто отображается как понятное имя (имя и фамилия).|
| mail| X| X| X| Полный адрес электронной почты.|
| member| | | X| |
| objectSID| X| | X| Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD.|
| proxyAddresses| X| X| X| Механическое свойство. Используется в Azure AD. Содержит все дополнительные адреса электронной почты пользователя.|
| pwdLastSet| X| | | Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры.|
| securityEnabled| | | X| Производный от groupType.|
| sourceAnchor| X| X| X| Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD.|
| usageLocation| X| | | Механическое свойство. Страна пользователя. Используется для назначения лицензии.|
| userPrincipalName| X| | | Это имя участника-пользователя является именем пользователя для входа. Чаще всего соответствует значению [mail].|


## Intune

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Определяет, включена ли учетная запись.|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется как в синхронизации паролей, так и в федерации.|
| securityEnabled| | | X| Производный от groupType|
| sourceAnchor| X| X| X| Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD.|
| usageLocation| X| | | Механическое свойство. Страна пользователя. Используется для назначения лицензии.|
| userPrincipalName| X| | | Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail].|



## Dynamics CRM

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Определяет, включена ли учетная запись.|
| c| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD.|
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется как в синхронизации паролей, так и в федерации.|
| securityEnabled| | | X| Производный от groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD.|
| st| X| X| | |
| streetAddress| X| X| | |
| TelephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | Механическое свойство. Страна пользователя. Используется для назначения лицензии.|
| userPrincipalName| X| | | Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail].|

## Сторонние приложения
Ниже приведены атрибуты, которые можно использовать, если каталог Azure AD не используется для поддержки Office 365, Dynamics или Intune. Это небольшой набор основных атрибутов.

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Определяет, включена ли учетная запись.|
| cn| X| | X| |
| displayName| X| X| X| |
| givenName| X| X| | |
| mail| X| | X| |
| managedBy| | | X| |
| mailNickName| X| X| X| |
| member| | | X| |
| objectSID| X| | | Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD.|
| proxyAddresses| X| X| x| |
| pwdLastSet| X| | | Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется как в синхронизации паролей, так и в федерации.|
| sn| X| X| | |
| sourceAnchor| X| X| X| Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD.|
| usageLocation| X| | | Механическое свойство. Страна пользователя. Используется для назначения лицензии.|
| userPrincipalName| X| | | Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail].|


## Гибридная обратная запись Exchange
Эти атрибуты записываются обратно из Azure AD в локальную службу Active Directory при активации гибридного развертывания Exchange. В зависимости от установленной версии Exchange может синхронизироваться меньшее количество атрибутов.

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObject| X| | | Производный от cloudAnchor в Azure AD.|
| msExchArchiveStatus| X| | | Серверный архив: позволяет клиентам архивировать почту.|
| msExchBlockedSendersHash| X| | | Фильтрация: записывает обратно данные локальной фильтрации и сетевые данные о безопасных и заблокированных отправителях от клиентов.|
| msExchSafeRecipientsHash| X| | | Фильтрация: записывает обратно данные локальной фильтрации и сетевые данные о безопасных и заблокированных отправителях от клиентов.|
| msExchSafeSendersHash| X| | | Фильтрация: записывает обратно данные локальной фильтрации и сетевые данные о безопасных и заблокированных отправителях от клиентов.|
| msExchUCVoiceMailSettings| X| | | Включение единой системы обмена сообщениями (сетевая голосовая почта): используется при интеграции сервера Microsoft Lync Server, указывая локальному серверу Lync Server, что у пользователя в онлайн-службах есть голосовая почта.|
| msExchUserHoldPolicies| X| | | Хранение для судебного разбирательства: позволяет облачным службам определять, на каких пользователей действует хранение для судебного разбирательства.|
| proxyAddresses| X| X| X| Вставляется только адрес x500 из Exchange Online.|

## Примечания об атрибутах
- Если используется альтернативный идентификатор, локальный атрибут userPrincipalName будет синхронизирован с атрибутом Azure AD onPremisesUserPrincipalName. Атрибут «Альтернативный идентификатор», например почта, будет синхронизирован с атрибутом Azure AD userPrincipalName.


## Дополнительные ресурсы

* [Службы синхронизации Azure AD Connect: настройка параметров синхронизации](active-directory-aadconnectsync-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=September15_HO1-->