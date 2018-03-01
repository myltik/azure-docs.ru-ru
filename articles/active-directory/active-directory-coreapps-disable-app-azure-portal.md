---
title: "Отключение входа пользователя в корпоративное приложение в Azure Active Directory | Документы Майкрософт"
description: "Узнайте, как в Azure Active Directory можно отключить корпоративное приложение, чтобы пользователи не могли войти в него."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: e044d32406236aacaf7fffa2b4b19dadd96a7d5d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Отключение входа пользователя в корпоративное приложение в Azure Active Directory
В Azure Active Directory (Azure AD) вы можете легко отключить корпоративное приложение, чтобы пользователи не могли войти в него. Необходимо иметь соответствующие разрешения для управления корпоративным приложением, а также права глобального администратора для доступа к каталогу.

## <a name="how-do-i-disable-user-sign-ins"></a>Как отключить вход пользователей?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Все службы**, в текстовом поле введите **Azure Active Directory**, а затем нажмите клавишу **ВВОД**.
3. В области **Azure Active Directory** -  ***имя_каталога*** (то есть в области Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.

    ![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. В области **Корпоративные приложения** выберите **All applications** (Все приложения). Отобразится список приложений, которыми можно управлять.
5. В области **Корпоративные приложения — All applications (Все приложения)** выберите приложение.
6. В области ***имя_приложения*** (то есть в области с именем выбранного приложения в заголовке) выберите **Свойства**.

    ![Выбор команды "Все приложения"](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. В области ***имя_приложения*** - **Свойства** для параметра **Включен ли вход для пользователей?** выберите значение **Нет**.
8. Щелкните **Сохранить** .

## <a name="next-steps"></a>Дополнительная информация
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Изменение имени или логотипа корпоративного приложения](active-directory-coreapps-change-app-logo-user-azure-portal.md)
