---
title: "Azure AD Connect и федерация | Документация Майкрософт"
description: "На этой странице представлена вся документация об операциях службы федерации Active Directory с использованием Azure AD Connect"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: fd3699e4ce423ae9490d5bf788729c43eaa623eb
ms.openlocfilehash: 19ebfae5de8576402348e3163f99a54b9308a078
ms.lasthandoff: 01/13/2017


---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect и федерация
Azure AD Connect позволяет настроить федерацию между локальной AD FS и Azure AD. С помощью федеративного входа ваши пользователи могут входить в службы Azure AD со своими локальными паролями, а в корпоративной сети им не нужно будет вводить пароли повторно. Федерация AD FS позволяет развернуть новую или указать существующую службу AD FS в ферме Windows Server 2012 R2.

В этой статье содержатся сведения о функциях службы Azure AD Connect, относящихся к федерации, и приводятся ссылки на связанные статьи. Ссылки на ресурсы, посвященные Azure AD Connect, см. в статье "Интеграция локальных удостоверений с Azure Active Directory".

## <a name="azure-ad-connect---federation-topics"></a>Статьи об Azure AD Connect и федерации
| Раздел | Содержание и целевая аудитория |
|:--- |:--- |
| **Параметры входа в Azure AD Connect** | |
| [Understanding User sign-in options](active-directory-aadconnect-user-signin.md) |Описание разных параметров входа пользователей и их влияние на работу пользователя в среде Azure. |
| **Установка AD FS с помощью Azure AD Connect** | |
| [Предварительные требования](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Предварительные требования для установки службы федерации Active Directory с помощью Azure AD Connect. |
| [Configure AD FS farm](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Установка новой фермы AD FS с помощью Azure AD Connect. |
| **Изменение конфигурации AD FS** | |
| [Восстановление доверия](active-directory-aadconnect-federation-management.md#repairthetrust) |Восстановление текущего доверия между локальной AD FS и Office 365 или Azure. |
| [Добавление нового сервера AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Добавление к ферме AD FS дополнительных серверов AD FS после начальной установки. |
| [Добавление нового WAP-сервера AD FS](active-directory-aadconnect-federation-management.md#addwapserver) |Добавление к ферме AD FS дополнительных серверов WAP после начальной установки. |
| [Добавление нового федеративного домена](active-directory-aadconnect-federation-management.md#addfeddomain) |Добавление нового домена в федерацию с Azure AD. |
| **Действия после установки** | |
| [Добавление настраиваемого логотипа компании или иллюстрации](active-directory-aadconnect-federation-management.md#customlogo) |Персонализация входа в систему путем настройки эмблемы, которая будет отображаться на странице входа в AD FS. |
| [Добавление описания входа в систему](active-directory-aadconnect-federation-management.md#addsignindescription) |Изменение текста для описания входа на странице входа в AD FS. |
| [Изменение правил утверждений служб федерации Active Directory](active-directory-aadconnect-federation-management.md#modclaims) |Изменение или добавление правил утверждений в AD FS в соответствии с настройками синхронизации Azure AD Connect. |

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)
* [Развертывание AD FS в Azure](active-directory-aadconnect-azure-adfs.md)
* [Развертывание AD FS высокого уровня доступности в нескольких регионах Azure с помощью диспетчера трафика Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

