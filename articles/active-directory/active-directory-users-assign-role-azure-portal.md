---

title: "Назначение пользователю ролей администратора в Azure Active Directory | Документы Майкрософт"
description: "В данном разделе поясняется, как изменить административную информацию о пользователе в Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: ebbadff39c736ec7364999f7644694e8a177073f
ms.contentlocale: ru-ru
ms.lasthandoff: 05/08/2017


---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Назначение пользователю ролей администратора в Azure Active Directory
В этой статье описывается назначение роли администратора пользователю в Azure Active Directory (Azure AD). Сведения о добавлении новых пользователей в организации см. в статье [Добавление пользователей из других каталогов или организаций-партнеров в предварительной версии Azure Active Directory](active-directory-users-create-azure-portal.md). По умолчанию добавленные пользователи не имеют прав администратора, но вы можете назначать им роли в любое время.

## <a name="assign-a-role-to-a-user"></a>Назначение роли пользователю
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

   ![Открытие страницы "Управление пользователями"](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. В колонке **Пользователи и группы** выберите **Все пользователи**.

   ![Открытие колонки "Все пользователи"](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. В колонке **Пользователи и группы — Все пользователи** выберите пользователя из списка.
5. В колонке для выбранного пользователя щелкните **Роль каталога**, после чего назначьте пользователя для роли из списка **Роль каталога**. Дополнительные сведения о ролях пользователей и администраторов см. в статье [Назначение ролей администратора в Azure Active Directory](active-directory-assign-admin-roles.md).

      ![Назначение пользователя для роли](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Щелкните **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
* [Добавление пользователей](active-directory-users-create-azure-portal.md)
* [Сброс пароля пользователя на новом портале Azure](active-directory-users-reset-password-azure-portal.md)
* [Изменение сведений о работе пользователя](active-directory-users-work-info-azure-portal.md)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Удаление пользователя из Azure AD](active-directory-users-delete-user-azure-portal.md)

