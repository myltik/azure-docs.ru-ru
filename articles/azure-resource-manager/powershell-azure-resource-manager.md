---
title: Управление решениями Azure с помощью PowerShell | Документация Майкрософт
description: Воспользуйтесь Azure PowerShell и Resource Manager для управления ресурсами.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 13e5836aea0e307cdce5bcdcd5cf3c50969dfbf8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Управление ресурсами с помощью Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Сведения об установке и использовании Azure PowerShell локально см. в [этой статье](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="understand-scope"></a>Общие сведения об области

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

В этой статье вы применяете все параметры управления к группе ресурсов, поэтому вы сможете легко отменить их по завершении работы.

Создайте группу ресурсов.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Сейчас группа ресурсов пуста.

## <a name="role-based-access-control"></a>Контроль доступа на основе ролей

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Назначение роли

В этой статье вы развертываете виртуальную машину и связанную с ней виртуальную сеть. Для управления решениями виртуальной машины существует три роли для конкретных ресурсов, предоставляющие необходимый тип доступа:

* [Участник виртуальной машины](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Участник сети](../role-based-access-control/built-in-roles.md#network-contributor)
* [Участник учетной записи хранения](../role-based-access-control/built-in-roles.md#storage-account-contributor)

Вместо назначения ролей для отдельных пользователей зачастую бывает проще [создать группу Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) для пользователей, которым необходимо выполнять подобные действия. А затем назначить этой группе соответствующую роль. Чтобы упростить работу, создайте группу Azure Active Directory без членов. Вы по-прежнему можете назначить группе роль для области. 

В следующем примере показано создание группы и назначение ей роли участника виртуальной машины для группы ресурсов. Для запуска команды `New-AzureAdGroup` необходимо использовать [Azure Cloud Shell](/azure/cloud-shell/overview) или [загрузить модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Как правило, нужно повторить эту процедуру для **участника сети** и **участника учетной записи хранения**, чтобы назначить пользователей для управления развернутыми ресурсами. В текущей статье эти действия можно пропустить.

## <a name="azure-policies"></a>Политики Azure

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Применение политик

Ваша подписка уже имеет несколько определений политики. Чтобы просмотреть список доступных определений политики, используйте следующую команду:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Отобразится список имеющихся определений политики. Политика может относиться к типу **Встроенная** или **Настраиваемая**. Просмотрите определения, которые описывают необходимое вам условие. В этой статье вы назначаете политики, которые:

* ограничивают расположения для всех ресурсов;
* ограничивают номера SKU для виртуальных машин;
* выполняют аудит виртуальных машин, не использующих управляемые диски.

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Развертывание виртуальной машины

Роли и политики назначены — теперь все готово для развертывания вашего решения. Размер по умолчанию — Standard_DS1_v2. Он соответствует одному из ваших разрешенных номеров SKU. При выполнении этого шага будут запрошены учетные данные. В качестве вводимых значений указываются имя пользователя и пароль для виртуальной машины.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

После завершения развертывания к решению можно применить дополнительные параметры управления.

## <a name="lock-resources"></a>Блокировка ресурсов

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Блокировка ресурса

Чтобы заблокировать виртуальную машину и группу безопасности сети, используйте следующую команду:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Виртуальную машину можно удалить только в случае, когда вы намеренно снимаете блокировку. Этот шаг описан в разделе [Очистка ресурсов](#clean-up-resources).

## <a name="tag-resources"></a>Добавление тегов к ресурсам

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Добавление тегов к ресурсам

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Чтобы применить теги к виртуальной машине, используйте следующую команду:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Поиск ресурсов по тегу

Чтобы найти ресурсы по имени и значению тега, используйте следующую команду:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Возвращаемые значения можно использовать для выполнения задач управления, например, остановки всех виртуальных машин с определенным значением тега.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Просмотр данных о затратах по значениям тега

После применения тегов к ресурсам можно просмотреть затраты на ресурсы с этими тегами. Однако сведения о последнем использовании отобразятся в анализе затрат только через некоторое время. Если сведения о затратах на ресурсы доступны, их можно просмотреть в группах ресурсов в подписке. Чтобы пользователи увидели сведения о затратах, им необходимы права [доступа на уровне подписки к сведениям о выставлении счетов](../billing/billing-manage-access.md).

Чтобы просмотреть сведения о затратах по тегам на портале, выберите подписку и щелкните **Анализ затрат**.

![Анализ затрат](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Затем отфильтруйте результаты по значению тега и нажмите кнопку **Применить**.

![Просмотр данных о затратах по тегу](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Кроме того, можно использовать [API-интерфейсы выставления счетов Azure](../billing/billing-usage-rate-card-overview.md), чтобы получить сведения о затратах программным образом.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалить заблокированные группы безопасности сети невозможно до тех пор, пока не будет снята блокировка. Для снятия блокировки используйте следующие команды:

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о мониторинге виртуальных машин см. в статье [Мониторинг и обновление виртуальных машин Windows с помощью Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Дополнительные сведения об использовании центра безопасности Azure для применения рекомендаций по безопасности см. в статье [Контроль безопасности виртуальных машин с помощью центра безопасности Azure](../virtual-machines/windows/tutorial-azure-security.md).
* Существующие ресурсы можно переместить в новую группу ресурсов. Примеры см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](resource-manager-subscription-governance.md).
