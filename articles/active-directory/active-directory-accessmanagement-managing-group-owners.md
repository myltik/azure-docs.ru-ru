---
title: Дополнительные возможности управления доступом с помощью групп | Документация Майкрософт
description: Расширенные указания по управлению группами безопасности и использованию этих групп для управления доступом к ресурсу.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: e4703ab5d9f4b8593e758764e50455672e368e18
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="managing-owners-for-a-group"></a>Управление владельцами группы
После того как владелец ресурса предоставил доступ к ресурсу группе Azure Active Directory, управление членством в группе осуществляется владельцем группы. Владелец ресурса фактически делегирует владельцу группы разрешение предоставлять пользователям доступ к ресурсу.

## <a name="add-an-owner-to-a-group"></a>Добавление владельца группы

1. В [центре администрирования Azure AD](https://aad.portal.azure.com) выберите **Пользователи и группы**.
2. Выберите **Все группы** и откройте группу, для которой нужно добавить владельцев.
3. Щелкните **Добавить владельцев**.
4. На странице **Добавление владельцев** выберите пользователя, которого нужно добавить в качестве владельца этой группы, и убедитесь в том, что его имя появилось в области **Выбранные**.

## <a name="remove-an-owner-from-a-group"></a>Удаление владельца группы

1. В [центре администрирования Azure AD](https://aad.portal.azure.com) выберите **Пользователи и группы**.
2. Выберите **Все группы** и откройте группу, для которой нужно удалить владельцев.
3. Откройте вкладку **Владельцы** .
4. Выберите владельца, которого нужно удалить из группы, а затем щелкните **Удалить**.

## <a name="additional-information"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Azure Active Directory cmdlets for configuring group settings](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
* [Что такое Microsoft Azure Active Directory](active-directory-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)
