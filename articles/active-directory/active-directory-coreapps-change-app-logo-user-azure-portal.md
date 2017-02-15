---
title: "Изменение имени или логотипа корпоративного приложения в предварительной версии Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как изменить имя или логотип настраиваемого корпоративного приложения в Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d01303ce-e6cb-4f3b-a4d6-ec29dfd68146
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 541efc3bdc192d21fd75aa4de9d902adb70b6407


---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory-preview"></a>Изменение имени или логотипа корпоративного приложения в предварительной версии Azure Active Directory
Вы можете легко изменить имя или логотип настраиваемого корпоративного приложения в предварительной версии Azure Active Directory (Azure AD). [Что есть в предварительной версии?](active-directory-preview-explainer.md)  Для внесения этих изменений необходимы соответствующие разрешения. В текущей предварительной версии необходимо быть создателем настраиваемого приложения.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Как изменить имя или логотип корпоративного приложения?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Другие службы**, введите **Azure Active Directory** в текстовое поле, а затем нажмите клавишу **ВВОД**.
3. В колонке **Azure Active Directory - *имя_каталога*** (то есть в колонке Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.

    ![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. В колонке **Корпоративные приложения** выберите **Все приложения**. Отобразится список приложений, которыми можно управлять.
5. В колонке **Корпоративные приложения — Все приложения** выберите приложение.
6. В колонке ***имя_приложения*** (то есть в колонке с именем выбранного приложения в заголовке) выберите **Свойства**.

    ![Выбор команды "Свойства"](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. В колонке ***имя_приложения*** ** — свойства** укажите файл нового логотипа или измените имя приложения.

    ![Изменение свойств для логотипа или имени приложения](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Щелкните **Сохранить** .

## <a name="next-steps"></a>Дальнейшие действия
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Отключение входа пользователя в корпоративное приложение](active-directory-coreapps-disable-app-azure-portal.md)



<!--HONumber=Nov16_HO3-->


