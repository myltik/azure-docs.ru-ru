<properties
	pageTitle="Управление участниками группы в предварительной версии Azure Active Directory | Microsoft Azure"
	description="Узнайте, как управлять пользователями и устройствами, которые входят в группу в Azure Active Directory."
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


# Управление участниками группы в предварительной версии Azure Active Directory

В этой статье объясняется, как управлять участниками группы в предварительной версии Azure Active Directory (Azure AD). [Что есть в предварительной версии?](active-directory-preview-explainer.md)

## Как можно найти участников и управлять ими?

1.  Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.

2.  Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

  ![Открытие страницы "Управление пользователями"](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  В колонке **Пользователи и группы** выберите **Все группы**.

  ![Открытие колонки группы](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. Выберите группу в колонке **Пользователи и группы — Все группы**.

5. В колонке **Группы — *имя\_группы*** выберите **Участники**.

  ![Открытие колонки "Участники"](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Чтобы добавить участников в группу, в колонке **Группы — Участники** щелкните **Добавить участников**.

  ![Команда "Добавить участников"](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. В колонке **Участники** выберите одного или несколько пользователей или устройств для добавления в группу, затем нажмите кнопку **Выбрать**, расположенную в нижней части колонки, чтобы добавить их в группу. В поле **Пользователь** можно ввести часть имени пользователя или устройства, чтобы отфильтровать по нему список отображенных элементов. Подстановочные знаки в поле не допускаются.

8. Чтобы удалить участника из группы, выберите его в колонке **Группы — Участники**.

9. В колонке ***имя\_участника*** щелкните **Удалить** и подтвердите свой выбор при появлении соответствующего запроса.

  ![Команда "Удалить участников"](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Завершив изменение состава группы, щелкните **Сохранить**.


## Дополнительная информация

В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Создание группы и добавление участников](active-directory-groups-create-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление членством в группе](active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->