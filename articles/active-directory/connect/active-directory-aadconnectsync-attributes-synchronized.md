---
title: Атрибуты, синхронизируемые службой Azure AD Connect | Документация Майкрософт
description: В этой статье перечислены атрибуты, которые синхронизируются с Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: billmath
ms.openlocfilehash: 506206d3e0a28f2ffc0e9e029ec27546bc332754
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32160061"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Службы синхронизации Azure AD Connect: атрибуты, синхронизируемые с Azure Active Directory
В этой статье перечислены атрибуты, которые синхронизируются при помощи служб синхронизации Azure AD Connect.  
Атрибуты сгруппированы по соответствующим приложениям Azure AD.

## <a name="attributes-to-synchronize"></a>Атрибуты для синхронизации
Распространенный вопрос состоит в том, *какой минимальный перечень атрибутов следует синхронизировать*. Общепринятый и рекомендуемый подход — оставить атрибуты по умолчанию, чтобы в облаке можно было построить полный глобальный список адресов (GAL) и получить все возможности для рабочих нагрузок Office 365. В некоторых случаях могут быть определенные атрибуты, которые ваша организация не будет синхронизировать с облаком, так как они содержат конфиденциальные или личные сведения, как в приведенном ниже примере:   
![Некорректные атрибуты](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

В этом случае начните со списка атрибутов, приведенного в этом разделе, и определите те из них, которые будут содержать конфиденциальные или личные сведения и которые нельзя синхронизировать. Отмените их выбор во время установки с помощью [фильтра приложений и атрибутов Azure AD](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> При отмене выбора атрибутов необходимо соблюдать осторожность, чтобы снять флажки только тех из них, которые абсолютно невозможно синхронизировать. Отмена выбора других атрибутов может отрицательно повлиять на функции.
>
>

## <a name="office-365-proplus"></a>Office 365 профессиональный плюс.
| Имя атрибута | Пользователь | Комментарий |
| --- |:---:| --- |
| AccountEnabled |X |Определяет, включена ли учетная запись. |
| cn |X | |
| displayName |X | |
| objectSID |X |Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD. |
| pwdLastSet |X |Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется для синхронизации хэша паролей, сквозной аутентификации и федерации. |
| sourceAnchor |X |Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD. |
| usageLocation |X |Механическое свойство. Страна пользователя. Используется для назначения лицензии. |
| userPrincipalName |X |Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail]. |

## <a name="exchange-online"></a>Exchange Online
| Имя атрибута | Пользователь | Контакт | Группа | Комментарий |
| --- |:---:|:---:|:---:| --- |
| AccountEnabled |X | | |Определяет, включена ли учетная запись. |
| assistant |X |X | | |
| altRecipient |X | | |Требуется Azure AD Connect сборки 1.1.552.0 или более поздних версий. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| info |X |X |X |Сейчас этот атрибут не используется для групп. |
| Initials |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Доступно в версии в Azure AD Connect 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Сейчас этот атрибут не используется Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Сейчас этот атрибут не используется Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Сейчас этот атрибут не используется Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Сейчас этот атрибут не используется Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Сейчас этот атрибут не используется Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется как в синхронизации паролей, так и в федерации. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Производный от groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| TelephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |Механическое свойство. Страна пользователя. Используется для назначения лицензии. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Имя атрибута | Пользователь | Контакт | Группа | Комментарий |
| --- |:---:|:---:|:---:| --- |
| AccountEnabled |X | | |Определяет, включена ли учетная запись. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homePhone |X |X | | |
| info |X |X |X | |
| Initials |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| middleName |X |X | | |
| mobile |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Сейчас этот атрибут не используется SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется для синхронизации хэша паролей, сквозной аутентификации и федерации. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Производный от groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| TelephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| unauthOrig |X |X |X | |
| URL-адрес |X |X | | |
| usageLocation |X | | |Механическое свойство. Страна пользователя. Используется для назначения лицензии. |
| userPrincipalName |X | | |Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail]. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online (впоследствии переименован в Skype для бизнеса)
| Имя атрибута | Пользователь | Контакт | Группа | Комментарий |
| --- |:---:|:---:|:---:| --- |
| AccountEnabled |X | | |Определяет, включена ли учетная запись. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется для синхронизации хэша паролей, сквозной аутентификации и федерации. |
| securityEnabled | | |X |Производный от groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| TelephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| usageLocation |X | | |Механическое свойство. Страна пользователя. Используется для назначения лицензии. |
| userPrincipalName |X | | |Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Имя атрибута | Пользователь | Контакт | Группа | Комментарий |
| --- |:---:|:---:|:---:| --- |
| AccountEnabled |X | | |Определяет, включена ли учетная запись. |
| cn |X | |X |Общее имя или псевдоним. Чаще всего префикс значения [mail]. |
| displayName |X |X |X |Строка, представляющая имя. Часто отображается как понятное имя (имя и фамилия). |
| mail |X |X |X |Полный адрес электронной почты. |
| member | | |X | |
| objectSID |X | |X |Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD. |
| proxyAddresses |X |X |X |Механическое свойство. Используется в Azure AD. Содержит все дополнительные адреса электронной почты пользователя. |
| pwdLastSet |X | | |Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. |
| securityEnabled | | |X |Производный от groupType. |
| sourceAnchor |X |X |X |Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD. |
| usageLocation |X | | |Механическое свойство. Страна пользователя. Используется для назначения лицензии. |
| userPrincipalName |X | | |Это имя участника-пользователя является именем пользователя для входа. Чаще всего соответствует значению [mail]. |

