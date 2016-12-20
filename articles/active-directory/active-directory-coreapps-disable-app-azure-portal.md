---
title: "Отключение входа пользователя в корпоративное приложение в предварительной версии Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как в Azure Active Directory можно отключить корпоративное приложение, чтобы пользователи не могли войти в него."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 33d3d50980341e0810b1222ce1199fa8689d95d0


---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Отключение входа пользователя в корпоративное приложение в предварительной версии Azure Active Directory
В предварительной версии Azure Active Directory (Azure AD) вы можете легко отключить корпоративное приложение, чтобы пользователи не могли войти в него. [Что есть в предварительной версии?](active-directory-preview-explainer.md)  Для управления корпоративным приложением необходимы соответствующие разрешения. В текущей предварительной версии необходимы привилегии глобального администратора для каталога.

## <a name="how-do-i-disable-user-sign-ins"></a>Как отключить вход пользователей?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Другие службы**, введите **Azure Active Directory** в текстовое поле, а затем нажмите клавишу **ВВОД**.
3. В колонке **Azure Active Directory - *имя_каталога*** (то есть в колонке Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.
   
    ![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. В колонке **Корпоративные приложения** выберите **Все приложения**. Отобразится список приложений, которыми можно управлять.
5. В колонке **Корпоративные приложения — Все приложения** выберите приложение.
6. В колонке ***имя_приложения*** (то есть в колонке с именем выбранного приложения в заголовке) выберите **Свойства**.
   
    ![Выбор команды "Все приложения"](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. В колонке ***имя_приложения*** ** — Свойства** для параметра **Включен ли вход для пользователей?** выберите значение **Нет**.
8. Щелкните **Сохранить** .

## <a name="next-steps"></a>Дальнейшие действия
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Изменение имени или логотипа корпоративного приложения](active-directory-coreapps-change-app-logo-user-azure-portal.md)




<!--HONumber=Nov16_HO3-->


