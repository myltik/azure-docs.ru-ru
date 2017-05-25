---
title: "Создание виртуальной машины Windows с несколькими сетевыми картами | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину Windows с несколькими сетевыми картами с помощью Azure PowerShell или шаблонов Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 6918b206c637e1e0ad99b472c6a45a4fc343dc6d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="create-a-windows-vm-with-multiple-nics"></a>Создание виртуальной машины Windows с несколькими сетевыми картами
Можно создать виртуальную машину (ВМ) в Azure, к которой подключено несколько виртуальных сетевых интерфейсов (сетевых карт). Распространен сценарий, когда разные подсети используются для интерфейсных и внутренних подключений, или когда для решения мониторинга или архивации используется выделенная сеть. Этой статье описываются быстрые команды для создания виртуальной машины с несколькими сетевыми картами. Чтобы получить дополнительные сведения, в том числе узнать, как создать нескольких сетевых карт в собственных сценариях PowerShell, узнайте больше о [развертывании виртуальных машин с несколькими сетевыми картами](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Различные [размеры виртуальных машин](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины.

## <a name="create-core-resources"></a>Создание основных ресурсов
Убедитесь, что у вас установлена и настроена [последняя версия Azure PowerShell](/powershell/azure/overview). Войдите в свою учетную запись Azure.

```powershell
Login-AzureRmAccount
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `mystorageaccount`, и `myVM`.

Сначала создайте группу ресурсов. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `WestUs`:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Создайте учетную запись хранения для размещения виртуальных машин. В следующем примере создается учетная запись хранения с именем `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Создание виртуальных сетей и подсетей
Определите две подсети виртуальной сети — для интерфейсного трафика и для внутреннего трафика. В следующем примере определяются две подсети — `mySubnetFrontEnd` и `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Создайте виртуальную сеть и подсети. В следующем примере создается виртуальная сеть `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Создание нескольких сетевых карт
Создайте две сетевые карты и подключите одну из них к интерфейсной подсети, а другую — к внутренней подсети. В следующем примере создаются две сетевые карты — `myNic1` и `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

Обычно также создается [группа безопасности сети](../../virtual-network/virtual-networks-nsg.md) или [балансировщик нагрузки](../../load-balancer/load-balancer-overview.md) для управления трафиком и его распределения между виртуальными машинами. В более [подробной статье о виртуальной машине с несколькими сетевыми картами](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md) приводятся инструкции по созданию группы безопасности сети и назначению сетевых карт.

## <a name="create-the-virtual-machine"></a>Создание виртуальной машины
Теперь начните создание конфигурации виртуальной машины. Для каждого размера виртуальной машины существует ограничение на общее количество сетевых карт, которые можно в нее добавить. Прочитайте дополнительные сведения о [размерах виртуальных машин Windows](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Сначала задайте переменной `$cred` учетные данные виртуальной машины:

```powershell
$cred = Get-Credential
```

В следующем примере определяется виртуальная машина `myVM` и используется размер виртуальной машины, поддерживающий до двух сетевых карт (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Настройте остальные параметры виртуальной машины. В следующем примере создается виртуальная машина Windows Server 2012 R2:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Подключите две сетевые карты, созданные ранее.

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Настройте хранилище и виртуальный диск для новой виртуальной машины.

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Наконец, создайте виртуальную машину.

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="add-a-nic-to-an-existing-vm"></a>Добавление сетевой карты в существующую виртуальную машину

В существующую виртуальную машину можно добавить сетевую карту. Чтобы использовать эту функцию, сначала необходимо отменить распределение виртуальной машины, выполнив приведенный ниже командлет Stop-AzureRmVM.

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

Затем нужно получить конфигурацию виртуальной машины с помощью командлета Get-AzureRmVM.

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

Можно создать сетевую карту в **виртуальной сети, в которой находится виртуальная машина**, как показано в начале этой статьи, или подключить существующую сетевую карту. Предположим, что вы подключаете имеющийся сетевой интерфейс `MyNic3` в виртуальной сети. 

```powershell
$nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId -Primary | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
```

> [!NOTE]
> Если у виртуальной машины несколько сетевых карт, то одна из них должна быть основной. Поэтому мы настроим новую сетевую карту как основную. Если основной является предыдущая сетевая карта в виртуальной машине, то параметр Primary указывать не нужно. Если требуется переключить основную сетевую карту на виртуальной машине, выполните следующие действия.

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"

# Find out all the NICs on the VM and find which one is Primary
$vm.NetworkProfile.NetworkInterfaces

# Set the NIC 0 to be primary
$vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
$vm.NetworkProfile.NetworkInterfaces[1].Primary = $false

# Update the VM state in Azure
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
```

## <a name="remove-a-nic-from-an-existing-vm"></a>Удаление сетевой карты из существующей виртуальной машины

Сетевую карту можно также удалить из виртуальной машины. Чтобы использовать эту функцию, сначала необходимо отменить распределение виртуальной машины, выполнив приведенный ниже командлет Stop-AzureRmVM.

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

Затем нужно получить конфигурацию виртуальной машины с помощью командлета Get-AzureRmVM.

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

Теперь просмотрите все сетевые карты виртуальной машины и скопируйте имя той из них, которую нужно удалить.

```powershell
$vm.NetworkProfile.NetworkInterfaces
Remove-AzureRmNetworkInterface -Name "myNic3" -ResourceGroupName "myResourceGroup" | `
    Update-AzureRmVm -ResourceGroupName "myResourceGroup"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Создание нескольких сетевых карт с помощью шаблонов Resource Manager
В шаблонах Azure Resource Manager используются декларативные JSON-файлы для определения среды. Дополнительные сведения см. в [обзоре Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Шаблоны Resource Manager дают возможность создать несколько экземпляров ресурса во время развертывания, в том числе создать несколько сетевых карт. Чтобы указать число создаваемых экземпляров, используется объект *copy* .

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Вы можете прочитать дополнительные сведения о [создании нескольких экземпляров с помощью объекта *copy*](../../resource-group-create-multiple.md). 

Можно также использовать `copyIndex()`, чтобы добавить номер к имени ресурса, что позволяет создать `myNic1`, `MyNic2` и т. д. Ниже показан пример добавления значения индекса.

```json
"name": "[concat('myNic', copyIndex())]", 
```

Вы можете ознакомиться с полным примером [создания нескольких сетевых карт с помощью шаблонов Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Дальнейшие действия
Обязательно ознакомьтесь с [размерами виртуальных машин Windows](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , когда будете создавать виртуальную машину с несколькими сетевыми картами. Обратите внимание на максимальное число сетевых карт, поддерживаемых каждым из размеров виртуальной машины. 



