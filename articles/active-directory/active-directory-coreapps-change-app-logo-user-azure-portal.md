---
title: Изменение имени или логотипа корпоративного приложения в Azure Active Directory | Документы Майкрософт
description: Узнайте, как изменить имя или логотип настраиваемого корпоративного приложения в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: a8c166e4c0abeddbdb23622e147cd7ecd5395b05
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Изменение имени или логотипа корпоративного приложения в Azure Active Directory
Вы можете легко изменить имя или логотип настраиваемого корпоративного приложения в Azure Active Directory (Azure AD). Для внесения этих изменений необходимо иметь соответствующие разрешения и быть создателем пользовательского приложения.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Как изменить имя или логотип корпоративного приложения?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Все службы**, в текстовом поле введите **Azure Active Directory**, а затем нажмите клавишу **ВВОД**.
3. В области **Azure Active Directory — *имя_каталога*** (то есть в области Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.

    ![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. В области **Корпоративные приложения** выберите **All applications** (Все приложения). Отобразится список приложений, которыми можно управлять.
5. В области **Корпоративные приложения — All applications (Все приложения)** выберите приложение.
6. В области ***имя_приложения*** (то есть в области с именем выбранного приложения в заголовке) выберите **Свойства**.

    ![Выбор команды "Свойства"](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. В области ***имя_приложения*** **— Свойства** укажите файл нового логотипа или измените имя приложения.

    ![Изменение свойств для логотипа или имени приложения](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Щелкните **Сохранить** .

## <a name="next-steps"></a>Дополнительная информация
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Отключение входа пользователя в корпоративное приложение](active-directory-coreapps-disable-app-azure-portal.md)