## <a name="intune"></a>Intune
| Имя атрибута | Пользователь | Контакт | Группа | Комментарий |
| --- |:---:|:---:|:---:| --- |
| AccountEnabled |X | | |Определяет, включена ли учетная запись. |
| c |X |X | | |
| cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| member | | |X | |
| objectSID |X | |X |Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется для синхронизации хэша паролей, сквозной аутентификации и федерации. |
| securityEnabled | | |X |Производный от groupType |
| sourceAnchor |X |X |X |Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD. |
| usageLocation |X | | |Механическое свойство. Страна пользователя. Используется для назначения лицензии. |
| userPrincipalName |X | | |Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Имя атрибута | Пользователь | Контакт | Группа | Комментарий |
| --- |:---:|:---:|:---:| --- |
| AccountEnabled |X | | |Определяет, включена ли учетная запись. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| objectSID |X | |X |Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется для синхронизации хэша паролей, сквозной аутентификации и федерации. |
| securityEnabled | | |X |Производный от groupType |
| sn |X |X | | |
| sourceAnchor |X |X |X |Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| TelephoneNumber |X |X | | |
| title |X |X | | |
| usageLocation |X | | |Механическое свойство. Страна пользователя. Используется для назначения лицензии. |
| userPrincipalName |X | | |Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail]. |

## <a name="3rd-party-applications"></a>Сторонние приложения
Эта группа представляет собой минимальный набор атрибутов, необходимых для универсальной рабочей нагрузки или приложения. Он может использоваться для рабочей нагрузки, не указанной в другом разделе, или приложения стороннего производителя. Явным образом он используется для следующего:

