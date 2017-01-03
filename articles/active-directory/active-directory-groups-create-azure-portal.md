---
title: "Создание группы в предварительной версии Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как создать группу в Azure Active Directory и добавить в нее пользователей (участников)."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 21e36b4bb812eb614332a060ffbfd5b7cc76749a


---
# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Создание группы в предварительной версии Azure Active Directory
> [!div class="op_single_selector"]
> * [Портал Azure](active-directory-groups-create-azure-portal.md)
> * [Классический портал Azure](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
> 
> 

В этой статье объясняется, как создать и заполнить новую группу в предварительной версии Azure Active Directory (Azure AD). [Что есть в предварительной версии?](active-directory-preview-explainer.md)  Группа используется для выполнения таких задач управления, как одновременное назначение лицензий или разрешений нескольким пользователям или устройствам.

## <a name="how-do-i-create-a-group"></a>Как создать группу?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.
   
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

## <a name="additional-information"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление участниками группы](active-directory-groups-members-azure-portal.md)
* [Управление членством в группе](active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)




<!--HONumber=Dec16_HO4-->


