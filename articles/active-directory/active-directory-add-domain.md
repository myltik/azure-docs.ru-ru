<properties
	pageTitle="Использование имен личных доменов для упрощения входа пользователей в систему | Microsoft Azure"
	description="В этом разделе объясняется, как добавить имя домена в Azure Active Directory, и приводится соответствующая информация."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# Использование имен личных доменов для упрощения входа пользователей в систему

Чтобы улучшить и упростить вход в систему и другие действия пользователей в Azure Active Directory, можно использовать имена личных доменов. Например, если организации принадлежит доменное имя contoso.com, пользователи могут входить в систему с помощью знакомых имен пользователей, таких как joe@contoso.com.

Каждый каталог в Azure Active Directory поставляется со встроенным доменным именем в формате contoso.onmicrosoft.com. С его помощью вы можете приступить к работе с Azure или другими службами Майкрософт. [Дополнительная информация о встроенных доменах](#built-in-domain-names-for-azure-active-directory).

## Использование имени личного домена в Azure AD

Если организации принадлежит имя личного домена, известное пользователям, рекомендуется использовать это имя в работе с Azure Active Directory. Чтобы использовать имя личного домена с Azure Active Directory, вам нужно выполнить следующие действия:

-   [добавить и проверить имя личного домена](active-directory-add-domain-add-verify-general.md);

-   [назначить пользователей личному домену](active-directory-add-domain-add-users.md).

## Использование имени личного домена с Office 365 и другими службами

Как и другие ресурсы в Azure AD, имена личных доменов, которые вы добавили и проверили, можно использовать с Office 365, Intune и другими приложениями, использующими службу Azure AD. Например, используя имя личного домена со службой Exchange Online, пользователи могут отправлять сообщения электронной почты со знакомого адреса, такого как joe@contoso.com, и получать сообщения на этот адрес. Чтобы включить поддержку использования личных доменов в этих приложениях, вам нужно добавить дополнительные записи DNS в регистраторе DNS в соответствии с документацией приложения.

-   [Использование личных доменов с Office 365](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=ru-RU&rs=ru-RU&ad=US)

-   [Использование личных доменов с Intune](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## Управление доменными именами в Azure AD

В Azure Active Directory обычно не нужно постоянно управлять доменными именами или администрировать их.

-   [Список доменных имен в Azure Active Directory](active-directory-add-domain-add-users.md).

-   [Что делать, если вы изменили регистратор DNS для имени личного домена](active-directory-add-domain-change-registrar.md).

## Удаление имени личного домена

Имя личного домена можно удалить из Azure AD, если ваша организация не использует его или если это доменное имя необходимо использовать с другой службой Azure AD.

-   [Удаление имени личного домена](#_Deleting_a_custom)

## Встроенные доменные имена для Azure Active Directory

Вы должны различать встроенные домены в Azure Active Directory (Azure AD) и личные домены, которые вы добавляете.

-   Встроенный домен — домен, который поставляется с каталогом Azure AD в формате contoso.onmicrosoft.com

-   Личный домен — доменное имя, которым уже владеет ваша организация, например contoso.com.

## Использование PowerShell или API Graph для управления доменными именами

Большинство задач управления доменными именами в Azure Active Directory можно выполнить с помощью Microsoft PowerShell или программно с помощью API Graph.

-   [Управление доменными именами в Azure AD с помощью PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Управление доменными именами в Azure AD с помощью API Graph (предварительная версия)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## Дальнейшие действия

Если вам нужны дополнительные ресурсы, об использовании доменного имени в Azure Active Directory, прочтите эти статьи.

- [Добавление фирменной символики компании на страницах входа и панели доступа](active-directory-add-company-branding.md)
- [Add and verify a custom domain name in Azure Active Directory](active-directory-add-domain-add-verify-general.md) (Добавление и проверка имени личного домена в Azure Active Directory)
- [Assign users to a custom domain](active-directory-add-domain-add-users.md) (Назначение пользователей личным доменам)
- [What to do if you change the DNS registrar for your custom domain name](active-directory-add-domain-change-registrar.md) (Что делать, если вы изменили регистратор DNS для имени личного домена)
- [Delete a custom domain in Azure Active Directory](active-directory-add-domain-delete-domain.md) (Удаление имени личного домена в Azure Active Directory)

<!---HONumber=AcomDC_0211_2016-->