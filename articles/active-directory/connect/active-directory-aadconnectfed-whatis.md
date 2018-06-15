---
title: Azure AD Connect и федерация | Документация Майкрософт
description: На этой странице представлена вся документация об операциях служб федерации Active Directory с использованием Azure AD Connect.
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.component: hybrid
ms.author: anandy
ms.openlocfilehash: 6c14b673bd9664aad54ea8002175999470661e34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593859"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect и федерация
Azure Active Directory (Azure AD) Connect позволяет настроить федерацию с локальными службами федерации Active Directory (AD FS) и Azure AD. С помощью федеративного входа ваши пользователи могут входить в службы Azure AD со своими локальными паролями, а в корпоративной сети им не нужно будет вводить пароли повторно. Благодаря возможности федерации с AD FS можно развернуть новую установку AD FS или указать существующую установку в ферме Windows Server 2012 R2.

В этой статье содержатся сведения о функциях службы Azure AD Connect, относящихся к федерации, и приводятся ссылки на все связанные статьи. Ссылки на ресурсы, посвященные Azure AD Connect, приведены в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Статьи об Azure AD Connect и федерации
| Раздел | Содержание и целевая аудитория |
|:--- |:--- |
| **Параметры входа в Azure AD Connect** | |
| [Параметры входа в Azure AD Connect](active-directory-aadconnect-user-signin.md) |Описание разных параметров входа пользователей и их влияние на работу пользователя в среде Azure. |
| **Установка AD FS с помощью Azure AD Connect** | |
| [Предварительные требования](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Предварительные требования для установки AD FS с помощью Azure AD Connect. |
| [Настройка федерации с AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Установка новой фермы AD FS с помощью Azure AD Connect. |
| [Федерация с Azure AD с помощью альтернативного имени пользователя](active-directory-aadconnect-federation-management.md#alternateid) | Настройка федерации с использованием альтернативного имени пользователя.  |
| **Изменение конфигурации AD FS** | |
| [Восстановление доверия](active-directory-aadconnect-federation-management.md#repairthetrust) |Восстановление текущих отношений доверия между локальной службой AD FS и Office 365 или Azure. |
| [Добавление сервера AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Добавление к ферме AD FS дополнительных серверов AD FS после начальной установки. |
| [Добавление прокси-сервера веб-приложения AD FS](active-directory-aadconnect-federation-management.md#addwapserver) |Добавление к ферме AD FS дополнительного сервера прокси-службы веб-приложения (WAP) после начальной установки. |
| [Добавление нового федеративного домена](active-directory-aadconnect-federation-management.md#addfeddomain) |Добавление нового домена в федерацию с Azure AD. |
| [Обновление SSL-сертификата](active-directory-aadconnectfed-ssl-update.md)| Обновление SSL-сертификата для фермы AD FS. |
| [Обновление сертификатов федерации для Office 365 и Azure AD](active-directory-aadconnect-o365-certs.md)|Обновление сертификата Office 365 в Azure AD.|
| **Другая конфигурация федерации** | |
| [Федерация нескольких экземпляров Azure AD с одним экземпляром AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Федерация нескольких экземпляров Azure AD с одной фермой AD FS.| 
| [Добавление настраиваемого логотипа компании или иллюстрации](active-directory-aadconnect-federation-management.md#customlogo) |Персонализация входа в систему путем настройки логотипа, который будет отображаться на странице входа в AD FS. |
| [Добавление описания входа в систему](active-directory-aadconnect-federation-management.md#addsignindescription) |Изменение текста для описания входа на странице входа в AD FS. |
| [Изменение правил утверждений служб федерации Active Directory](active-directory-aadconnect-federation-management.md#modclaims) |Изменение или добавление правил утверждений в AD FS в соответствии с настройками синхронизации Azure AD Connect. |


## <a name="additional-resources"></a>Дополнительные ресурсы
* [Федерация нескольких экземпляров Azure AD с одним экземпляром AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Развертывание AD FS в Azure](active-directory-aadconnect-azure-adfs.md)
* [Развертывание AD FS высокого уровня доступности в нескольких регионах Azure с помощью диспетчера трафика Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
