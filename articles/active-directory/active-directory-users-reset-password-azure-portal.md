---
title: "Сброс паролей в Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как выполнить запускаемый администратором сброс пароля пользователя в Azure Active Directory."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: bea082081e3f3f52ba78188903a9536fe9de9392
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Сброс пароля пользователя в Azure Active Directory

Администраторам может потребоваться сбросить пароль пользователя, если он его забыл или заблокировал, а также при других сценариях. Ниже приведены инструкции по сбросу пароля пользователя.

## <a name="how-to-reset-the-password-for-a-user"></a>Сброс пароля для пользователя

1. Войдите в [центр администрирования Azure AD](https://aad.portal.azure.com) с помощью учетной записи, имеющей разрешения каталога на сброс паролей пользователей.
2. Последовательно выберите **Azure Active Directory** > **Пользователи и группы** > **Все пользователи**.
3. Выберите пользователя, для которого необходимо сбросить пароль.
2. Для выбранного пользователя щелкните **Сброс пароля**.

    ![Сброс пароля для пользователя из профиля пользователя в Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. В разделе **Сброс пароля** щелкните **Сбросить пароль**.
7. Отобразится временный пароль, который можно предоставить пользователю. Пользователю будет предложено сменить свой пароль при следующем входе в систему. 

   > [!NOTE]
   > Этот временный пароль не имеет срока действия. Он будет действовать до того момента, когда пользователь снова войдет в систему и выполнит принудительную смену пароля. 

## <a name="next-steps"></a>Дальнейшие действия
* [Добавление пользователей](active-directory-users-create-azure-portal.md)
* [Назначение пользователю ролей администратора в Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Краткое руководство по добавлению новых пользователей в Azure Active Directory](active-directory-users-delete-user-azure-portal.md)