* Yammer (используется только пользователь);
* [гибридные сценарии межорганизационного взаимодействия бизнес-бизнес, обеспечиваемые такими ресурсами, как SharePoint.](http://go.microsoft.com/fwlink/?LinkId=747036)

Ниже приведена группа атрибутов, которые можно использовать, если каталог Azure AD не используется для поддержки Office 365, Dynamics или Intune. Это небольшой набор основных атрибутов.

| Имя атрибута | Пользователь | Контакт | Группа | Комментарий |
| --- |:---:|:---:|:---:| --- |
| AccountEnabled |X | | |Определяет, включена ли учетная запись. |
| cn |X | |X | |
| displayName |X |X |X | |
| givenName |X |X | | |
| mail |X | |X | |
| managedBy | | |X | |
| mailNickname |X |X |X | |
| member | | |X | |
| objectSID |X | | |Механическое свойство. Идентификатор пользователя AD, используемый для обеспечения синхронизации между Azure AD и AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Механическое свойство. Используется, чтобы определить, когда необходимо сделать недействительными уже выданные маркеры. Используется для синхронизации хэша паролей, сквозной аутентификации и федерации. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Механическое свойство. Неизменяемый идентификатор для поддержания связи между доменными службами Active Directory и Azure AD. |
| usageLocation |X | | |Механическое свойство. Страна пользователя. Используется для назначения лицензии. |
| userPrincipalName |X | | |Имя участника-пользователя является именем для входа. Чаще всего соответствует значению [mail]. |

## <a name="windows-10"></a>Windows 10
Компьютер (устройство), присоединенное к домену Windows 10, синхронизирует некоторые атрибуты с Azure AD. Дополнительные сведения о сценариях см. в статье [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Эти атрибуты всегда синхронизируются, и Windows 10 отображается как приложение, выбор которого нельзя отменить. Компьютер, присоединенный к домену Windows 10, определяется заполненным атрибутом userCertificate.

| Имя атрибута | Устройство | Комментарий |
| --- |:---:| --- |
| AccountEnabled |X | |
| deviceTrustType |X |Жестко запрограммированное значение для компьютеров, присоединенных к домену. |
| displayName |X | |
| MS-DS-CreatorSID |X |Другое название registeredOwnerReference. |
| objectGUID |X |Другое название deviceID. |
| objectSID |X |Другое название onPremisesSecurityIdentifier. |
| operatingSystem |X |Другое название deviceOSType. |
| operatingSystemVersion |X |Другое название deviceOSVersion. |
| userCertificate |X | |

Эти атрибуты для **пользователя** представлены в дополнение к другим выбранным приложениям.  

| Имя атрибута | Пользователь | Комментарий |
| --- |:---:| --- |
| domainFQDN |X |Другое название dnsDomainName. Например, contoso.com. |
| domainNetBios |X |Другое название netBiosName. Например, CONTOSO. |

## <a name="exchange-hybrid-writeback"></a>Гибридная обратная запись Exchange
Эти атрибуты записываются из Azure AD в локальную службу Active Directory при активации **гибридного развертывания Exchange**. В зависимости от установленной версии Exchange может синхронизироваться меньшее количество атрибутов.

| Имя атрибута (пользовательский интерфейс Connect) |Имя атрибута (локальная служба AD) | Пользователь | Контакт | Группа | Комментарий |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Производный от cloudAnchor в Azure AD. Это новый атрибут в Exchange 2016 и Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Серверный архив: позволяет клиентам архивировать почту. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Фильтрация: записывает обратно данные локальной фильтрации и сетевые данные о безопасных и заблокированных отправителях от клиентов. |
| msExchSafeRecipientsHash| MS-Exch-SafeRecipientsHash  |X | | |Фильтрация: записывает обратно данные локальной фильтрации и сетевые данные о безопасных и заблокированных отправителях от клиентов. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Фильтрация: записывает обратно данные локальной фильтрации и сетевые данные о безопасных и заблокированных отправителях от клиентов. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Включение единой системы обмена сообщениями (сетевая голосовая почта): используется при интеграции сервера Microsoft Lync Server, указывая локальному серверу Lync Server, что у пользователя в онлайн-службах есть голосовая почта. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Хранение для судебного разбирательства: позволяет облачным службам определять, на каких пользователей действует хранение для судебного разбирательства. |
| proxyAddresses| proxyAddresses |X |X |X |Вставляется только адрес x500 из Exchange Online. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Позволяет в почтовом ящике Exchange Online предоставлять права SendOnBehalfTo пользователям с локальными почтовыми ящиками Exchange. Требуется Azure AD Connect сборки 1.1.552.0 или более поздних версий. |

## <a name="exchange-mail-public-folder"></a>Общедоступная папка почты Exchange
Эти атрибуты синхронизируются из локального экземпляра Active Directory в Azure AD при включении **общедоступных почтовых папок Exchange**.

| Имя атрибута | PublicFolder | Комментарий |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Обратная запись устройств
Объекты устройств создаются в Active Directory. Это могут быть устройства, присоединенные к Azure AD, или компьютеры Windows 10, присоединенные к домену.

| Имя атрибута | Устройство | Комментарий |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| Различающееся имя |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Только со схемой Windows Server 2016 AD |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Заметки
* Если используется альтернативный идентификатор, то локальный атрибут userPrincipalName синхронизируется с атрибутом Azure AD onPremisesUserPrincipalName. Атрибут "Альтернативный идентификатор", например почта, синхронизируется с атрибутом Azure AD userPrincipalName.
* В списках выше тип объекта **User** также относится к типу объекта **iNetOrgPerson**.

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше о настройке [службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).
