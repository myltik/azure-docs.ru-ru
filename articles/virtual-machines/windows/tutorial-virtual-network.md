---
title: "Виртуальные сети и виртуальные машины Windows в Azure | Документы Майкрософт"
description: "Руководство по управлению виртуальными сетями Azure и виртуальными машинами Windows с помощью Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 54a4a37d581f023610cd61835bdc76814fbd46e0
ms.contentlocale: ru-ru
ms.lasthandoff: 09/01/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Управление виртуальными сетями Azure и виртуальными машинами Windows с помощью Azure PowerShell

Виртуальные машины Azure осуществляют внутреннее и внешнее взаимодействие через сеть Azure. Это руководство содержит сведения о создании нескольких виртуальных машин (ВМ) в виртуальной сети и настройках сетевого взаимодействия между ними. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создать виртуальную сеть
> * Создание подсетей виртуальных сетей
> * Управление сетевым трафиком с помощью групп безопасности сети
> * Просмотр правил трафика в действии

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-vnet"></a>Создание виртуальной сети

Виртуальная сеть — это представление вашей собственной сети в облаке. Это логическая изоляция облака Azure, выделенного для вашей подписки. В виртуальной сети находятся подсети, правила для их взаимодействия и подключения от виртуальных машин к подсетям.

Прежде чем создавать другие ресурсы Azure, нужно создать группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Следующий пример позволяет создать группу ресурсов *myRGNetwork* в расположении *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Подсеть является дочерним ресурсом виртуальной сети, который помогает определить сегменты адресных пространств в пределах блока CIDR на основе префиксов IP-адресов. Сетевые карты можно добавлять в подсети и подключать к виртуальным машинам, обеспечивая сетевые подключения для различных рабочих нагрузок.

Создайте подсеть с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig).

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Создайте виртуальную сеть *myVNet*, использующую *myFrontendSubnet*, выполнив командлет [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork).

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>Создание интерфейсной виртуальной машины

Для взаимодействия в виртуальной сети виртуальной машине требуется виртуальная сетевая карта. Так как доступ к *myFrontendVM* осуществляется через Интернет, требуется общедоступный IP-адрес. 

Создайте общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

Создайте сетевую карту с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):


```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential): Эти учетные данные используются для подключения к виртуальной машине на дополнительных шагах:

```azurepowershell-interactive
$cred = Get-Credential
```

Создайте виртуальные машины с помощью командлетов [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) и [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). 

```azurepowershell-interactive
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="install-web-server"></a>Установка веб-сервера

Установить службы IIS на *myFrontendVM* можно с помощью сеанса удаленного рабочего стола. Вам нужно получить общедоступный IP-адрес виртуальной машины для доступа к ней.

Получить общедоступный IP-адрес *myFrontendVM* можно с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее *myPublicIPAddress*.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myRGNetwork `
    -Name myPublicIPAddress | select IpAddress
```

Запишите этот IP-адрес, чтобы использовать его в последующих шагах.

Используйте приведенную ниже команду для создания сеанса удаленного рабочего стола с *myFrontendVM*. Замените *<publicIPAddress>* записанным ранее адресом. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```
mstsc /v:<publicIpAddress>
``` 

После входа на *myFrontendVM* вы можете установить IIS и включить локальное правило брандмауэра, разрешающее веб-трафик, с помощью одной строки кода PowerShell. Откройте командную строку PowerShell на виртуальной машине из сеанса RDP и выполните следующую команду:

Используйте командлет [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) для запуска расширения настраиваемых сценариев, которое устанавливает веб-сервер IIS:

```azurepowershell-interactive
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Теперь вы можете использовать общедоступный IP-адрес для перехода к виртуальной машине и просмотра сайта IIS.

![Сайт IIS по умолчанию](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>Управление внутренним трафиком

Группа безопасности сети (NSG) содержит перечень правил безопасности, которые разрешают или запрещают передачу сетевого трафика к ресурсам, подключенным к виртуальной сети. Группы безопасности сети можно сопоставить с подсетями или отдельными сетевыми картами, подключенными к виртуальным машинам. Открытие или закрытие доступа к виртуальным машинам осуществляется с помощью правил NSG. При создании *myFrontendVM* входящий порт 3389 автоматически был открыт для RDP-подключений.

С помощью группы безопасности сети можно настроить взаимодействие внутри виртуальных машин. В этом разделе вы узнаете, как создать в сети дополнительную подсеть и назначить ей группу безопасности сети, чтобы разрешить подключение из *myFrontendVM* к *myBackendVM* через порт 1433. После этого подсеть назначается виртуальной машине при ее создании.

Можно ограничить внутренний трафик для *myBackendVM* и разрешить только трафик из *myFrontendVM*, создав NSG для внутренней подсети. Следующий пример создает правило NSG *myBackendNSGRule* с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Добавьте группу безопасности сети *myBackendNSG* с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>Добавление внутренней подсети

Добавьте *myBackEndSubnet* в *myVNet* с помощью командлета [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
```

## <a name="create-back-end-vm"></a>Создание внутренней виртуальной машины

Внутреннюю виртуальную машину проще всего создать с помощью образа SQL Server. В этом учебнике лишь создается виртуальная машина с сервером базы данных, и не приводятся сведения о доступе к базе данных.

Создайте *myBackendNic*.

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Создайте *myBackendVM*.

```azurepowershell-interactive
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016SP1-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

В рассматриваемом образе система SQL Server установлена, однако в данном руководстве она не используется. Она показывает, как можно настроить виртуальную машину для обработки веб-трафика, а также для обработки операций управления базой данных.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали и защитили сети Azure с точки зрения виртуальных машин. 

> [!div class="checklist"]
> * Создать виртуальную сеть
> * Создание подсетей виртуальных сетей
> * Управление сетевым трафиком с помощью групп безопасности сети
> * Просмотр правил трафика в действии

Перейдите к следующему руководству, чтобы узнать о мониторинге защиты данных на виртуальных машинах с помощью службы архивации Azure. .

> [!div class="nextstepaction"]
> [Архивация виртуальных машин Windows в Azure](./tutorial-backup-vms.md)

