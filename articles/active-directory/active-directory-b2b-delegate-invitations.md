---
title: "Делегирование приглашений для службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Свойства пользователя службы совместной работы Azure Active Directory B2B можно настраивать."
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 08d06153ab64021d94cd3fdc8ac13839bb4c6908
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Делегирование приглашений для службы совместной работы Azure Active Directory B2B

Благодаря службе совместной работы Azure Active Directory (Azure AD) B2B больше не нужно быть глобальным администратором, чтобы приглашать пользователей. Вместо этого можно использовать политики и делегировать приглашения пользователям, роли которых дают возможность отправлять приглашения. Вам доступен важный новый способ делегирования приглашений гостевым пользователям — с помощью роли Guest Inviter.

## <a name="guest-inviter-role"></a>Роль Guest Inviter
Можно назначить пользователю роль Guest Inviter для отправки приглашений. Для отправки приглашений не нужно быть участником роли глобального администратора. По умолчанию обычные пользователи могут также вызывать API приглашения, если только глобальный администратор не отключил для них приглашения. Кроме того, пользователь может вызвать API с помощью портала Azure или PowerShell.

Ниже приведен пример, показывающий, как использовать PowerShell, чтобы добавить пользователя в роль Guest Inviter:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Управление пользователями, имеющими разрешение на приглашение

![Настройка пользователей с разрешением на приглашение](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Используя службу совместной работы Azure AD B2B, администратор клиентов может устанавливать следующие политики приглашения:

- отключение приглашений;
- приглашать могут только администраторы и пользователи с ролью Guest Inviter;
- приглашать могут администраторы и участники роли Guest Inviter;
- приглашать могут все пользователи, включая гостей.

По умолчанию для клиентов настраивается вариант 4 (приглашать пользователей B2B могут все пользователи, включая гостей).

## <a name="next-steps"></a>Дополнительная информация

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md)
* [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](active-directory-b2b-add-guest-to-role.md)
* [Динамические группы и служба совместной работы Azure Active Directory B2B](active-directory-b2b-dynamic-groups.md)
* [Примеры кода и команд PowerShell для службы совместной работы Azure Active Directory B2B](active-directory-b2b-code-samples.md)
* [Настройка приложений SaaS для службы совместной работы B2B](active-directory-b2b-configure-saas-apps.md)
* [Основные сведения о токенах пользователей в службе совместной работы Azure Active Directory B2B](active-directory-b2b-user-token.md)
* [Сопоставление утверждений пользователя службы совместной работы B2B в Azure Active Directory](active-directory-b2b-claims-mapping.md)
* [Доступ внешних пользователей к Office 365](active-directory-b2b-o365-external-user.md)
* [Текущие ограничения службы совместной работы Azure Active Directory B2B](active-directory-b2b-current-limitations.md)
