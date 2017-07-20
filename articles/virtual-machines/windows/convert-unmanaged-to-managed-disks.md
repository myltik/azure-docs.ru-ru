---
title: "Переключение виртуальной машины Windows с неуправляемых на Управляемые диски в Azure | Документация Майкрософт"
description: "Переключение виртуальной машины Windows, развернутой в рамках модели Resource Manager, с неуправляемых дисков на Управляемые диски Azure с помощью PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 636d4f7c5da72973a7837718cfb42dda93bba2cc
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-windows-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Как преобразовать виртуальную машину Windows с неуправляемыми дисками для использования Управляемых дисков Azure

При наличии виртуальных машин, использующих неуправляемые диски, их можно преобразовать для использования [Управляемых дисков Azure](../../storage/storage-managed-disks-overview.md). При этом преобразуются диск операционной системы и все подключенные диски данных.

В этой статье показано, как преобразовать виртуальные машины с помощью Azure PowerShell. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Перед началом работы


* Просмотрите раздел [Планирование миграции на управляемые диски](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Преобразование одноэкземплярных виртуальных машин
В этом разделе описывается, как преобразовать одноэкземплярные виртуальные машины Azure с неуправляемыми дисками, чтобы они могли использовать Управляемые диски. (Если виртуальные машины находятся в группе доступности, см. следующий раздел.) 

1. Освободите виртуальную машину с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). В следующем примере освобождается виртуальная машина `myVM`, входящая в группу ресурсов `myResourceGroup`. 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Преобразуйте виртуальную машину для использования Управляемых дисков с помощью командлета [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Приведенный ниже процесс преобразовывает виртуальную машину, включая ее диск ОС и все диски данных.

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. После преобразования запустите виртуальную машину с помощью командлета [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). Следующий пример перезапустит предыдущую виртуальную машину:

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Преобразование виртуальных машин в группе доступности

Если виртуальные машины, которые вы хотите преобразовать для использования управляемых дисков, входят в группу доступности, то необходимо сначала преобразовать эту группу доступности в управляемую группу доступности.

1. Преобразуйте группу доступности с помощью командлета [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). В следующем примере преобразовывается группа доступности `myAvailabilitySet` в группе ресурсов `myResourceGroup`.

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Если регион, в котором находится группа доступности, имеет только 2 управляемых домена сбоя, но количество неуправляемых доменов сбоя равно 3, отобразится ошибка The specified fault domain count 3 must fall in the range 1 to 2 (Указанное число доменов сбоя (3) должно быть в диапазоне от 1 до 2). Чтобы устранить эту ошибку, необходимо обновить количество доменов сбоя до двух и обновить значение `Sku` к `Aligned` следующим образом:

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Освободите и преобразуйте виртуальные машины в группе доступности. Следующий скрипт освобождает каждую виртуальную машину с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), преобразует с помощью [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) и перезапускает с помощью [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm).

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  }
  ```


## <a name="convert-standard-managed-disks-to-premium"></a>Преобразование стандартных управляемых дисков в диски класса Premium
После преобразования виртуальных машин для использования управляемых дисков вы также можете переключиться между типами хранилищ. Вы можете также использовать для хранения дисков произвольное сочетание хранилищ класса Standard и класса Premium. В следующем примере показано, как переключиться между стандартным хранилищем и хранилищем класса Premium. Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также выполняется переключение до размера, поддерживающего хранилище класса Premium.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -rgName $resourceGroupName

# Stop deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change VM size to a size supporting Premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the VM selected, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="troubleshooting"></a>Устранение неполадок

Если во время преобразования возникает ошибка или виртуальная машина находится в состоянии сбоя из-за проблем во время предыдущего преобразования, выполните командлет `ConvertTo-AzureRmVMManagedDisk` еще раз. Простой повтор обычно решает проблему.


## <a name="managed-disks-and-azure-storage-service-encryption"></a>Управляемые диски и шифрование службы хранилища Azure

Невозможно преобразовать неуправляемый диск в управляемый, выполнив предыдущие шаги, если неуправляемый диск размещен в учетной записи хранения, которая зашифрована или когда-либо шифровалась с помощью [шифрования службы хранилища Azure](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ниже приведены инструкции по копированию и использованию неуправляемых дисков, которые находятся в зашифрованной учетной записи хранения.

1. Выполните команду [AzCopy](../../storage/storage-use-azcopy.md), чтобы скопировать виртуальный жесткий диск в учетную запись хранения, для которой никогда не использовалось шифрование службы хранилища Azure.

2. Используйте скопированную виртуальную машину одним из следующих способов.

  * Создайте виртуальную машину, которая использует управляемые диски, и в процессе создания укажите этот VHD-файл с помощью команды `New-AzureRmVm`.

  * Кроме того, с помощью команды `Add-AzureRmVmDataDisk` можно также подключить этот VHD-файл к работающей виртуальной машине, использующей Управляемые диски.


## <a name="next-steps"></a>Дальнейшие действия

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).


