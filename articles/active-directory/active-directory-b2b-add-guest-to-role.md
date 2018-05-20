---
title: Добавление пользователя службы совместной работы Azure Active Directory B2B в роль | Документация Майкрософт
description: Добавление роли гостевого пользователя в Azure Active Directory
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: d9889a15bd4e09863640e8c8e7ea03220d23bb23
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Предоставление пользователям разрешений от партнерских организаций в клиенте Azure Active Directory

Пользователи службы совместной работы Azure Active Directory (Azure AD) B2B добавляются в каталог как гостевые пользователи, а разрешения гостей в каталоге по умолчанию ограничены. Ваша организация может быть заинтересована в назначении некоторым гостевым пользователям более привилегированных ролей. Чтобы это сделать, вы можете добавлять гостевых пользователей в любые роли, исходя из потребностей своей организации.

## <a name="default-role"></a>Роль по умолчанию

![роль по умолчанию](./media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Роль "Глобальный администратор"

![роль "Глобальный администратор"](./media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Роль "Администратор с ограниченными правами"

![роль "Администратор с ограниченными правами"](./media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Дополнительная информация

- [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md)
