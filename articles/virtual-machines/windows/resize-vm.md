---
title: Использование PowerShell для изменения размера виртуальной машины Windows в Azure | Документация Майкрософт
description: Изменение размера виртуальной машины Windows, созданной в модели развертывания с помощью Resource Manager, с использованием Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: cynthn
ms.openlocfilehash: d2010ee9017416360069c74118b8ae25e71e1da7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="resize-a-windows-vm"></a>Изменение размера виртуальной машины Windows
В этой статье описано, как изменить размер виртуальной машины Windows, созданной в модели развертывания с помощью Resource Manager, с использованием Azure PowerShell.

После того как вы создали виртуальную машину, ее можно масштабировать, изменяя размер. В некоторых случаях сначала необходимо освободить виртуальную машину. Это может случиться, если новый размер недоступен в кластере оборудования, в котором она сейчас размещена.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Изменение размера виртуальной машины Windows, не входящей в группу доступности
1. Выведите список размеров виртуальной машины, доступных в кластере оборудования, в котором она размещена. 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. Если в списке есть нужный размер, выполните следующие команды, чтобы изменить размер виртуальной машины. Если нужный размер отсутствует в списке, перейдите к шагу 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Если нужного размера нет в списке, выполните следующие команды, чтобы освободить виртуальную машину, изменить ее размер и перезагрузить.
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -Name $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> При освобождении виртуальной машины освобождаются все назначенные ей динамические IP-адреса. Это не влияет на диски ОС и данных. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Изменение размера виртуальной машины Windows в группе доступности
Если новый размер виртуальной машины, входящей в группу доступности, недоступен в кластере оборудования, в котором она сейчас размещена, то для изменения размера этой виртуальной машины потребуется освободить все виртуальные машины в данной группе доступности. После изменения размера одной виртуальной машины может также потребоваться обновить размер остальных виртуальных машин в группе доступности. Чтобы изменить размер виртуальной машины в группе доступности, выполните следующие действия.

1. Выведите список размеров виртуальной машины, доступных в кластере оборудования, в котором она размещена.
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. Если в списке есть нужный размер, выполните следующие команды, чтобы изменить размер виртуальной машины. Если его нет в списке, перейдите к шагу 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Если нужный размер отсутствует в списке, выполните приведенные ниже действия, чтобы освободить все виртуальные машины в группе доступности, изменить их размер, а затем перезапустить.
4. Остановите все виртуальные машины в группе доступности.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. Измените размер виртуальных машин в группе доступности и перезапустите их.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>Дополнительная информация
* Для дополнительной масштабируемости запустите несколько экземпляров виртуальных машин и разверните их. Дополнительные сведения см. в разделе [Автоматическое масштабирование ВМ в наборе масштабирования ВМ](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

