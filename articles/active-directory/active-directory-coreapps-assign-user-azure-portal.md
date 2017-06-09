---
title: "Назначение пользователя или группы корпоративному приложению в Azure Active Directory | Документы Майкрософт"
description: "Узнайте, как выбрать корпоративное приложение и назначить для него пользователя или группу в Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: ee784704ada9238b5cd048f99aaa4cb192ec7d57
ms.contentlocale: ru-ru
ms.lasthandoff: 05/05/2017


---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Назначение пользователя или группы корпоративному приложению в Azure Active Directory
Назначить пользователя или группу для корпоративного приложения в Azure Active Directory (Azure AD) не сложно. Необходимо иметь соответствующие разрешения для управления корпоративным приложением, а также права глобального администратора для доступа к каталогу.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Как назначить пользователю доступ к корпоративному приложению?
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Другие службы**, в текстовом поле введите Azure Active Directory, а затем нажмите клавишу **ВВОД**.
3. В колонке **Azure Active Directory — *имя_каталога*** (то есть в колонке Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.

    ![Открытие колонки "Корпоративные приложения"](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. В колонке **Корпоративные приложения** выберите **Все приложения**. Отобразится список приложений, которыми можно управлять.
5. В колонке **Корпоративные приложения — Все приложения** выберите приложение.
6. В колонке ***имя_приложения*** (то есть в колонке с именем выбранного приложения в заголовке) выберите **Пользователи и группы**.

    ![Выбор команды "Все приложения"](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. В колонке ***имя_приложения***  **— User & Group Assignment** (Назначение пользователей и групп) щелкните **Добавить**.
8. В колонке **Добавление назначения** щелкните **Пользователи и группы**.

    ![Назначение приложению пользователя или группы](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. В колонке **Пользователи и группы** выберите одного или несколько пользователей или групп из списка, а затем нажмите кнопку **Выбрать** в нижней части колонки.
10. В колонке **Добавление назначения** щелкните **Роль**. Затем в колонке **Выбор роли** выберите роль для этих пользователей или групп. Нажмите кнопку **ОК** в нижней части колонки.
11. В колонке **Добавление назначения** нажмите кнопку **Назначить** в нижней части колонки. У назначенных пользователей и групп будут разрешения, определенные выбранной ролью для этого корпоративного приложения.

## <a name="next-steps"></a>Дальнейшие действия
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Отключение входа пользователя в корпоративное приложение](active-directory-coreapps-disable-app-azure-portal.md)
* [Изменение имени или логотипа корпоративного приложения](active-directory-coreapps-change-app-logo-user-azure-portal.md)

