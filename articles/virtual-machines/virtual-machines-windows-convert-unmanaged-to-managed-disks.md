---
title: "Переключение виртуальной машины с неуправляемых на управляемые диски в Azure | Документация Майкрософт"
description: "Переключение виртуальной машины, развернутой в рамках модели Resource Manager, с неуправляемых дисков на управляемые диски с помощью PowerShell"
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
ms.date: 02/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 59798ae9412a7550c94f8fa67c39f504aad8d00c
ms.openlocfilehash: 3867c57d40a218c80403578d30cb999bf9f6cd38
ms.lasthandoff: 03/01/2017


---
# <a name="convert-a-vm-from-unmanaged-disks-to-managed-disks"></a>Переключение виртуальной машины с неуправляемых на управляемые диски

Если у вас есть существующие виртуальные машины Azure, которые используют неуправляемые диски в учетных записях хранения, вы можете переключить их на использование [управляемых дисков](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), чтобы получить дополнительные преимущества. Этот процесс преобразует диск операционной системы и все подключенные диски данных, перенося их с неуправляемых дисков учетной записи хранения на управляемые диски. Вам следует остановить виртуальные машины и отменить их подготовку, а затем использовать PowerShell для переключения виртуальной машины на управляемые диски. После преобразования перезапустите виртуальную машину, и теперь она будет использовать управляемые диски.

