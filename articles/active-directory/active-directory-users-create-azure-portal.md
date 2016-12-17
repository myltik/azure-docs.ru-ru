---
title: "Добавление новых пользователей в предварительную версию Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как добавлять новых пользователей или изменять сведения о них в Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7540b90327cf4c88d4e5937b10fcabb3d5b9c4fc


---
# <a name="add-new-users-to-azure-active-directory-preview"></a>Добавление новых пользователей в предварительную версию Azure Active Directory
> [!div class="op_single_selector"]
> * [Портал Azure](active-directory-users-create-azure-portal.md)
> * [классическом портале Azure](active-directory-create-users.md)
> 
> 

В этой статье объясняется, как добавить новых пользователей организации в предварительной версии Azure Active Direstory (Azure AD). [Что есть в предварительной версии?](active-directory-preview-explainer.md)

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.
   
   ![Открытие страницы "Управление пользователями"](./media/active-directory-users-create-azure-portal/create-users-user-management.png)
3. В колонке **Пользователи и группы** выберите **Все пользователи** и щелкните **Добавить**.
   
   ![Выбор команды "Добавить"](./media/active-directory-users-create-azure-portal/create-users-add-command.png)
4. Введите сведения о пользователе в соответствующих полях, например **Имя** и **Имя пользователя**. Частью имени имя пользователя, представляющей собой доменное имя, должно быть доменное имя по умолчанию, foo.onmicrosoft.com, или проверенное доменное имя, не являющееся федеративным, например contoso.com.
5. Скопируйте или иным способом запишите созданный пароль пользователя, чтобы после завершения этого процесса его можно было предоставить пользователю.
6. При необходимости можно заполнить сведения, открыв колонку **Профиль**, **Группы** или **Роль каталога** для пользователя. Дополнительные сведения о ролях пользователей и администраторов см. в статье [Назначение ролей администратора в Azure Active Directory](active-directory-assign-admin-roles.md).
7. В колонке **Пользователь** щелкните **Создать**.
8. Безопасным способом передайте созданный пароль новому пользователю, чтобы он мог войти в систему.

## <a name="whats-next"></a>Что дальше?
* [Добавление внешнего пользователя](active-directory-users-create-external-azure-portal.md)
* [Сброс пароля пользователя на новом портале Azure](active-directory-users-reset-password-azure-portal.md)
* [Изменение сведений о работе пользователя](active-directory-users-work-info-azure-portal.md)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Удаление пользователя из Azure AD](active-directory-users-delete-user-azure-portal.md)
* [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)




<!--HONumber=Nov16_HO3-->


