---
title: Отключение входа пользователя в корпоративное приложение в предварительной версии Azure Active Directory | Microsoft Docs
description: Узнайте, как в Azure Active Directory можно отключить корпоративное приложение, чтобы пользователи не могли войти в него.
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
# Отключение входа пользователя в корпоративное приложение в предварительной версии Azure Active Directory
В предварительной версии Azure Active Directory (Azure AD) вы можете легко отключить корпоративное приложение, чтобы пользователи не могли войти в него. [Что есть в предварительной версии?](active-directory-preview-explainer.md) Для управления корпоративным приложением необходимы соответствующие разрешения. В текущей предварительной версии необходимы привилегии глобального администратора для каталога.

## Как отключить вход пользователей?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Azure Active Directory** в текстовое поле, а затем нажмите клавишу **ВВОД**.
3. В колонке **Azure Active Directory — *имя\_каталога*** (то есть в колонке Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.
   
    ![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. В колонке **Корпоративные приложения** выберите **Все приложения**. Отобразится список приложений, которыми можно управлять.
5. В колонке **Корпоративные приложения — Все приложения** выберите приложение.
6. В колонке ***имя\_приложения*** (то есть в колонке с именем выбранного приложения в заголовке) выберите **Свойства**.
   
    ![Выбор команды "Все приложения"](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. В колонке ***имя\_приложения*** — **Свойства** для параметра **Enabled for users to sign-in?** (Вход пользователей разрешен?) выберите значение **Нет**.
8. Щелкните **Сохранить**.

## Дальнейшие действия
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-user-azure-portal.md)
* [Изменение имени или логотипа корпоративного приложения](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->