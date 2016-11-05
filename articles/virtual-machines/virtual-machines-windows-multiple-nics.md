---
title: Настройка нескольких сетевых карт на виртуальной машине Windows | Microsoft Docs
description: Узнайте, как создать виртуальную машину с несколькими сетевыми картами с помощью Azure PowerShell или шаблонов Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2016
ms.author: iainfou

---
# Создание виртуальной машины с несколькими сетевыми картами
Можно создать виртуальную машину (ВМ) в Azure, к которой подключено несколько виртуальных сетевых интерфейсов (сетевых карт). Распространен сценарий, когда разные подсети используются для интерфейсных и внутренних подключений, или когда для решения мониторинга или архивации используется выделенная сеть. Этой статье описываются быстрые команды для создания виртуальной машины с несколькими сетевыми картами. Чтобы получить дополнительные сведения, в том числе узнать, как создать нескольких сетевых карт в собственных сценариях PowerShell, узнайте больше о [развертывании виртуальных машин с несколькими сетевыми картами](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Различные [размеры виртуальных машин](virtual-machines-windows-sizes.md) поддерживают разное число сетевых карт, так что выбирайте соответствующий размер виртуальной машины.

> [!WARNING]
> Подключать несколько сетевых карт следует при создании виртуальной машины. Их нельзя добавить в существующую виртуальную машину. Вы можете [создать новую виртуальную машины на основе исходных виртуальных дисков](virtual-machines-windows-specialized-image.md) и создать несколько сетевых карт при развертывании это виртуальной машины.
> 
> 

## Создание основных ресурсов
Убедитесь, что у вас установлена и настроена [последняя версия Azure PowerShell](../powershell-install-configure.md).

Сначала создайте группу ресурсов.

```powershell
New-AzureRmResourceGroup -Name TestRG -Location WestUS
```

Создайте учетную запись хранения для размещения виртуальных машин.

```powershell
$storageAcc = New-AzureRmStorageAccount -Name teststorage `
    -ResourceGroupName TestRG -Kind Storage -SkuName Premium_LRS -Location WestUS
```

## Создание виртуальных сетей и подсетей
Определите две подсети виртуальной сети — для интерфейсного трафика и для внутреннего трафика. Создайте виртуальную сеть с этими подсетями.

```powershell
$frontEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" `
    -AddressPrefix 192.168.1.0/24
$backEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "BackEnd" `
    -AddressPrefix 192.168.2.0/24


$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location WestUS `
    -Subnet $frontEndSubnet,$backEndSubnet
```


## Создание нескольких сетевых карт
Создайте две сетевые карты и подключите одну из них к интерфейсной подсети, а другую — к внутренней подсети.

```powershell
$frontEnd = $vnet.Subnets|?{$_.Name -eq 'FrontEnd'}
$NIC1 = New-AzureRmNetworkInterface -Name NIC1 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $FrontEnd.Id

$backEnd = $vnet.Subnets|?{$_.Name -eq 'BackEnd'}
$NIC2 = New-AzureRmNetworkInterface -Name NIC2 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $BackEnd.Id
```

Обычно также создается [группа безопасности сети](../virtual-network/virtual-networks-nsg.md) или [балансировщик нагрузки](../load-balancer/load-balancer-overview.md) для управления трафиком и его распределения между виртуальными машинами. В более [подробной статье о виртуальной машине с несколькими сетевыми картами](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) приводятся инструкции по созданию группы безопасности сети и назначению сетевых карт.

## Создание виртуальной машины
Теперь начните создание конфигурации виртуальной машины. Для каждого размера виртуальной машины существует ограничение на общее количество сетевых карт, которые можно в нее добавить. Прочитайте дополнительные сведения о [размерах виртуальных машин Windows](virtual-machines-windows-sizes.md). В следующем примере используется размер виртуальной машины, поддерживающий до двух сетевых карт (`Standard_DS2_v2`).

```powershell
$cred = Get-Credential

$vmConfig = New-AzureRmVMConfig -VMName TestVM -VMSize "Standard_DS2_v2"

$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName TestVM `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
```

Подключите две сетевые карты, созданные ранее.

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC2.Id
```

Настройте хранилище и виртуальный диск для новой виртуальной машины.

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption fromImage
```

Наконец, создайте виртуальную машину.

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName TestRG -Location WestUS
```

## Создание нескольких сетевых карт с помощью шаблонов Resource Manager
В шаблонах Azure Resource Manager используются декларативные JSON-файлы для определения среды. Вы можете прочитать [обзор Azure Resource Manager](../resource-group-overview.md), чтобы узнать больше об этом. Шаблоны Resource Manager дают возможность создать несколько экземпляров ресурса во время развертывания, в том числе создать несколько сетевых карт. Чтобы указать число создаваемых экземпляров, используется объект *copy*.

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Вы можете прочитать дополнительные сведения о [создании нескольких экземпляров с помощью объекта *copy*](../resource-group-create-multiple.md).

Можно также использовать `copyIndex()`, чтобы добавить номер к имени ресурса, что позволяет создать `NIC1`, `NIC2` и т. д. Ниже показан пример добавления значения индекса.

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

Вы можете ознакомиться с полным примером [создания нескольких сетевых карт с помощью шаблонов Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Дальнейшие действия
Обязательно ознакомьтесь с [размерами виртуальных машин Windows](virtual-machines-windows-sizes.md), когда будете создавать виртуальную машину с несколькими сетевыми картами. Обратите внимание на максимальное число сетевых карт, поддерживаемых каждым из размеров виртуальной машины.

Помните, что невозможно добавить дополнительные сетевые карты в существующую виртуальную машины. Все сетевые карты должны быть созданы при развертывании виртуальной машины. Будьте внимательны при планировании развертываний. С самого начала убедитесь в наличии всех необходимых сетевых подключений.

<!---HONumber=AcomDC_0817_2016-->