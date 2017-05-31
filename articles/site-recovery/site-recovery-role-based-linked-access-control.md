---
title: "Управление Azure Site Recovery с помощью управления доступом на основе ролей | Документация Майкрософт"
description: "В этой статье объясняется, как применять управление доступом на основе ролей (RBAC) для управления развертываниями Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: manayar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 08a4d0fa673a37c61e57daed66ab6768e0276ca8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017


---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>Использование управления доступом на основе ролей для управления развертываниями Azure Site Recovery

Контроль доступа на основе ролей (RBAC) Azure обеспечивает точное управление доступом для Azure. Используя RBAC, можно разделить обязанности в команде и предоставлять пользователям только разрешения для выполнения определенных заданий.

Azure предоставляет различные встроенные роли для контроля операций управления ресурсами. Дополнительные сведения см. в статье о [встроенных ролях RBAC в Azure](../active-directory/role-based-access-built-in-roles.md).

Если вы хотите определить собственные роли для дополнительного управления, см. статью о [создании пользовательских ролей](../active-directory/role-based-access-control-custom-roles.md).

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Разрешения, необходимые для включения репликации для новых виртуальных машин
Когда новая виртуальная машина реплицируется в Azure с помощью Azure Site Recovery, проверяются уровни доступа соответствующего пользователя, чтобы подтвердить, что пользователь имеет разрешения, необходимые для использования ресурсов Azure, предоставленных Site Recovery.

Чтобы включить репликацию для новой виртуальной машины, пользователь должен иметь следующие разрешения.
* Разрешение на создание виртуальной машины в выбранной группе ресурсов.
* Разрешение на создание виртуальной машины в выбранной виртуальной сети.
* Разрешение на запись в выбранной учетной записи хранения.

Пользователю необходимы следующие разрешения для полной репликации новой виртуальной машины.

> [!IMPORTANT]
>Проследите, чтобы соответствующие разрешения были добавлены для каждой модели развертывания (Resource Manager и классическая), которая используется для развертывания ресурсов.

| **Тип ресурса** | **Модель развертывания** | **Разрешение** |
| --- | --- | --- |
| Среда выполнения приложений | Диспетчер ресурсов | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Классический | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Сеть | Диспетчер ресурсов | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Классический | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Хранилище | Диспетчер ресурсов | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Классический | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Группа ресурсов | Диспетчер ресурсов | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Попробуйте использовать [встроенные роли](../active-directory/role-based-access-built-in-roles.md) "Участник виртуальных машин" и "Участник классических виртуальных машин" для развертывания с помощью модели Resource Manager и классической модели соответственно.

## <a name="next-steps"></a>Дальнейшие действия
* [Управление доступом на основе ролей](../active-directory/role-based-access-control-configure.md). Начало работы с RBAC на портале Azure.
* Сведения об управлении доступом с помощью следующих средств:
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Интерфейс командной строки Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [ИНТЕРФЕЙС REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [Устранение неполадок при управлении доступом на основе ролей](../active-directory/role-based-access-control-troubleshooting.md). Рекомендации по устранению распространенных проблем.