Прежде чем начать, внимательно изучите [планирование миграции на управляемые диски](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
Отработайте процесс миграции на тестовой виртуальной машине, прежде чем переходить к миграции в рабочей среде, так как процесс миграции необратим.


> [!IMPORTANT] 
> Во время преобразования будет отменена подготовка виртуальной машины. Это означает, что при последующем запуске она получит новый IP-адрес. Если вам важно, чтоб IP-адрес сохранился, следует использовать зарезервированный IP-адрес.


## <a name="managed-disks-and-azure-storage-service-encryption-sse"></a>Управляемые диски и шифрование службы хранилища (SSE) Azure

Вы не сможете переключить виртуальные машины с неуправляемыми дисками, созданные в рамках модели развертывания Resource Manager, на использование управляемых дисков, если любой из неуправляемых дисков, подключенных к этой виртуальной машине, находится в учетной записи хранения, для которой используется или использовалось ранее [шифрование службы хранилища Azure (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Следующие шаги описывают переключение неуправляемой виртуальной машины, которая подключена или была подключена ранее к зашифрованной учетной записи хранения.

**Диски данных**.
1.    Отсоедините диск данных от виртуальной машины.
2.    Скопируйте VHD в учетную запись хранения, для которой никогда не применялось шифрование SSE. Чтобы скопировать диск в другую учетную запись хранения, используйте [AzCopy](../storage/storage-use-azcopy.md):`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myDataDisk.vhd`
3.    Присоедините скопированный диск к виртуальной машине и переходите к переключению виртуальной машины.

**Диск ОС**.
1.    Остановите виртуальную машину и отмените ее подготовку. При необходимости сохраните конфигурацию виртуальной машины.
2.    Скопируйте VHD с операционной системой в учетную запись хранения, для которой никогда не применялось шифрование SSE. Чтобы скопировать диск в другую учетную запись хранения, используйте [AzCopy](../storage/storage-use-azcopy.md):`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myVhd.vhd`
3.    Создайте новую виртуальную машину, которая использует управляемые диски, и в процессе создания присоедините к ней этот VHD-файл в качестве диска операционной системы.

## <a name="convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set"></a>Переключение виртуальных машин, входящих в группу доступности, на использование управляемой группы доступности с управляемыми дисками

Если виртуальные машины, которые вы хотите переключить на управляемые диски, входят в группу доступности, необходимо сначала преобразовать эту группу доступности в управляемую группу доступности.

Следующий скрипт обновляет группу доступности до управляемой группы доступности, затем отменяет ее подготовку, переключает на управляемые диски и повторно запускает каждую виртуальную машину в этой группе доступности.

```powershell
$rgName = 'myResourceGroup'
$avSetName = 'myAvailabilitySet'

$avSet =  Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Managed

foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
   $vm =  Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

   Stop-AzureRmVM -ResourceGroupName $rgName -Name  $vm.Name -Force

   ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   
    }
```

## <a name="convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type"></a>Переключение существующих виртуальных машин Azure на использование управляемых дисков в хранилище того же класса

> [!IMPORTANT]
> После выполнения следующей процедуры создается один большой двоичный объект, размещенный в контейнере по умолчанию /vhds. Этот объект хранится в файле с именем VMName.xxxxxxx.status. Этот файл создается в Azure, только если на виртуальной машине установлены [расширения виртуальных машин](virtual-machines-windows-classic-agents-and-extensions.md). Не удаляйте этот оставшийся объект состояния. В дальнейшем эта проблема будет решена.

В этом разделе описывается, как можно переключить существующие виртуальные машины Azure с неуправляемых дисков в учетных записях хранения на управляемые диски в хранилище того же класса. Этот процесс позволяет перейти от неуправляемых дисков класса Premium (SSD) на управляемые диски класса Premium, или от неуправляемых дисков класса Standard (HDD) на управляемые диски класса Standard. 

1. Создайте переменные и отмените подготовку виртуальной машины. В этом примере для группы ресурсов устанавливается имя **myResourceGroup**, а для виртуальной машины — **myVM**.

    ```powershell
    $rgName = "myResourceGroup"
    $vmName = "myVM"
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
   
    Вы увидите, что *состояние* виртуальной машины на портале Azure изменится с **Остановлено** на **Остановлено (освобождено)**.
    
2. Преобразуйте все диски, связанные с виртуальной машиной, в том числе диск операционной системы и все диски данных.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
    ```


## <a name="migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks"></a>Перенос существующих виртуальных машин Azure с неуправляемых дисков класса "Стандартный" на управляемые диски класса "Премиум"

В этом разделе показано, как можно перевести существующие виртуальные машины Azure с неуправляемых дисков класса "Стандартный" на управляемые диски класса "Премуим". Чтобы использовать управляемые диски класса "Премиум", необходимо использовать [размер виртуальной машины](virtual-machines-windows-sizes.md), поддерживающий хранилище класса Premium.


1.  Сначала задайте общие параметры. Убедитесь, что выбранный [размер виртуальной машины](virtual-machines-windows-sizes.md) поддерживает хранилище класса Premium.

    ```powershell
    $resourceGroupName = 'YourResourceGroupName'
    $vmName = 'YourVMName'
    $size = 'Standard_DS2_v2'
    ```
1.  Получите ссылку на виртуальную машину с неуправляемыми дисками

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
    ```
    
1.  Остановите виртуальную машину и отмените ее подготовку.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```

1.  Выберите для виртуальной машины размер, поддерживающий хранилище класса Premium, из числа доступных в регионе размещения виртуальной машины.

    ```powershell
    $vm.HardwareProfile.VmSize = $size
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName
    ```

1.  Переведите виртуальную машину с неуправляемых дисков на управляемые диски. 

    Если возникнет внутренняя ошибка сервера, попробуйте повторить этот процесс 2–3 раза, прежде чем обращаться в службу поддержки.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
1. Остановите виртуальную машину и отмените ее подготовку.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```
2.  Перенесите все диски в хранилище класса Premium.

    ```powershell
    $vmDisks = Get-AzureRmDisk -ResourceGroupName $resourceGroupName 
    foreach ($disk in $vmDisks) 
        {
        if($disk.OwnerId -eq $vm.Id)
            {
             $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
             Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $resourceGroupName `
             -DiskName $disk.Name
            }
        }
    ```
1. Запустите виртуальную машину.

    ```powershell
    Start-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    ```
    
Вы можете также использовать для хранения дисков произвольное сочетание хранилищ класса Standard и класса Premium.
    

## <a name="next-steps"></a>Дальнейшие действия

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](virtual-machines-windows-snapshot-copy-managed-disk.md).


