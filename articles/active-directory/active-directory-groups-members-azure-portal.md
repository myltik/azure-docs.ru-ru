---
title: "Управление участниками группы в предварительной версии Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как управлять пользователями и устройствами, которые входят в группу в Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d399a97d-fd2a-4b2d-b73d-0975db83f41b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c0c411f6e2f51fa2d55d46ff92153dc8882bb38


---
# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Управление участниками группы в предварительной версии Azure Active Directory
В этой статье объясняется, как управлять участниками группы в предварительной версии Azure Active Directory (Azure AD). [Что есть в предварительной версии?](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>Как можно найти участников и управлять ими?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

   ![Открытие страницы "Управление пользователями"](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. В колонке **Пользователи и группы** выберите **Все группы**.

   ![Открытие колонки группы](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. Выберите группу в колонке **Пользователи и группы — Все группы** .
5. В колонке **Группа — *имя_группы*** выберите **Участники**.

   ![Открытие колонки "Участники"](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Чтобы добавить участников в группу, в колонке **Группа — Участники** щелкните **Добавить участников**.

   ![Команда "Добавить участников"](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. В колонке **Участники** выберите одного или несколько пользователей или устройств для добавления в группу, а затем нажмите кнопку **Выбрать**, расположенную в нижней части колонки, чтобы добавить их в группу. В поле **Пользователь** можно ввести часть имени пользователя или устройства, чтобы отфильтровать по нему список отображенных элементов. Подстановочные знаки в поле не допускаются.
8. Чтобы удалить участника из группы, выберите его в колонке **Группы — Участники** .
9. В колонке ***имя_участника*** выберите команду **Удалить** и подтвердите свой выбор при появлении соответствующего запроса.

   ![Команда "Удалить участников"](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Завершив изменение состава группы, щелкните **Сохранить**.

## <a name="additional-information"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Создание группы и добавление участников](active-directory-groups-create-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление членством в группе](active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)



<!--HONumber=Nov16_HO3-->


