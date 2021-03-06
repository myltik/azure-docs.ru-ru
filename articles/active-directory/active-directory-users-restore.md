---
title: Восстановление или удаление без возможности восстановления недавно удаленного пользователя в Azure Active Directory | Документация Майкрософт
description: Как восстановить удаленного пользователя, просмотреть подходящих для восстановления пользователей и окончательно удалить пользователя в Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/09/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 8dbb546954f8eee0bf997b3d2f4f92d67c0b7869
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930880"
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Восстановление удаленного пользователя в Azure Active Directory

В этой статье содержатся инструкции по восстановлению или окончательному удалению ранее удаленного пользователя. После удаления пользователя в Azure Active Directory он сохраняется в течение 30 дней с даты удаления. В течение этого времени пользователя и его свойства можно восстановить. 

> [!wARNING]
> После окончательного удаления восстановить пользователя будет невозможно.


## <a name="how-to-restore-a-recently-deleted-user"></a>Как восстановить недавно удаленного пользователя
Если пользователь был удален недавно, всех данные каталога сохраняются. При восстановлении пользователя эти сведения также восстанавливаются.

1. В [центре администрирования Azure AD](https://aad.portal.azure.com) выберите **Пользователи** и **Удаленные пользователи**. 
2. Выберите одного или нескольких недавно удаленных пользователей.
3. Выберите **Восстановить учетную запись пользователя**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Как удалить недавно удаленного пользователя без возможности восстановления

1. В [центре администрирования Azure AD](https://aad.portal.azure.com) выберите **Пользователи** и **Удаленные пользователи**. 
2. Выберите одного или нескольких недавно удаленных пользователей.
3. Выберите **Удалить навсегда**.

## <a name="required-permissions"></a>Необходимые разрешения
Для восстановления пользователя достаточно следующих разрешений.

Роль  | Разрешения 
--------- | ---------
Администратор организации<p>Служба поддержка партнеров уровня 1<p>Служба поддержка партнеров уровня 2<p>Администратор учетных записей | Возможность восстановления удаленных пользователей 
Администратор организации<p>Служба поддержка партнеров уровня 1<p>Служба поддержка партнеров уровня 2<p>Администратор учетных записей | Возможность навсегда удалить пользователей

## <a name="next-steps"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об управлении пользователями в Azure Active Directory.

* [Quickstart: Add new users to Azure Active Directory](add-users-azure-active-directory.md) (Краткое руководство: добавление новых пользователей в Azure Active Directory)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Добавление гостевых пользователей из другого каталога](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)
