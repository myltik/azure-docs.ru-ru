---
title: "Azure Active Directory B2B взаимодействия для гибридных организаций | Документы Microsoft"
description: "Предоставьте партнерам доступ для локальных и облачных ресурсов с совместной работы Azure AD B2B"
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
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B взаимодействия для гибридных организаций

В гибридной организации, где есть оба локальных и облачных ресурсов, может потребоваться предоставьте внешним партнерам доступ для локальных и облачных ресурсов. Для доступа к локальным ресурсам можно управлять учетными записями партнера локально в локальной среде Active Directory. Партнеры вход свои учетные данные учетной записи участника на доступ к ресурсам организации. Чтобы предоставить партнерам доступ к облачным ресурсам через эти же учетные данные, теперь можно использовать подключение Azure Active Directory (Azure AD) для синхронизации учетных записей партнера в облаке как пользователи Azure AD B2B (то есть, пользователи с UserType = Гость).

## <a name="identify-unique-attributes-for-usertype"></a>Определите уникальные атрибуты для UserType

Перед включением синхронизации пользовательского атрибута, сначала необходимо решить, как наследовать атрибут UserType из локальной службы Active Directory. Другими словами какие параметры в локальной среде уникальны для вашего внешними участниками? Определите параметр, который позволяет отличить эти внешними участниками от членов вашей организации.

— Два обычных подхода для этого.

- Назначение атрибутов неиспользуемые локальной Active Directory (например, extensionAttribute1) для использования в качестве исходного атрибута. 
- Кроме того получить значение для атрибута UserType от других свойств. Например, вы хотите синхронизировать всех пользователей как у гостя при окончании срока их в локальной среде Active Directory атрибут UserPrincipalName с доменом  *@partners.fabrikam123.org* .
 
Атрибут подробные требования см. в разделе [включить синхронизацию UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Настройка Azure AD Connect для синхронизации пользователей в облаке

После определения уникальный атрибут, можно настроить Azure AD Connect для синхронизации этих пользователей в облаке, как пользователи Azure AD B2B (то есть, пользователи с UserType = Гость). С авторизации точки зрения эти пользователи будут отличаться от B2B пользователей, созданные с помощью процесса приглашения совместной работы Azure AD B2B.

Реализация инструкции см. в разделе [включить синхронизацию UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о совместной работы Azure AD B2B. в разделе [возможности совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- Обзор Azure AD Connect см. в разделе [интегрировать локальных каталогов с Azure Active Directory](connect/active-directory-aadconnect.md).

