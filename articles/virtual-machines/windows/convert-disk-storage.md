---
title: "Преобразование хранилища управляемых дисков Azure с уровня \"Стандартный\" до уровня \"Премиум\" и наоборот | Документация Майкрософт"
description: "Преобразование хранилища управляемых дисков Azure с уровня \"Стандартный\" до уровня \"Премиум\" и наоборот с помощью Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 9e5c73ceb0ff7d9c18c9cf7128b69e40b9796874
ms.contentlocale: ru-ru
ms.lasthandoff: 08/10/2017

---

# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот

Для компонента "Управляемые диски" доступны два варианта хранилища: уровень [Премиум](../../storage/storage-premium-storage.md) (на базе SSD) и уровень [Стандартный](../../storage/storage-standard-storage.md) (на базе жестких дисков). Это позволяет с легкостью переключаться между двумя вариантами использования при минимальном времени простоя в зависимости от потребностей производительности. Эта возможность недоступна для неуправляемых дисков. Но вы можете [преобразовать диски в управляемые](convert-unmanaged-to-managed-disks.md), чтобы с легкостью переключаться между двумя вариантами использования.

В этой статье рассматривается преобразование хранилища управляемых дисков с уровня "Стандартный" до уровня "Премиум" и наоборот с помощью Azure PowerShell. Если вам необходимо установить или обновить Azure PowerShell, ознакомьтесь со статьей [об установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Перед началом работы

* Процесс преобразования требует перезагрузки виртуальной машины, поэтому запланируйте перенос хранилища дисков на предварительно установленный период обслуживания. 
* При использовании неуправляемых дисков сначала [преобразуйте их в управляемые диски](convert-unmanaged-to-managed-disks.md), чтобы воспользоваться приведенными в этой статье сведениями о переключении между двумя вариантами хранения. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Преобразование всех управляемых дисков на виртуальной машине с уровня "Стандартный" до уровня "Премиум" и наоборот

В следующем примере показано, как переключить все диски на виртуальной машине с хранилища уровня "Стандартный" на хранилище уровня "Премиум". Чтобы использовать управляемые диски уровня "Премиум", необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище уровня "Премиум". В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Преобразование управляемого диска с уровня "Стандартный" до уровня "Премиум" и наоборот

Для среды разработки и тестирования может потребоваться сочетание дисков уровней "Стандартный" и "Премиум", чтобы сократить затраты. Для этого переведите на уровень "Премиум" только диски, для которых требуется более высокая производительность. В следующем примере показано, как переключить диск на виртуальной машине с хранилища уровня "Стандартный" на хранилище уровня "Премиум" и наоборот. Чтобы использовать управляемые диски уровня "Премиум", необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище уровня "Премиум". В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
$vmResource = Get-AzureRmResource -ResourceId $disk.OwnerId
$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Дальнейшие действия

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).


