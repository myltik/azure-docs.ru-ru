---
title: "Управление группами, в которые входит ваша группа, в предварительной версии Azure Active Directory | Документация Майкрософт"
description: "В Azure Active Directory группы могут содержать другие группы.  Вот как можно управлять членством такого типа."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e785c2d0-7724-47d4-a56e-c58280c08a14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1968862a68d86c0f42e86ecb1f23aeebd264076c


---
# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>Управление группами, в которые входит ваша группа, в предварительной версии Azure Active Directory
В предварительной версии Azure Active Directory группы могут содержать другие группы. [Что есть в предварительной версии?](active-directory-preview-explainer.md)  Вот как можно управлять членством такого типа.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Как можно узнать, участником каких групп является моя группа?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

   ![Открытие страницы "Управление пользователями"](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
3. В колонке **Пользователи и группы** выберите **Все группы**.

   ![Открытие колонки группы](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
4. Выберите группу в колонке **Пользователи и группы — Все группы** .
5. В колонке **Группа — *имя_группы*** выберите **Членства в группах**.

   ![Открытие колонки "Участие в группах"](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
6. Чтобы добавить группу в качестве участника в другую группу, в колонке **Группа — Членства в группах** выберите команду **Добавить**.
7. Выберите группу в колонке **Выбор группы** и нажмите кнопку **Выбрать**, расположенную в нижней части колонки. Одним действием группу можно добавить только в одну другую группу. В поле **Пользователь** можно ввести часть имени пользователя или устройства, чтобы отфильтровать по нему список отображенных элементов. Подстановочные знаки в поле не допускаются.

   ![Добавление членства в группе](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Чтобы удалить группу из другой группы, участником которой она является, в колонке **Группа — Принадлежность к группам** выберите удаляемую группу.
9. В колонке ***имя_группы*** выберите команду **Удалить** и подтвердите свой выбор при появлении соответствующего запроса.

   ![Команда "Удаление членства"](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Завершив изменение членства группы в других группах, щелкните **Сохранить**.

## <a name="additional-information"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Создание группы и добавление участников](active-directory-groups-create-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление участниками группы](active-directory-groups-members-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)



<!--HONumber=Dec16_HO4-->


