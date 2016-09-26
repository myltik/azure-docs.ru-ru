<properties
	pageTitle="Назначение пользователю ролей администратора в предварительной версии Azure Active Directory | Microsoft Azure"
	description="В данном разделе поясняется, как изменить административную информацию о пользователе в Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>

# Назначение пользователю ролей администратора в предварительной версии Azure Active Directory

В этой статье описывается назначение роли администратора пользователю в предварительной версии Azure Active Directory (Azure AD). [Что есть в предварительной версии?](active-directory-preview-explainer.md) Сведения о добавлении новых пользователей в организации см. в статье [Добавление новых пользователей в Azure Active Directory](active-directory-users-create-azure-portal.md). По умолчанию добавленные пользователи не имеют прав администратора, но вы можете назначать им роли в любое время.

## Назначение роли пользователю

1.  Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.

2.  Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

    ![Открытие страницы "Управление пользователями"](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3.  В колонке **Пользователи и группы** выберите **Все пользователи**.

    ![Открытие колонки "Все пользователи"](./media/active-directory-users-assign-role-azure-portal/create-users-opru-RUers-blade.png)

4. В колонке **Пользователи и группы — Все пользователи** выберите пользователя из списка.

5. В колонке для выбранного пользователя щелкните **Роль каталога**, после чего назначьте пользователя для роли из списка **Роль каталога**. Дополнительные сведения о ролях пользователей и администраторов см. в разделе [Назначение ролей администратора в Azure AD](active-directory-assign-admin-roles.md).

	  ![Назначение пользователя для роли](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

6. Щелкните **Сохранить**.


## Что дальше?

- [Добавление пользователей](active-directory-users-create-azure-portal.md)
- [Сброс пароля пользователя на новом портале Azure](active-directory-users-reset-password-azure-portal.md)
- [Изменение сведений о работе пользователя](active-directory-users-work-info-azure-portal.md)
- [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
- [Удаление пользователя из Azure AD](active-directory-users-delete-user-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->