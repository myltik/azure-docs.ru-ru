---
title: "Служба совместной работы Azure Active Directory B2B для гибридных организаций | Документация Майкрософт"
description: "Предоставьте партнерам доступ к локальным и облачным ресурсам с помощью службы совместной работы Azure AD B2B."
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Служба совместной работы Azure Active Directory B2B для гибридных организаций

В гибридной организации, в которой используются локальные и облачные ресурсы, может потребоваться предоставить к ним доступ для внешних партнеров. Для предоставления доступа к локальным ресурсам можно управлять учетными записями партнеров в локальной среде Active Directory. Партнеры могут входить, используя свои партнерские учетные записи участника для доступа к ресурсам организации. Чтобы предоставить партнерам доступ к облачным ресурсам с помощью этих же учетных данных, теперь можно использовать службу Azure Active Directory (Azure AD) Connect для синхронизации партнерских учетных записей с облаком в качестве пользователей Azure AD B2B (то есть пользователей, у которых атрибут UserType имеет значение Guest).

## <a name="identify-unique-attributes-for-usertype"></a>Определение уникальных атрибутов для UserType

Перед включением синхронизации атрибута UserType необходимо решить, каким образом этот атрибут будет получен из локальной службы Active Directory. Другими словами, какие параметры в локальной среде являются уникальными для внешних участников совместной работы? Определите параметр, который позволяет отличить этих участников от членов вашей организации.

Для этого существуют два распространенных подхода.

- Назначьте неиспользуемый локальный атрибут Active Directory (например, extensionAttribute1) в качестве исходного атрибута. 
- Кроме того, можно получить значение атрибута UserType из других свойств. Например, требуется синхронизировать всех пользователей в качестве гостевых, если их локальный атрибут Active Directory UserPrincipalName заканчивается доменном *@partners.fabrikam123.org*.
 
Подробные требования к атрибутам приведены в разделе [Включение синхронизации атрибута UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Настройка Azure AD Connect для синхронизации пользователей с облаком

После определения уникального атрибута можно настроить Azure AD Connect для синхронизации этих пользователей с облаком в качестве пользователей Azure AD B2B (то есть пользователей, у которых атрибут UserType имеет значение Guest). С точки зрения процесса авторизации эти пользователи будут неотличимы от пользователей B2B, созданных с помощью процесса приглашения в службу совместной работы Azure AD B2B.

Инструкции по реализации приведены в разделе [Включение синхронизации атрибута UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения о службе совместной работы Azure AD B2B см. в разделе [Что такое служба совместной работы Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md).
- Общие сведения об Azure AD Connect см. в статье [Интеграция локальных каталогов с Azure Active Directory](connect/active-directory-aadconnect.md).

