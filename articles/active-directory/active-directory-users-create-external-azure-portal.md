<properties
	pageTitle="Добавление пользователей из других каталогов или организаций-партнеров в предварительной версии Azure Active Directory | Microsoft Azure"
	description="Узнайте, как добавлять пользователей (в том числе внешних и гостевых) или изменять сведения о них в Azure Active Directory."
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

# Добавление пользователей из других каталогов или организаций-партнеров в предварительной версии Azure Active Directory

> [AZURE.SELECTOR]
- [Портал Azure](active-directory-users-create-external-azure-portal.md)
- [Классический портал Azure](active-directory-create-users-external.md)

В этой статье объясняется, как в предварительной версии Azure Active Directory (Azure AD) добавить пользователей из других каталогов, а также пользователей из организаций-партнеров. [Что есть в предварительной версии?](active-directory-preview-explainer.md) Сведения о добавлении новых корпоративных пользователей, а также пользователей с учетными записями Майкрософт см. в статье [Добавление новых пользователей в Azure Active Directory](active-directory-users-create-azure-portal.md). По умолчанию добавленные пользователи не имеют прав администратора, но вы можете назначать им роли в любое время.

## Добавление пользователей

1.  Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.

2.  Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

    ![Открытие страницы "Управление пользователями"](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  В колонке **Пользователи и группы** выберите **Пользователи** и щелкните **Добавить**.

    ![Выбор команду "Добавить"](./media/ management](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. В колонке **Пользователь** в поле **Имя** введите отображаемое имя, а в поле **Имя пользователя** — имя для входа пользователя.

5. Скопируйте или иным способом запишите созданный пароль пользователя, чтобы после завершения этого процесса его можно было предоставить пользователю.

6. При необходимости выберите **Профиль**, чтобы добавить имя, фамилию, должность и название отдела пользователя.

		![Opening the user profile](./media/ management](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

	- Выберите **Группы**, чтобы добавить пользователя в одну или несколько групп.

		![Добавление пользователя в группы](./media/ management](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

	- Выберите **Роль в организации**, чтобы назначить пользователю роль из списка **Роли**. Дополнительные сведения о ролях пользователей и администраторов см. в разделе [Назначение ролей администратора в Azure AD](active-directory-assign-admin-roles.md).

		![Назначение роли для пользователя](./media/ management](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. Нажмите кнопку **Создать**.

8. Безопасным способом передайте созданный пароль новому пользователю, чтобы он мог войти в систему.

> [AZURE.IMPORTANT] Если в организации используется более одного домена, при добавлении учетной записи пользователя необходимо учитывать следующие моменты:
>
> - Чтобы добавить учетные записи пользователей с тем же именем участника-пользователя (UPN) в разных доменах, **сначала** добавьте, например, geoffgrisso@contoso.onmicrosoft.com, а **затем** — geoffgrisso@contoso.com.
> - **Не** добавляйте geoffgrisso@contoso.com, пока не добавите geoffgrisso@contoso.onmicrosoft.com. Порядок добавления важен: если вы допустите ошибку сейчас, потом ее может быть сложно исправить.

Если изменить данные пользователя, удостоверение которого синхронизируется с локальной службой Active Directory, данные такого пользователя невозможно будет изменить на классическом портале Azure. Чтобы изменить сведения о таком пользователе, используйте локальные средства управления Active Directory.


## Что дальше?

- [Добавление пользователей](active-directory-users-create-azure-portal.md)
- [Сброс пароля пользователя на новом портале Azure](active-directory-users-reset-password-azure-portal.md)
- [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)
- [Изменение сведений о работе пользователя](active-directory-users-work-info-azure-portal.md)
- [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
- [Удаление пользователя из Azure AD](active-directory-users-delete-user-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->