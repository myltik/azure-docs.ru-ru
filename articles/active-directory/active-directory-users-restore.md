---
title: "Восстановление удаленного пользователя в Azure Active Directory | Документация Майкрософт"
description: "Как восстановить удаленного пользователя, просмотреть подходящих для восстановления пользователей и окончательно удалить пользователя в Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: c3b7550c2aea0e8bcb7998e0e8c732894b500403
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Восстановление удаленного пользователя в Azure Active Directory

В этой статье содержатся инструкции по восстановлению или окончательному удалению ранее удаленного пользователя. После удаления пользователя в Azure Active Directory он сохраняется в течение 30 дней с даты удаления. В течение этого времени пользователя и его свойства можно восстановить. 

## <a name="required-permissions"></a>Необходимые разрешения
Для восстановления пользователя достаточно следующих разрешений.

Роль  | Разрешения 
--------- | ---------
Администратор организации<p>Служба поддержка партнеров уровня 1<p>Служба поддержка партнеров уровня 2<p>Администратор учетных записей | Возможность восстановления удаленных пользователей 
Администратор организации<p>Служба поддержка партнеров уровня 1<p>Служба поддержка партнеров уровня 2<p>Администратор учетных записей | Возможность навсегда удалить пользователей

## <a name="how-to-restore-a-deleted-user"></a>Восстановление удаленного пользователя

На портале Azure можно восстановить и навсегда удалить удаленного пользователя.

1. В [центре администрирования Azure AD](https://aad.portal.azure.com) выберите **Пользователи и группы** &gt; **Пользователи**. 
2. В разделе **Показать** отфильтруйте страницу, чтобы показать **недавно удаленных пользователей**. 
3. Выберите одного или нескольких недавно удаленных пользователей.
4. Выберите **Восстановление учетной записи пользователя** или **Удалить навсегда**.

## <a name="next-steps"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об управлении пользователями в Azure Active Directory.

* [Quickstart: Add new users to Azure Active Directory](add-users-azure-active-directory.md) (Краткое руководство: добавление новых пользователей в Azure Active Directory)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Добавление гостевых пользователей из другого каталога](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)
