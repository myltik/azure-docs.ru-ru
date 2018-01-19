---
title: "Назначение пользователю ролей администратора в Azure Active Directory | Документы Майкрософт"
description: "В данном разделе поясняется, как изменить административную информацию о пользователе в Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: dcb52e9de98d881474007410f3db599682e151ce
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Назначение пользователю ролей администратора в Azure Active Directory
В этой статье описывается назначение роли администратора пользователю в Azure Active Directory (Azure AD). Сведения о добавлении новых пользователей в организации см. в статье [Добавление пользователей из других каталогов или организаций-партнеров в предварительной версии Azure Active Directory](active-directory-users-create-azure-portal.md). По умолчанию добавленные пользователи не имеют прав администратора, но вы можете назначать им роли в любое время.

## <a name="assign-a-role-to-a-user"></a>Назначение роли пользователю
1. Войдите в [центр администрирования Azure AD](https://aad.portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Пользователи и группы**.

   ![Открытие страницы "Управление пользователями"](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Выберите **Все пользователи**.
  
  ![Открытие группы "Все пользователи"](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Выберите пользователя из списка.
5. Для выбранного пользователя щелкните **Роль каталога**, после чего назначьте пользователя роли из списка **Роль каталога**. Дополнительные сведения о ролях пользователей и администраторов см. в статье [Назначение ролей администратора в Azure AD](active-directory-assign-admin-roles-azure-portal.md).

      ![Назначение пользователя для роли](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Щелкните **Сохранить**.

## <a name="next-steps"></a>Дополнительная информация
* [Краткое руководство по добавлению новых пользователей в Azure Active Directory](add-users-azure-active-directory.md)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Добавление гостевых пользователей из другого каталога](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Восстановление удаленного пользователя](active-directory-users-restore.md)
