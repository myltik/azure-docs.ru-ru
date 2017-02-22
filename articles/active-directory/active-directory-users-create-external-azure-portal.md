---
title: "Добавление пользователей из других каталогов или организаций-партнеров в предварительной версии Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как добавлять пользователей (в том числе внешних и гостевых) или изменять сведения о них в Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e4f7a52c-e782-4bdf-a04e-2174e310785b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 3b31bd036d9c3ff8036b314b93cbddd94874ff63
ms.openlocfilehash: 9ccf5cad400321404981868a7ee9a4b86c396c6c


---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory-preview"></a>Добавление пользователей из других каталогов или организаций-партнеров в предварительной версии Azure Active Directory
> [!div class="op_single_selector"]
> * [Портал Azure](active-directory-users-create-external-azure-portal.md)
> * [классическом портале Azure](active-directory-create-users-external.md)
>
>

В этой статье объясняется, как в предварительной версии Azure Active Directory (Azure AD) добавить пользователей из других каталогов, а также пользователей из организаций-партнеров. [Что есть в предварительной версии?](active-directory-preview-explainer.md) Сведения о добавлении новых корпоративных пользователей, а также пользователей с учетными записями Майкрософт см. в статье [Добавление новых пользователей или пользователей с учетными записями Майкрософт в Azure Active Directory](active-directory-users-create-azure-portal.md). По умолчанию добавленные пользователи не имеют прав администратора, но вы можете назначать им роли в любое время.

## <a name="add-a-user"></a>Добавление пользователей
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

   ![Открытие страницы "Управление пользователями"](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)
3. В колонке **Пользователи и группы** выберите **Пользователи** и щелкните **Добавить**.

   ![Выбор команды "Добавить"](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)
4. В колонке **Пользователь** в поле **Имя** введите отображаемое имя, а в поле **Имя пользователя** — имя для входа пользователя.
5. Скопируйте или иным способом запишите созданный пароль пользователя, чтобы после завершения этого процесса его можно было предоставить пользователю.
6. При необходимости выберите **Профиль** , чтобы добавить имя, фамилию, должность и название отдела пользователя.

    ![Открытие профиля пользователя](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

   * Выберите **Группы** , чтобы добавить пользователя в одну или несколько групп.

       ![Добавление пользователя к группам](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)
   * Выберите **Роль в организации**, чтобы назначить пользователю роль из списка **Роли**. Дополнительные сведения о ролях пользователей и администраторов см. в статье [Назначение ролей администратора в Azure Active Directory](active-directory-assign-admin-roles.md).

       ![Назначение пользователя для роли](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)
7. Нажмите кнопку **Создать**.
8. Безопасным способом передайте созданный пароль новому пользователю, чтобы он мог войти в систему.

> [!IMPORTANT]
> Если в организации используется более одного домена, при добавлении учетной записи пользователя необходимо учитывать следующие моменты:
>
> * Чтобы добавить учетные записи пользователей с тем же именем участника-пользователя (UPN) в разных доменах, **сначала** добавьте, например, geoffgrisso@contoso.onmicrosoft.com,, а **затем** — geoffgrisso@contoso.com.
> * **Не** добавляйте geoffgrisso@contoso.com, пока не добавите geoffgrisso@contoso.onmicrosoft.com. Порядок добавления важен: если вы допустите ошибку сейчас, потом ее может быть сложно исправить.
>
>

Если изменить данные пользователя, удостоверение которого синхронизируется с локальной службой Active Directory, данные такого пользователя невозможно будет изменить на классическом портале Azure. Чтобы изменить сведения о таком пользователе, используйте локальные средства управления Active Directory.

## <a name="next-steps"></a>Дальнейшие действия
* [Добавление пользователей](active-directory-users-create-azure-portal.md)
* [Сброс пароля пользователя на новом портале Azure](active-directory-users-reset-password-azure-portal.md)
* [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Изменение сведений о работе пользователя](active-directory-users-work-info-azure-portal.md)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Удаление пользователя из Azure AD](active-directory-users-delete-user-azure-portal.md)



<!--HONumber=Feb17_HO3-->


