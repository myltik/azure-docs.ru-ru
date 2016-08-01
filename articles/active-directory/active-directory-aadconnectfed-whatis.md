<properties
	pageTitle="Azure AD Connect и федерация | Microsoft Azure"
	description="На этой странице представлена вся документация об операциях службы федерации Active Directory с использованием Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="anandy"/>


# Azure AD Connect и федерация

Azure AD Connect позволяет настроить федерацию между локальной AD FS и Azure AD. С помощью федеративного входа ваши пользователи могут входить в службы Azure AD со своими локальными паролями, а в корпоративной сети им не нужно будет вводить пароли повторно. Федерация AD FS позволяет развернуть новую или указать существующую службу AD FS в ферме Windows Server 2012 R2.

В этом разделе содержится информация о функциях службы Azure AD Connect, относящихся к федерации, и приводятся ссылки на связанные статьи. Ссылки на ресурсы, посвященные Azure AD Connect, см. в статье "Интеграция локальных удостоверений с Azure Active Directory".

## Статьи об Azure AD Connect и федерации

| Раздел | Содержание и целевая аудитория |
|:------|:-----------|
| **Параметры входа в Azure AD Connect** ||
| [Understanding User sign-in options](active-directory-aadconnect-user-signin.md) (Основные сведения о параметрах входа пользователя) | Описание разных параметров входа пользователей и их влияние на работу пользователя в среде Azure. |
| **Установка AD FS с помощью Azure AD Connect**||
| [Предварительные требования](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Предварительные требования для установки службы федерации Active Directory с помощью Azure AD Connect.|
| [Configure AD FS farm](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) (Настройка фермы AD FS) | Установка новой фермы AD FS с помощью Azure AD Connect. |
| **Изменение конфигурации AD FS** | |
| [Восстановление доверия](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Восстановление текущего доверия между локальной AD FS и Office 365 или Azure. |
| [Добавление нового сервера AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Добавление к ферме AD FS дополнительных серверов AD FS после начальной установки. |
| [Добавление нового WAP-сервера AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Добавление к ферме AD FS дополнительных серверов WAP после начальной установки. |
| [Добавление нового федеративного домена](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Добавление нового домена в федерацию с Azure AD. |
|**Действия после установки**||
| [Добавление настраиваемого логотипа компании или иллюстрации](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Персонализация входа в систему путем настройки эмблемы, которая будет отображаться на странице входа в AD FS. |
| [Добавление описания входа в систему](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Изменение текста для описания входа на странице входа в AD FS. | 
| [Изменение правил утверждений служб федерации Active Directory](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Изменение или добавление правил утверждений в AD FS в соответствии с настройками синхронизации Azure AD Connect. |


## Дополнительные ресурсы

* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)
* [Развертывание AD FS в Azure](active-directory-aadconnect-azure-adfs.md)

<!---HONumber=AcomDC_0720_2016-->