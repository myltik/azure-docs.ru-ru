---
title: Приложение "Управление привилегированными пользователями" для ресурсов Azure — аудит ресурсов | Документация Майкрософт
description: Узнайте, как просмотреть сведения обо всех действиях в роли для определенного ресурса.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Управление привилегированными пользователями — роли ресурсов — аудит

Аудит ресурсов дает вам представление о всей активности роли для этого ресурса. Вы можете отфильтровать информацию с помощью предопределенной даты или настраиваемого диапазона.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Аудит ресурсов также обеспечивает быстрый доступ для просмотра сведений о действиях пользователя. В разделе "Тип аудита" выберите "Активировать". Щелкните ("действие"), чтобы просмотреть сведения о действиях пользователя для ресурсов Azure.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Мой аудит

Параметр "Мой аудит" позволяет получить представление действиях в личной роли пользователя. Вы можете отфильтровать информацию с помощью предопределенной даты или настраиваемого диапазона.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Просмотр активации и активность ресурсов Azure

В случае, если вам нужно выяснить, какие действия выполняет конкретный пользователь с разными ресурсами, вы можете просмотреть активность ресурсов Azure, связанную с данным периодом активации (для допустимых пользователей). Начните с выбора пользователя из представления "Участники" или списка участников в определенной роли. Отобразится графическое представление действий пользователя в ресурсах Azure по датам и недавние активации роли за тот же период времени.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

При выборе активации определенной роли будут показаны детали активации роли и соответствующая активность с ресурсами Azure, которая произошла, когда этот пользователь был активен.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

