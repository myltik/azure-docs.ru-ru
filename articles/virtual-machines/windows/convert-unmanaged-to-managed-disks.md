---
title: Управляемые диски Azure. Преобразование виртуальной машины Windows с неуправляемыми дисками для использования управляемых дисков | Документация Майкрософт
description: Переключение виртуальной машины Windows, развернутой в рамках модели Resource Manager, с неуправляемых дисков на управляемые диски с помощью PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: 92168ba5605e119d42ba40ee694cebb3ad116041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
ms.locfileid: "29804221"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Переключение виртуальной машины Windows с неуправляемых дисков на управляемые диски

При наличии виртуальных машин Windows, использующих неуправляемые диски, их можно преобразовать для использования управляемых дисков с помощью службы [Управляемые диски Azure](managed-disks-overview.md). При этом преобразуются диск операционной системы и все подключенные диски данных.

В этой статье показано, как преобразовать виртуальные машины с помощью Azure PowerShell. Если вам необходимо установить или обновить Azure PowerShell, ознакомьтесь со статьей [об установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Перед началом работы


* Просмотрите раздел [Планирование миграции на управляемые диски](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Просмотрите раздел с [часто задаваемыми вопросами о переходе на управляемые диски](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Преобразование одноэкземплярных виртуальных машин
В этом разделе описывается, как преобразовать одноэкземплярные виртуальные машины Azure с неуправляемыми дисками, чтобы они могли использовать Управляемые диски. (Если виртуальные машины находятся в группе доступности, ознакомьтесь со следующим разделом.) 

1. Отмените выделение виртуальной машины с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). В следующем примере освобождается виртуальная машина `myVM`, входящая в группу ресурсов `myResourceGroup`. 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Преобразуйте виртуальную машину для использования управляемых дисков с помощью командлета [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Приведенный ниже процесс преобразовывает виртуальную машину, включая ее диск ОС и все диски данных, а затем запускает виртуальную машину.

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
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

2. Освободите и преобразуйте виртуальные машины в группе доступности. Следующий сценарий отменяет выделение каждой виртуальной машины с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), а затем преобразует ее с помощью командлета [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) и автоматически перезапускает ее в процессе преобразования.

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Устранение неполадок

Если во время преобразования произойдет ошибка или виртуальная машина находится в состоянии сбоя из-за проблем во время предыдущего преобразования, выполните командлет `ConvertTo-AzureRmVMManagedDisk` еще раз. Простой повтор обычно решает проблему.
Перед преобразованием убедитесь, что все расширения виртуальной машины находятся в состоянии "Подготовка выполнена успешно", или преобразование завершится ошибкой с кодом 409.


## <a name="next-steps"></a>Дополнительная информация

[Преобразование управляемых дисков уровня "Стандартный" в диски уровня "Премиум"](convert-disk-storage.md)

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).

