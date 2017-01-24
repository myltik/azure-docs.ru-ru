---
title: "Перенос учетной записи и ресурсов службы автоматизации | Документация Майкрософт"
description: "В этой статье описывается, как переместить учетную запись в службе автоматизации Azure и связанные с ней ресурсы из одной подписки в другую."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 4fbfb24a2e9d55d718902d468bd25e12f64e7d24
ms.openlocfilehash: 687da15bdaf854254321b59350f47549781676f5


---
# <a name="migrate-automation-account-and-resources"></a>Перенос учетной записи и ресурсов службы автоматизации
Созданные на портале Azure учетные записи службы автоматизации и связанные с ней ресурсы (активы, модули Runbook, модули, и т. д.), можно перенести из одной группы ресурсов или подписки в другую с помощью функции [Перемещение ресурсов](../azure-resource-manager/resource-group-move-resources.md), доступной на портале Azure. Тем не менее перед выполнением этого действия необходимо свериться со следующим [контрольным списком перед перемещением ресурсов](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources), а также приведенным ниже списком, относящимся к службе автоматизации.   

1. Целевая подписка или группа ресурсов должна находиться в том же регионе, что и ресурс.  Это означает, что учетные записи службы автоматизации нельзя перемещать между регионами.
2. При перемещении ресурсов (например, модулей Runbook, заданий и т. д.) исходная и целевая группы блокируются на время этой операции. Операции записи и удаления для групп блокируются до завершения перемещения.  
3. Любые модули Runbook или переменные, ссылающиеся на идентификатор ресурса или подписки из существующей подписки, после завершения переноса потребуется обновить.   

> [!NOTE]
> Данная функция не поддерживает перемещение классических ресурсов службы автоматизации.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Перемещение учетной записи службы автоматизации с помощью портала
1. Из учетной записи службы автоматизации щелкните **Переместить** в верхней части колонки.<br> ![Параметр "Переместить"](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Обратите внимание, что в колонке **Перемещение ресурсов** представлены ресурсы, относящиеся к учетной записи службы автоматизации и группам ресурсов.  В раскрывающихся списках выберите **подписку** и **группу ресурсов** или выберите параметр **Создать группу ресурсов** и введите имя новой группы ресурсов в соответствующем поле.  
3. Просмотрите уведомление и установите флажок *understand tools and scripts will need to be updated to use new resource IDs after resources have been moved* (Я понимаю, что инструменты и сценарии потребуется обновить, чтобы использовать новые идентификаторы ресурсов после их перемещения) для подтверждения этого. Затем нажмите кнопку **ОК**.<br> ![Колонка "Перемещение ресурсов"](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Выполнение этого действия может занять несколько минут.  В разделе **Уведомления** будет показано состояние каждого выполняемого действия — проверки, переноса и т. д., после чего отобразится сообщение о завершении операции.     

## <a name="to-move-the-automation-account-using-powershell"></a>Перемещение учетной записи службы автоматизации с помощью PowerShell
Чтобы переместить существующие ресурсы службы автоматизации в другую группу ресурсов или подписку, используйте командлет **Get-AzureRmResource** для получения определенной учетной записи службы автоматизации, а затем командлет **Move-AzureRmResource** для выполнения переноса.

В первом примере показано, как переместить один учетную запись службы автоматизации в новую группу ресурсов.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

После выполнения приведенного выше примера кода будет предложено подтвердить, что вы хотите выполнить это действие.  После нажатия кнопки **Yes** (Да) выполнение сценария будет продолжено и вы не будете получать какие-либо уведомления, пока выполняется перенос.  

Чтобы переместить ресурс в новую подписку, добавьте значение параметра *DestinationSubscriptionId* .

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Как и в предыдущем примере, вам будет предложено подтвердить перемещение.  

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../azure-resource-manager/resource-group-move-resources.md).
* Дополнительные сведения об управлении доступом на основе ролей в службе автоматизации Azure см. в [этой статье](automation-role-based-access-control.md).
* Сведения о командлетах PowerShell для управления подпиской см. в статье [Использование Azure PowerShell с Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).
* Сведения о функциях портала для управления подпиской см. в статье [Управление ресурсами Azure через портал](../azure-resource-manager/resource-group-portal.md).



<!--HONumber=Dec16_HO3-->


