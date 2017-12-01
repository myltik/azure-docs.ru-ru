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
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2345b434a51b768793c2dea4587dc0a49ab35b70
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-use-availability-sets"></a>Использование групп доступности

В этом руководстве показано, как повысить доступность и надежность решений виртуальных машин в Azure с помощью групп доступности. При развертывании виртуальных машин в Azure группа доступности распределяет их между несколькими изолированными аппаратными узлами в кластере. Таким образом, в случае сбоя оборудования или программного обеспечения в Azure затрагивается только подмножество виртуальных машин, а общее решение остается доступным для использования. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * "Создать группу доступности"
> * Создание виртуальной машины в группе доступности
> * Проверка доступных размеров виртуальных машин
> * Проверка службы "Помощник по Azure"

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="availability-set-overview"></a>Обзор групп доступности

Группа доступности — это логическая группа функций, которые можно использовать в Azure, чтобы гарантировать изоляцию ресурсов виртуальной машины во время развертывания в центре обработки данных Azure. Azure гарантирует, что виртуальные машины, размещенные в группе доступности, выполняются на нескольких физических серверах, в вычислительных стойках, в пределах единиц хранения и сетевых коммутаторов. В случае сбоя оборудования или программного обеспечения в Azure затрагивается только подмножество виртуальных машин, а приложение продолжает работать и остается доступным для клиентов. Группы доступности — это важный элемент при создании надежных облачных решений.

Рассмотрим стандартное решение на основе виртуальной машины, в котором может находиться 4 внешних веб-сервера и использоваться 2 внутренние виртуальные машины, содержащие базу данных. С помощью Azure вы можете определить две группы доступности перед развертыванием виртуальных машин: группу доступности для веб-уровня и группу доступности для уровня базы данных. Затем при создании виртуальной машины можно указать группу доступности в качестве параметра для команды az vm create. Azure автоматически изолирует виртуальные машины, созданные в группе доступности, в нескольких ресурсах физического оборудования. Если возникает проблема с физическим оборудованием, на котором запущены виртуальные машины сервера базы данных или веб-сервера, то другие экземпляры этих виртуальных машин по-прежнему работают, так как они используют другое оборудование.

Если требуется развернуть надежные решения на основе виртуальной машины в Azure, используйте группы доступности.

## <a name="create-an-availability-set"></a>"Создать группу доступности"

Создать группу доступности можно с помощью командлета [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). В этом примере мы задали число доменов обновления и сбоя равным *2* для группы доступности *myAvailabilitySet* в группе ресурсов *myResourceGroupAvailability*.

Создайте группу ресурсов.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Создайте управляемую группу доступности с помощью командлета [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) с параметром **-sku aligned**.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Создание виртуальных машин в группе доступности

Чтобы виртуальные машины правильно распределялись по оборудованию, их нужно создать внутри группы доступности. Существующую виртуальную машину невозможно добавить в группу доступности после создания. 

Оборудование в расположении делится на несколько доменов обновления и доменов сбоя. **Домен обновления** — это группа виртуальных машин и базового физического оборудования, которую можно перезагрузить одновременно. Виртуальные машины в одном **домене сбоя** совместно используют общее хранилище, а также общий источник питания и сетевой коммутатор. 

При создании конфигурации виртуальной машины с помощью командлета [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) можно использовать параметр `-AvailabilitySetId`, чтобы указать идентификатор группы доступности.

Создайте две виртуальные машины в группе доступности с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
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
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig
    
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup `
    -Location eastus `
    -Name myNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAvailability `
    -SecurityRules $nsgRuleRDP
    
# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name mySubnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

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
        -ComputerName myVM$i `
        -Credential $cred `
        -VM $vm `
        -Windows `
        -EnableAutoUpdate `
        -ProvisionVMAgent
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

Если взглянуть на группу доступности на портале, перейдя в раздел "Группы ресурсов" > "myResourceGroupAvailability" > "myAvailabilitySet", то можно увидеть, как виртуальные машины распределены между 2 доменами сбоя и обновления.

![Группа доступности на портале](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Знакомство с доступными размерами виртуальной машины 

Позднее вы можете добавить в группу доступности другие виртуальные машины, но следует понимать, какие размеры виртуальных машин доступны на оборудовании. Выполните командлет [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) для получения списка всех доступных размеров в аппаратном кластере для группы доступности.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="check-azure-advisor"></a>Проверка службы "Помощник по Azure" 

Службу "Помощник по Azure" можно также использовать для получения дополнительных сведений о том, как повысить доступность виртуальных машин. Служба "Помощник по Azure" поможет следовать рекомендациям по оптимизации развернутых служб Azure. Он анализирует конфигурацию ресурсов и данные телеметрии их использования и рекомендует решения, которые помогут повысить эффективность затрат, производительность, уровень доступности и безопасности ресурсов Azure.

Войдите на [портал Azure](https://portal.azure.com), выберите **Больше служб** и введите **Помощник**. Панель мониторинга службы "Помощник" отображает персонализированные рекомендации для выбранной подписки. Дополнительные сведения можно найти в разделе [Приступая к работе с Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * "Создать группу доступности"
> * Создание виртуальной машины в группе доступности
> * Проверка доступных размеров виртуальных машин
> * Проверка службы "Помощник по Azure"

Перейдите к следующему руководству, чтобы узнать о масштабируемых наборах виртуальных машин.

> [!div class="nextstepaction"]
> [Создание масштабируемого набора виртуальных машин](tutorial-create-vmss.md)


