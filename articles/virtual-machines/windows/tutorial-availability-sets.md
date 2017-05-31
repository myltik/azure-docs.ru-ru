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
ms.date: 05/08/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 022396a8bf0478414be179b9f7341a459ed2bc60
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---

# <a name="how-to-use-availability-sets"></a>Использование групп доступности

В этом руководстве вы узнаете, как повысить доступность и надежность решений виртуальных машин в Azure с помощью групп доступности. При развертывании виртуальных машин в Azure группа доступности распределяет их между несколькими изолированными аппаратными кластерами. Таким образом, в случае сбоя оборудования или программного обеспечения в Azure будет затронуто только подмножество виртуальных машин, а общее решение останется доступным для использования (с точки зрения использующих его клиентов). 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * "Создать группу доступности"
> * Создание виртуальной машины в группе доступности
> * Проверка доступных размеров виртуальных машин

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="availability-set-overview"></a>Обзор групп доступности

Группа доступности — это логическая группа функций, которые можно использовать в Azure, чтобы гарантировать изоляцию ресурсов виртуальной машины во время развертывания в центре обработки данных Azure. Azure гарантирует, что виртуальные машины, размещенные в группе доступности, выполняются на нескольких физических серверах, в вычислительных стойках, в пределах единиц хранения и сетевых коммутаторов. Таким образом, в случае сбоя оборудования или программного обеспечения в Azure будет затронуто только подмножество виртуальных машин, а приложение продолжит работать и останется доступным для клиентов. Группы доступности — это важный элемент при создании надежных облачных решений.

Рассмотрим стандартное решение на основе виртуальной машины, в котором может находиться 4 внешних веб-сервера и использоваться 2 внутренние виртуальные машины, содержащие базу данных. С помощью Azure вы можете определить 2 группы доступности перед развертыванием виртуальных машин: группу доступности для веб-уровня и группу доступности для уровня базы данных. Затем при создании виртуальной машины можно указать группу доступности в качестве параметра для команды az vm create. Azure автоматически изолирует виртуальные машины, созданные в группе доступности, в нескольких ресурсах физического оборудования. Это означает, что если возникла проблема с физическим оборудованием, на котором запущены виртуальные машины сервера базы данных или веб-сервера, то другие экземпляры этих виртуальных машин будут по-прежнему нормально работать, так как они используют другое оборудование.

Если требуется развернуть надежные решения на основе виртуальной машины в Azure, следует всегда использовать группы доступности.

## <a name="create-an-availability-set"></a>"Создать группу доступности"

Создать группу доступности можно с помощью командлета [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). В этом примере мы задали число доменов обновления и сбоя равным *2* для группы доступности *myAvailabilitySet* в группе ресурсов *myResourceGroupAvailability*.

Создайте группу ресурсов.

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```


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

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * "Создать группу доступности"
> * Создание виртуальной машины в группе доступности
> * Проверка доступных размеров виртуальных машин

Перейдите к следующему руководству, чтобы узнать о масштабируемых наборах виртуальных машин.

> [!div class="nextstepaction"]
> [Создание масштабируемого набора виртуальных машин](tutorial-create-vmss.md)



