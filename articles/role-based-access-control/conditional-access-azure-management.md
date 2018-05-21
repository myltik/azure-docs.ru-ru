---
title: Управление доступом к функциям управления Azure с помощью условного доступа в Azure Active Directory
description: Сведения об использовании условного доступа в Azure AD для управления доступом к функциям управления Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: dcf701c15ee20b7d9aeea65f3d06b794006a5202
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Управление доступом к функциям управления Azure с помощью условного доступа

Компонент условного доступа в Azure Active Directory (Azure AD) управляет доступом к облачным приложениям на основе заданных вами условий. Чтобы разрешить доступ, создайте политики условного доступа, разрешающие или запрещающие доступ в зависимости от того, соблюдены ли требования политики. 

Обычно условный доступ используется для управления доступом к облачным приложениям. Вы также можете настроить политики для управления доступом к функциям управления Azure на основе определенных условий (таких как риск при входе, расположение или устройство) и для обеспечения соблюдения требований, таких как многофакторная проверка подлинности.

Чтобы создать политику для управления Azure, выберите **Microsoft Azure Management** (Управление Microsoft Azure) в области **Облачные приложения** при выборе приложения, к которому применяется политика.

![Условный доступ для управления Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Созданная вами политика применяется ко всем конечным точкам управления Azure, включая классический портал Azure, портал Azure, поставщик Azure Resource Manager, классические API управления службами и Azure PowerShell.

> [!CAUTION]
> Перед настройкой политики для управления доступом к функциям управления Azure тщательно изучите принципы работы условного доступа. Убедитесь в отсутствии условий, которые могут заблокировать вам доступ к порталу.

Дополнительные сведения о настройке и использовании условного доступа см. в статье [Условный доступ в Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).