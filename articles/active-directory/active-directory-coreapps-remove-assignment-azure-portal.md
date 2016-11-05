---
title: Удаление назначения доступа к корпоративному приложению для пользователя или группы в предварительной версии Azure Active Directory | Microsoft Docs
description: Узнайте, как удалить назначение доступа к корпоративному приложению для пользователя или группы в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Удаление назначения доступа к корпоративному приложению для пользователя или группы в предварительной версии Azure Active Directory
Вы можете легко удалить назначение доступа к корпоративному приложению для пользователя или группы в предварительной версии Azure Active Directory (Azure AD). [Что есть в предварительной версии?](active-directory-preview-explainer.md) Для управления корпоративным приложением необходимы соответствующие разрешения. В текущей предварительной версии необходимы привилегии глобального администратора для каталога.

## Как удалить назначение пользователя или группы?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Azure Active Directory** в текстовое поле, а затем нажмите клавишу **ВВОД**.
3. В колонке **Azure Active Directory — *имя\_каталога*** (то есть в колонке Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.
   
    ![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. В колонке **Корпоративные приложения** выберите **Все приложения**. Отобразится список приложений, которыми можно управлять.
5. В колонке **Корпоративные приложения — Все приложения** выберите приложение.
6. В колонке ***имя\_приложения*** (то есть в колонке с именем выбранного приложения в заголовке) выберите **Пользователи и группы**.
   
    ![Выбор пользователей или групп](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. В колонке ***имя\_приложения*** — **User & Group Assignment** (Назначение групп и пользователей) выберите одного из несколько пользователей или групп, а затем щелкните **Удалить**. При появлении запроса подтвердите свое решение.
   
    ![Выбор команды "Удалить"](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## Дальнейшие действия
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)
* [Отключение входа пользователя в корпоративное приложение](active-directory-coreapps-disable-app-azure-portal.md)
* [Изменение имени или логотипа корпоративного приложения](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->