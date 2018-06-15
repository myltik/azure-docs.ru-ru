---
title: Создание группы пользователей в Azure Active Directory | Документы Майкрософт
description: Узнайте, как создать группу в Azure Active Directory и добавить в нее пользователей
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0dfd1289770c9e0d564576250ef6e51428657193
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712918"
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Создание группы и добавление в нее пользователей в Azure Active Directory
> [!div class="op_single_selector"]
> * [портал Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

В этой статье объясняется, как создать и заполнить новую группу в Azure Active Directory. Группа используется для выполнения таких задач управления, как одновременное назначение лицензий или разрешений нескольким пользователям или устройствам.

## <a name="how-do-i-create-a-group"></a>Как создать группу?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Все службы**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

   ![Открытие страницы "Управление пользователями"](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. В колонке **Пользователи и группы** выберите **Все группы**.

   ![Открытие колонки группы](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. В колонке **Пользователи и группы — Все группы** щелкните **Добавить**.

   ![Выбор команды "Добавить"](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. В колонке **Группа** добавьте имя и описание группы.
6. Чтобы выбрать участников для добавления в группу, выберите **Назначено** в поле **Тип членства**, а затем выберите **Участники**. Дополнительные сведения о динамическом управлении членством в группе см. в статье [Создание расширенных правил членства в группе с помощью атрибутов в предварительной версии Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

   ![Выбор участников для добавления](./media/active-directory-groups-create-azure-portal/select-members.png)
7. В колонке **Участники** выберите одного или несколько пользователей или устройств для добавления в группу, а затем нажмите кнопку **Выбрать**, расположенную в нижней части колонки, чтобы добавить их в группу. В поле **Пользователь** можно ввести часть имени пользователя или устройства, чтобы отфильтровать по нему список отображенных элементов. Подстановочные знаки в поле не допускаются.
8. Завершив добавление участников в группу, щелкните **Создать** в колонке **Группа**.    

   ![Подтверждение создания группы](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление участниками группы](active-directory-groups-members-azure-portal.md)
* [Управление членством в группе](active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)
