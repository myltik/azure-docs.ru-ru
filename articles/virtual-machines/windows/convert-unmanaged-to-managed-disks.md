---
title: "Управляемые диски Azure. Преобразование виртуальной машины Windows с неуправляемыми дисками для использования управляемых дисков | Документация Майкрософт"
description: "Переключение виртуальной машины Windows, развернутой в рамках модели Resource Manager, с неуправляемых дисков на управляемые диски с помощью PowerShell."
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
ms.openlocfilehash: 7f26f357268d6a3190557b7099ef07c7ef805119
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Переключение виртуальной машины Windows с неуправляемых дисков на управляемые диски

При наличии виртуальных машин Windows, использующих неуправляемые диски, их можно преобразовать для использования управляемых дисков с помощью службы [Управляемые диски Azure](managed-disks-overview.md). При этом преобразуются диск операционной системы и все подключенные диски данных.

В этой статье показано, как преобразовать виртуальные машины с помощью Azure PowerShell. Если вам необходимо установить или обновить Azure PowerShell, ознакомьтесь со статьей [об установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Перед началом работы


* Просмотрите раздел [Планирование миграции на управляемые диски](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Преобразование одноэкземплярных виртуальных машин
В этом разделе описывается, как преобразовать одноэкземплярные виртуальные машины Azure с неуправляемыми дисками, чтобы они могли использовать Управляемые диски. (Если виртуальные машины находятся в группе доступности, ознакомьтесь со следующим разделом.) 

1. Отмените выделение виртуальной машины с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). В следующем примере освобождается виртуальная машина `myVM`, входящая в группу ресурсов `myResourceGroup`. 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Преобразуйте виртуальную машину для использования управляемых дисков с помощью командлета [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Приведенный ниже процесс преобразовывает виртуальную машину, включая ее диск ОС и все диски данных.

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. После преобразования запустите виртуальную машину с помощью командлета [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). Следующий пример перезапустит предыдущую виртуальную машину:

  ```azurepowershell-interactive
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Преобразование виртуальных машин в группе доступности

Если виртуальные машины, которые вы хотите преобразовать для использования управляемых дисков, входят в группу доступности, то необходимо сначала преобразовать эту группу доступности в управляемую группу доступности.

1. Преобразуйте группу доступности с помощью командлета [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). В следующем примере преобразовывается группа доступности `myAvailabilitySet` в группе ресурсов `myResourceGroup`.

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Если регион, в котором находится группа доступности, имеет только 2 управляемых домена сбоя, но количество неуправляемых доменов сбоя равно 3, отобразится ошибка "Указанное число доменов сбоя 3 должно быть в диапазоне от 1 до 2". Чтобы устранить эту ошибку, необходимо обновить количество доменов сбоя до двух и обновить значение `Sku` к `Aligned` следующим образом:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Освободите и преобразуйте виртуальные машины в группе доступности. Следующий сценарий отменяет выделение каждой виртуальной машины с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), а затем преобразует ее с помощью командлета [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) и перезапускает с помощью командлета [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm).

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Устранение неполадок

Если во время преобразования произойдет ошибка или виртуальная машина находится в состоянии сбоя из-за проблем во время предыдущего преобразования, выполните командлет `ConvertTo-AzureRmVMManagedDisk` еще раз. Простой повтор обычно решает проблему.


## <a name="next-steps"></a>Дальнейшие действия

[Преобразование управляемых дисков уровня "Стандартный" в диски уровня "Премиум"](convert-disk-storage.md)

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).

