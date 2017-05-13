---
title: "Руководство по группам доступности для виртуальных машин Windows в Azure | Документация Майкрософт"
description: "Узнайте о группах доступности для виртуальных машин Windows в Azure."
documentationcenter: 
services: virtual-machines-windows
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
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>Использование групп доступности

В этом учебнике описано, как повысить доступность виртуальных машин, поместив их в логическую группу, называемую группой доступности. При создании виртуальных машин в группе доступности платформа Azure распределяет их по базовой инфраструктуре. Использование группы доступности гарантирует, что в случае планового обслуживания платформы либо аппаратного сбоя по меньшей мере одна виртуальная машина продолжит работать.

Для работы с этим руководством можно использовать последнюю версию модуля [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="availability-set-overview"></a>Обзор групп доступности

Виртуальные машины можно создавать по логическим группам оборудования в базовом центре обработки данных Azure. При создании двух и более виртуальных машин вычислительные ресурсы и ресурсы хранения распределяются по оборудованию, например серверам, сетевым коммутаторам и хранилищу. Это позволяет поддерживать доступность приложения в случае обслуживания компонента оборудования. Группы доступности позволяют задать логические группы.

Группы доступности обеспечивают для виртуальных машин высокий уровень доступности. Следует также убедиться, что приложения способны выдерживать сбои или работы по обслуживанию.

## <a name="create-an-availability-set"></a>"Создать группу доступности"

Создать группу доступности можно с помощью командлета [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). В этом примере мы задали число доменов обновления и сбоя равным *2* для группы доступности *myAvailabilitySet* в группе ресурсов *myResourceGroupAvailability*.


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Создание виртуальных машин в группе доступности

Чтобы виртуальные машины правильно распределялись по оборудованию, их нужно создать внутри группы доступности. Существующую виртуальную машину невозможно добавить в группу доступности после создания. 

Оборудование в расположении делится на несколько доменов обновления и доменов сбоя. **Домен обновления** — это группа виртуальных машин и базового физического оборудования, которую можно перезагрузить одновременно. Виртуальные машины в одном **домене сбоя** совместно используют общее хранилище, а также общий источник питания и сетевой коммутатор. 

При создании виртуальной машины с указанием конфигурации с помощью командлета [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) можно указать группу доступности, задав ее идентификатор в параметре `-AvailabilitySetId`.

Создайте две виртуальные машины в группе доступности с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

Создание и настройка двух виртуальных машин занимает несколько минут. По завершении у вас будут две виртуальные машины, распределенные по базовому оборудованию. 

## <a name="check-for-available-vm-sizes"></a>Знакомство с доступными размерами виртуальной машины 

Позднее вы можете добавить в группу доступности другие виртуальные машины, но следует понимать, какие размеры виртуальных машин доступны на оборудовании. Выполните командлет [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) для получения списка всех доступных размеров в аппаратном кластере для группы доступности.

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы узнали, как создать масштабируемый набор виртуальных машин. Перейдите к следующему руководству, чтобы узнать о масштабируемых наборах виртуальных машин.

[Создание масштабируемого набора виртуальных машин](tutorial-create-vmss.md)



