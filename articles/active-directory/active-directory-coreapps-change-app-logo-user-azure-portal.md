<properties
	pageTitle="Изменение имени или логотипа корпоративного приложения в предварительной версии Azure Active Directory | Microsoft Azure"
	description="Узнайте, как изменить имя или логотип настраиваемого корпоративного приложения в Azure Active Directory."
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

# Изменение имени или логотипа корпоративного приложения в предварительной версии Azure Active Directory

Вы можете легко изменить имя или логотип настраиваемого корпоративного приложения в предварительной версии Azure Active Directory (Azure AD). [Что есть в предварительной версии?](active-directory-preview-explainer.md) Для внесения этих изменений необходимы соответствующие разрешения. В текущей предварительной версии необходимо быть создателем настраиваемого приложения.

## Как изменить имя или логотип корпоративного приложения?

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.

2. Выберите **Больше служб**, введите **Azure Active Directory** в текстовое поле, а затем нажмите клавишу **ВВОД**.

3. В колонке **Azure Active Directory — *имя\_каталога*** (то есть в колонке Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.

	![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)

4. В колонке **Корпоративные приложения** выберите **Все приложения**. Отобразится список приложений, которыми можно управлять.

5. В колонке **Корпоративные приложения — Все приложения** выберите приложение.

6. В колонке ***имя\_приложения*** (то есть в колонке с именем выбранного приложения в заголовке) выберите **Свойства**.

	![Выбор команды "Свойства"](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)

7. В колонке ***имя\_приложения*** — **Свойства** укажите файл нового логотипа или измените имя приложения.

	![Изменение свойств для логотипа или имени приложения](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)

8. Щелкните **Сохранить**.

## Дальнейшие действия

- [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
- [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)
- [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-user-azure-portal.md)
- [Отключение входа пользователя в корпоративное приложение](active-directory-coreapps-disable-app-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->