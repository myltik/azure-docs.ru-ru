---
title: "Управление владельцами групп в Azure Active Directory | Документация Майкрософт"
description: "Управление владельцами групп и использование этих групп для управления доступом к ресурсу."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>Управление владельцами группы
После того как владелец ресурса предоставил доступ к ресурсу группе Azure Active Directory, управление членством в группе осуществляется владельцем группы. Владелец ресурса фактически делегирует владельцу группы разрешение предоставлять пользователям доступ к ресурсу.

## <a name="add-an-owner-to-a-group"></a>Добавление владельца группы

1. В [центре администрирования Azure AD](https://aad.portal.azure.com) выберите **Пользователи и группы**.
2. Выберите **Все группы** и откройте группу, для которой нужно добавить владельцев.
3. Выберите **Владельцы**, а затем щелкните **Добавить владельцев**.
4. На странице **Добавление владельцев** выберите пользователя, которого нужно добавить в качестве владельца этой группы, и щелкните или коснитесь **Выбрать**. 

## <a name="remove-an-owner-from-a-group"></a>Удаление владельца группы

1. В [центре администрирования Azure AD](https://aad.portal.azure.com) выберите **Пользователи и группы**.
2. Выберите **Все группы** и откройте группу, для которой нужно удалить владельцев.
3. Щелкните **Владельцы** и выберите владельца, которого нужно удалить для группы, а затем щелкните или коснитесь **Выбрать**.
4. В открытой области выбранного владельца щелкните **Удалить**.

## <a name="additional-information"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения о группах Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Создание группы и добавление участников](active-directory-groups-create-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление членством в группе](active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)

