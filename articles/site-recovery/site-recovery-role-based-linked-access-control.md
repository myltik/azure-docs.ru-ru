---
title: Управление Azure Site Recovery с помощью управления доступом на основе ролей | Документация Майкрософт
description: В этой статье объясняется, как применять управление доступом на основе ролей (RBAC) для управления развертываниями Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: d02ecc3293d0607dd3e19ade3c1d9087b544703b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>Использование управления доступом на основе ролей для управления развертываниями Azure Site Recovery

Контроль доступа на основе ролей (RBAC) Azure обеспечивает точное управление доступом для Azure. Используя RBAC, можно разделить обязанности в команде и предоставлять пользователям только разрешения для выполнения определенных заданий.

Azure Site Recovery предоставляет 3 встроенные роли для контроля операций управления Site Recovery. Дополнительные сведения см. в статье о [встроенных ролях RBAC в Azure](../role-based-access-control/built-in-roles.md).

* [Участник Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor). Эта роль имеет все разрешения, необходимые для управления операциями Azure Site Recovery в хранилище служб восстановления. Пользователь с этой ролью, тем не менее, не может создать или удалить хранилище служб восстановления или назначить права доступа другим пользователям. Эта роль лучше всего подходит для администраторов аварийного восстановления, которые могут включить и аварийное восстановление и управлять им для приложений или всей организации, в зависимости от ситуации.
* [Оператор Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator). Эта роль имеет разрешения на выполнение операций отработки отказа и восстановления размещения и управление ими. Пользователь с этой ролью не может включить или отключить репликацию, создать или удалить хранилище, зарегистрировать новую инфраструктуру или назначить права доступа другим пользователям. Эта роль лучше всего подходит для оператора аварийного восстановления, который может выполнять отработку отказа виртуальных машин или приложений по указанию владельцев приложений и ИТ-администраторов в случае действительной аварии или ее имитации, например отработки аварийного восстановления. После устранения аварии оператор аварийного восстановления может повторно применить защиту и восстановить размещение виртуальных машин.
* [Читатель Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader). Эта роль имеет разрешения на просмотр всех операций управления Site Recovery. Она лучше всего подходит для специалиста по контролю ИТ, который может отслеживать текущее состояние защиты и отправлять запросы в службу поддержки, если это необходимо.

Если вы хотите определить собственные роли для дополнительного управления, см. статью о [создании пользовательских ролей](../role-based-access-control/custom-roles.md).

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

Попробуйте использовать [встроенные роли](../role-based-access-control/built-in-roles.md) "Участник виртуальных машин" и "Участник классических виртуальных машин" для развертывания с помощью модели Resource Manager и классической модели соответственно.

## <a name="next-steps"></a>Дополнительная информация
* [Управление доступом на основе ролей](../role-based-access-control/role-assignments-portal.md). Начало работы с RBAC на портале Azure.
* Сведения об управлении доступом с помощью следующих средств:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [интерфейс командной строки Azure](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Устранение неполадок при управлении доступом на основе ролей](../role-based-access-control/troubleshooting.md). Рекомендации по устранению распространенных проблем.
