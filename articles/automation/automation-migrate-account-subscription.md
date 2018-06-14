---
title: Перенос учетной записи и ресурсов службы автоматизации
description: В этой статье описывается, как переместить учетную запись в службе автоматизации Azure и связанные с ней ресурсы из одной подписки в другую.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 75d973388bb8a37cc0b796b1621f847b047d70f9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194118"
---
# <a name="migrate-automation-account-and-resources"></a>Перенос учетной записи и ресурсов службы автоматизации
Созданные на портале Azure учетные записи службы автоматизации и связанные с ней ресурсы (ресурсы, модули Runbook, модули, и т. д.), можно перенести из одной группы ресурсов или подписки в другую с помощью функции [Перемещение ресурсов](../azure-resource-manager/resource-group-move-resources.md), доступной на портале Azure. Но перед выполнением этого действия необходимо свериться со следующим [контрольным списком перед перемещением ресурсов](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources), а также приведенным ниже списком, относящимся к службе автоматизации.  

1. Целевая подписка или группа ресурсов должна находиться в том же регионе, что и ресурс. Это означает, что учетные записи службы автоматизации нельзя перемещать между регионами.
2. При перемещении ресурсов (например, модулей runbook, заданий и т. д.) исходная и целевая группы блокируются на время этой операции. Операции записи и удаления для групп блокируются до завершения перемещения. 
3. Все модули Runbook или переменные, ссылающиеся на идентификатор ресурса или подписки из существующей подписки, после завершения переноса потребуется обновить.  

> [!NOTE]
> Данная функция не поддерживает перемещение классических ресурсов службы автоматизации.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Перемещение учетной записи службы автоматизации с помощью портала
1. Из учетной записи службы автоматизации щелкните **Переместить** в верхней части страницы.<br> ![Параметр "Переместить"](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Выберите другую группу ресурсов или подписку для перемещения учетной записи автоматизации.
3. Обратите внимание, что на панели **Перемещение ресурсов** представлены ресурсы, относящиеся к учетной записи службы автоматизации и группам ресурсов. В раскрывающихся списках выберите **подписку** и **группу ресурсов** или выберите параметр **Создать группу ресурсов** и введите имя новой группы ресурсов в соответствующем поле. 
4. Просмотрите уведомление и установите флажок *understand tools and scripts will need to be updated to use new resource IDs after resources have been moved* (Я понимаю, что инструменты и сценарии потребуется обновить, чтобы использовать новые идентификаторы ресурсов после их перемещения) для подтверждения этого. Затем нажмите кнопку **ОК**.<br> ![Панель "Перемещение ресурсов"](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Выполнение этого действия может занять несколько минут. В разделе **Уведомления** отображается состояние каждого выполняемого действия — проверки, переноса и т. д. Затем отобразится сообщение о завершении операции.    

## <a name="to-move-the-automation-account-using-powershell"></a>Перемещение учетной записи службы автоматизации с помощью PowerShell
Чтобы переместить существующие ресурсы службы автоматизации в другую группу ресурсов или подписку, используйте командлет **Get-AzureRmResource** для получения определенной учетной записи службы автоматизации, а затем командлет **Move-AzureRmResource** для выполнения переноса.

В первом примере показано, как переместить один учетную запись службы автоматизации в новую группу ресурсов.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

После выполнения приведенного выше примера кода нужно подтвердить, что вы хотите выполнить это действие. После нажатия кнопки **Да** выполнение сценария будет продолжено и вы не будете получать уведомления, пока выполняется перенос. 

Чтобы переместить ресурс в новую подписку, добавьте значение параметра *DestinationSubscriptionId* .

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Как и в предыдущем примере, вам будет предложено подтвердить перемещение. 

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../azure-resource-manager/resource-group-move-resources.md).
* Дополнительные сведения об управлении доступом на основе ролей в службе автоматизации Azure см. в [этой статье](automation-role-based-access-control.md).
* Сведения о командлетах PowerShell для управления подпиской см. в статье [Использование Azure PowerShell с Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).
* Сведения о функциях портала для управления подпиской см. в статье [Управление ресурсами Azure через портал](../azure-resource-manager/resource-group-portal.md).
