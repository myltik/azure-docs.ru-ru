---
title: "Расширение диска данных, подключенного к виртуальной машине Windows в Azure | Документация Майкрософт"
description: "Увеличение размера диска данных, подключенного к виртуальной машине, с помощью PowerShell."
services: virtual-machines-windows
documentationcenter: na
author: cynthn
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 620a28f4fb4421179c0ba030c10acba861760adf
ms.contentlocale: ru-ru
ms.lasthandoff: 04/21/2017


---

# <a name="increase-the-size-of-a-data-disk-attached-to-a-windows-vm"></a>Увеличение размера диска данных, подключенного к виртуальной машине Windows

Если необходимо увеличить размер диска данных, подключенного к виртуальной машине, это можно сделать с помощью PowerShell. После увеличения размера диска данных в параметрах виртуальной машины Azure необходимо также выделить новое дисковое пространство в виртуальной машине.


## <a name="use-powershell-to-increase-the-size-of-a-managed-data-disk"></a>Увеличение размера управляемого диска данных с помощью PowerShell

Чтобы увеличить размер управляемого диска данные, используйте приведенные ниже командлеты PowerShell.

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMReseourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup) | [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm)                 |
| [Stop-AzureRMVM](/powershell/module/azurerm.compute/stop-azurermvm)                        | [Set-AzureRmVMDataDisk](/powershell/module/azurerm.compute/set-azurermvmdatadisk) |
| [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm)                    | [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm)             |
<br>

Следующий сценарий поможет получить сведения о виртуальной машине, выбрать диск данных и указать его новый размер.

```powershell
# Select resource group

    $rg = Get-AzureRMResourceGroup | Out-GridView `
        -Title "Select the resource group" `
        -PassThru

    $rgName = $rg.ResourceGroupName

# Select the VM

    $vm = Get-AzureRMVM -ResourceGroupName $rgName `
        | Out-GridView `
            -Title "Select a VM" `
             -PassThru

# Select data disk

    $disk = $vm.dataDiskNames | Out-GridView `
        -Title "Select a data disk" `
        -PassThru

# Specify a larger size for the data disk

    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk

    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" -DiskSizeInGB $size

# View the new size of the data disk(s)

    $vm.StorageProfile.DataDisks

# Update the configuration in Azure

    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM

    Start-AzureRmVM -ResourceGroupName $rgName -VMName $vm.name

```

## <a name="use-powershell-to-increase-the-size-of-an-unmanaged-data-disk"></a>Увеличение размера неуправляемого диска данных с помощью PowerShell

Чтобы увеличить размер неуправляемых дисков данные в учетной записи хранения, используйте приведенные ниже командлеты PowerShell.

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm)                 |
| [Stop-AzureRMVM](/powershell/module/azurerm.compute/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/module/azurerm.compute/set-azurermvmdatadisk) |
| [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm)                   | [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm)             |

<br>

Следующий сценарий поможет получить сведения о виртуальной машине и учетной записи хранения, выбрать диск данных и указать его новый размер.

```powershell

# Select Azure Storage Account

    $storageAccount =
        Get-AzureRMStorageAccount | Out-GridView `
            -Title "Select Azure Storage Account" `
            -PassThru

    $rgName = $storageAccount.ResourceGroupName

# Select the VM

    $vm = Get-AzureRMVM `
    -ResourceGroupName $rgName | Out-GridView `
            -Title "Select a VM …" `
            -PassThru

# Select Data Disk to resize

    $disk =
        $vm.DataDiskNames | Out-GridView `
            -Title "Select a data disk to resize" `
            -PassThru


# Specify a larger data disk size

    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk

    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" `
        -DiskSizeInGB $size

# Update the configuration in Azure

    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    Start-AzureRmVM -ResourceGroupName $rgName `
    -VMName $vm.name

```

## <a name="allocate-the-unallocated-disk-space"></a>Выделение нераспределенного дискового пространства

Увеличив размер диска, необходимо выделить для него новое нераспределенное дисковое пространство в виртуальной машине. Чтобы выделить это пространство, можно подключиться к виртуальной машине и использовать инструмент "Управление дисками" (diskmgmt.msc). Если при создании виртуальной машины вы установили на нее WinRM и сертификат, то вы можете инициализировать диск удаленно с помощью PowerShell. Можно также использовать расширение пользовательского сценария.

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Файл сценария может содержать код следующего вида, который увеличивает пространство, выделенное для диска, до максимального размера дисков.

```powershell
$driveLetter= "F"

$MaxSize = (Get-PartitionSupportedSize -DriveLetter $driveLetter).sizeMax

Resize-Partition -DriveLetter $driveLetter -Size $MaxSize
```

## <a name="next-steps"></a>Дальнейшие действия
- [Узнайте больше о дисках и виртуальных жестких дисках](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

