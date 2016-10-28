<properties
	pageTitle="Управление группами, в которые входит ваша группа, в предварительной версии Azure Active Directory | Microsoft Azure"
	description="В Azure Active Directory группы могут содержать другие группы. Вот как можно управлять членством такого типа."
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


# Управление группами, в которые входит ваша группа, в предварительной версии Azure Active Directory

В предварительной версии Azure Active Directory группы могут содержать другие группы. [Что есть в предварительной версии?](active-directory-preview-explainer.md) Вот как можно управлять членством такого типа.

## Как можно узнать, участником каких групп является моя группа?

1.  Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.

2.  Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

  ![Открытие страницы "Управление пользователями"](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  В колонке **Пользователи и группы** выберите **Все группы**.

  ![Открытие колонки группы](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. Выберите группу в колонке **Пользователи и группы — Все группы**.

5. В колонке **Группы — *имя\_группы*** выберите **Принадлежность к группам**.

  ![Открытие колонки "Участие в группах"](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Чтобы добавить группу в качестве участника в другую группу, в колонке **Группа — Принадлежность к группам** щелкните **Добавить**.

7. Выберите группу в колонке **Выбор группы** и нажмите кнопку **Выбрать**, расположенную в нижней части колонки. Одним действием группу можно добавить только в одну другую группу. В поле **Пользователь** можно ввести часть имени пользователя или устройства, чтобы отфильтровать по нему список отображенных элементов. Подстановочные знаки в поле не допускаются.

  ![Добавление членства в группе](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Чтобы удалить группу из другой группы, участником которой она является, в колонке **Группа — Принадлежность к группам** выберите удаляемую группу.

9. В колонке ***имя\_группы*** щелкните **Удалить** и подтвердите свой выбор при появлении соответствующего запроса.

  ![Команда "Удаление членства"](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Завершив изменение членства группы в других группах, щелкните **Сохранить**.


## Дополнительная информация

В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Создание группы и добавление участников](active-directory-groups-create-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление участниками группы](active-directory-groups-members-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->