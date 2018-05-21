---
title: Руководство. Управление виртуальными машинами Azure с помощью Azure PowerShell | Документация Майкрософт
description: Из этого руководства вы узнаете, как управлять виртуальными машинами Azure с применением RBAC, политик, блокировок и тегов с помощью Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 154ba47881c65d963729f9074d93c7bb61020389
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-powershell"></a>Руководство. Управление виртуальными машинами Linux с помощью Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Сведения об установке и использовании Azure PowerShell локально см. в [этой статье](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure. Для локальных установок также [скачайте модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/), чтобы создать новую группу Azure Active Directory.

## <a name="understand-scope"></a>Общие сведения об области

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

В этом руководстве к группе ресурсов применяются все параметры управления, поэтому вы сможете легко отменить их по завершении работы.

Создайте группу ресурсов.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Сейчас группа ресурсов пуста.

## <a name="role-based-access-control"></a>Контроль доступа на основе ролей

Вам нужно, чтобы у пользователей вашей организации был необходимый уровень доступа к этим ресурсам. Вы не хотите предоставлять пользователям неограниченный доступ, но при этом требуется обеспечить им возможность работать. [Управление доступом на основе ролей](../../role-based-access-control/overview.md) позволяет предоставлять пользователям разрешения на выполнение определенных действий в той или иной области.

Для создания и удаления назначений ролей пользователи должны иметь доступ `Microsoft.Authorization/roleAssignments/*`. Такой доступ предоставляется с помощью ролей владельца или администратора доступа пользователей.

Для управления решениями виртуальной машины существует три роли для конкретных ресурсов, предоставляющие необходимый тип доступа:

* [Участник виртуальной машины](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Участник сети](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Участник учетной записи хранения](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Вместо назначения ролей для отдельных пользователей зачастую бывает проще [создать группу Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) для пользователей, которым необходимо выполнять подобные действия. А затем назначить этой группе соответствующую роль. Чтобы упростить работу, создайте группу Azure Active Directory без членов. Вы по-прежнему можете назначить группе роль для области. 

В следующем примере создается группа Azure Active Directory с именем *VMDemoContributors* и почтовым псевдонимом *vmDemoGroup*. Почтовый псевдоним служит в качестве псевдонима для группы.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

После возврата командной строки распространение группы по Azure Active Directory занимает некоторое время. Подождав 20 или 30 секунд, воспользуйтесь командой [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment), чтобы назначить новую группу Azure Active Directory роли участника виртуальных машин для группы ресурсов.  Если выполнить следующую команду до ее распространения, будет выдана ошибка с сообщением о том, что **субъект<guid> не существует в каталоге**. Попробуйте выполнить команду снова.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Как правило, нужно повторить эту процедуру для *участника сети* и *участника учетной записи хранения*, чтобы назначить пользователей для управления развернутыми ресурсами. В текущей статье эти действия можно пропустить.

## <a name="azure-policies"></a>Политики Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Применение политик

Ваша подписка уже имеет несколько определений политики. Чтобы просмотреть доступные определения политик, воспользуйтесь командой [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition):

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Отобразится список имеющихся определений политики. Политика может относиться к типу **Встроенная** или **Настраиваемая**. Просмотрите определения, которые описывают необходимое вам условие. В этой статье вы назначаете политики, которые:

* ограничивают расположения для всех ресурсов;
* ограничивают номера SKU для виртуальных машин;
* выполняют аудит виртуальных машин, не использующих управляемые диски.

В приведенном ниже примере вы получите три определения политик на основе отображаемого имени. Назначьте эти определения группе ресурсов с помощью команды [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Для некоторых политик нужно указать допустимые значения, задав значения параметров.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
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

[Блокировки ресурсов](../../azure-resource-manager/resource-group-lock-resources.md) не позволяют пользователям в организации случайно удалить или изменить критически важные ресурсы. В отличие от управления доступом на основе ролей, блокировки ресурсов применяют ограничение для всех пользователей и ролей. Можно установить уровень блокировки *CanNotDelete* или *ReadOnly*.

Чтобы заблокировать виртуальную машину и группу безопасности сети, воспользуйтесь командой [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock):

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Чтобы протестировать блокировки, попробуйте выполнить следующую команду:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Появится ошибка с сообщением о том, что операцию удаления не удалось выполнить из-за блокировки. Группу ресурсов можно удалить, только если намеренно снять блокировки. Этот шаг описан в разделе [Очистка ресурсов](#clean-up-resources).

## <a name="tag-resources"></a>Добавление тегов к ресурсам

К ресурсам Azure можно применять [теги](../../azure-resource-manager/resource-group-using-tags.md), чтобы логически упорядочивать их по категориям. Каждый тег состоит из имени и значения. Например, имя Environment и значение Production можно применить ко всем ресурсам в рабочей среде.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Чтобы применить теги к виртуальной машине, воспользуйтесь командой [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource):

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Поиск ресурсов по тегу

Чтобы найти ресурсы по имени и значению тега, воспользуйтесь командой [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource):

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Возвращаемые значения можно использовать для выполнения задач управления, например, остановки всех виртуальных машин с определенным значением тега.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Просмотр данных о затратах по значениям тега

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалить заблокированные группы безопасности сети невозможно до тех пор, пока не будет снята блокировка. Чтобы снять блокировку, воспользуйтесь командой [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock):

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

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы создали пользовательский образ виртуальной машины. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * назначение пользователей для роли;
> * применение политик, принудительно внедряющих стандарты;
> * защита важных ресурсов с помощью блокировок;
> * добавление к ресурсам тегов для выставления счетов и управления.

Перейдите к следующему руководству, чтобы узнать о высокодоступных виртуальных машинах.

> [!div class="nextstepaction"]
> [Мониторинг виртуальных машин](tutorial-monitoring.md)

