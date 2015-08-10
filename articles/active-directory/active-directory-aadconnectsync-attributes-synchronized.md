<properties
	pageTitle="Azure AD Connect Sync: атрибуты, синхронизируемые с Azure Active Directory"
	description="В этой статье перечислены атрибуты, которые синхронизируются с Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync: атрибуты, синхронизируемые с Azure Active Directory

В этой статье перечислены атрибуты, которые синхронизируются при помощи службы Azure AD Connect Sync.<br> Атрибуты сгруппированы по соответствующим приложениям Azure AD.
 




## Office 365 профессиональный плюс.

| Имя атрибута| Пользователь| Комментарий |
| --- | :-: | --- |
| AccountEnabled| X| Производный от userAccountControl|
| cn| X| |
| displayName| X| |
| objectSID| X| |
| pwdLastSet| X| |
| sourceAnchor| X| Настройка атрибута, используемого для пользователей, описана в руководстве по установке.|
| usageLocation| X| msExchUsageLocation в доменных службах Active Directory|
| userPrincipalName| X| |


## Exchange Online

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Производный от userAccountControl|
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
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Производный от groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Настройка атрибута, используемого для пользователей, описана в руководстве по установке.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| TelephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | msExchUsageLocation в доменных службах Active Directory|
| userCertificate| X| X| | |
| userPrincipalName| X| | | |
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |


## SharePoint Online

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Производный от userAccountControl|
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
| objectSID| X| | X| |
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
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| Производный от groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Настройка атрибута, используемого для пользователей, описана в руководстве по установке.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| TelephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | msExchUsageLocation в доменных службах Active Directory|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |

## Lync Online

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Производный от userAccountControl|
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
| objectSID| X| | X| |
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Производный от groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Настройка атрибута, используемого для пользователей, описана в руководстве по установке.|
| st| X| X| | |
| streetAddress| X| X| | |
| TelephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | msExchUsageLocation в доменных службах Active Directory|
| userPrincipalName| X| | | |
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
| AccountEnabled| X| | | Производный от userAccountControl|
| c| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Производный от groupType|
| sourceAnchor| X| X| X| Настройка атрибута, используемого для пользователей, описана в руководстве по установке.|
| usageLocation| X| | | msExchUsageLocation в доменных службах Active Directory|
| userPrincipalName| X| | | |


## Dynamics CRM

| Имя атрибута| Пользователь| Контакт| Группа| Комментарий |
| --- | :-: | :-: | :-: | --- |
| AccountEnabled| X| | | Производный от userAccountControl|
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
| objectSID| X| | X| |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | |
| securityEnabled| | | X| Производный от groupType|
| sn| X| X| | |
| sourceAnchor| X| X| X| Настройка атрибута, используемого для пользователей, описана в руководстве по установке.|
| st| X| X| | |
| streetAddress| X| X| | |
| TelephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | msExchUsageLocation в доменных службах Active Directory|
| userPrincipalName| X| | | |


## Дополнительные ресурсы

* [Azure AD Connect Sync: настройка параметров синхронизации](active-directory-aadconnectsync-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=July15_HO5-->