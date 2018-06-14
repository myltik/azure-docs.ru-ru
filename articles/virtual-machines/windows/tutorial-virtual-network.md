---
title: Руководство. Создание и администрирование виртуальных сетей Azure для виртуальных машин Windows | Документация Майкрософт
description: В этом руководстве описано, как с помощью Azure PowerShell создать и администрировать виртуальные сети Azure для виртуальных машин Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a13163949a52503f42642c109a4fd4c1dedd837f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944092"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Руководство. Создание и администрирование виртуальных сетей Azure для виртуальными машинами Windows с помощью Azure PowerShell

Виртуальные машины Azure осуществляют внутреннее и внешнее взаимодействие через сеть Azure. В этом руководстве содержатся сведения о развертывании двух виртуальных машин и настройке для них сети Azure. Примеры, описанные в этом руководстве, предполагают, что на виртуальных машинах размещается веб-приложение с сервером базы данных, но здесь не описывается развертывание самого приложения. Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание виртуальной сети и подсети
> * Создание общедоступного IP-адреса
> * Создание интерфейсной виртуальной машины
> * защищают сетевой трафик;
> * Создание внутренней виртуальной машины

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell версии 5.7.0 и выше. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="vm-networking-overview"></a>Обзор сети виртуальной машины

Виртуальные сети Azure позволяют устанавливать безопасные сетевые подключения между виртуальными машинами, в Интернете, а также между другими службами Azure, такими как база данных SQL Azure. Виртуальные сети разбиты на логические сегменты — подсети. Подсети позволяют контролировать поток сетевого трафика. Это своего рода периметр безопасности. При развертывании виртуальная машина обычно содержит виртуальный сетевой интерфейс, подключенный к подсети.

Во время работы с этим руководством вы создадите указанные ниже ресурсы.

![Виртуальная сеть с двумя подсетями](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet.* Виртуальная сеть, которую используют виртуальные машины для взаимодействия между собой и в Интернете.
- *myFrontendSubnet.* Подсеть в *myVNet*, используемая ресурсами внешних интерфейсов.
- *myPublicIPAddress.* Общедоступный IP-адрес, используемый для доступа к *myFrontendVM* из Интернета.
- *myFrontentNic.* Сетевой интерфейс, используемый *myFrontendVM* для взаимодействия с *myBackendVM*.
- *myFrontendVM.* Виртуальная машина, используемая для взаимодействия Интернета и *myBackendVM*.
- *myBackendNSG.* Группа безопасности сети, которая управляет взаимодействием между *myFrontendVM* и *myBackendVM*.
- *myBackendSubnet.* Подсеть, связанная с *myBackendNSG* и используемая внутренними ресурсами.
- *myBackendNic.* Сетевой интерфейс, используемый *myBackendVM* для взаимодействия с *myFrontendVM*.
- *myBackendVM.* Виртуальная машина, использующая порт 1433 для взаимодействия с *myFrontendVM*.


## <a name="create-a-virtual-network-and-subnet"></a>Создание виртуальной сети и подсети

В этом руководстве создается виртуальная сеть с двумя подсетями: интерфейсная подсеть для размещения веб-приложения и внутренняя подсеть для размещения сервера базы данных.

Прежде чем создать виртуальную машину, выполните командлет [az group create](/powershell/module/azurerm.resources/new-azurermresourcegroup) для создания группы ресурсов. Следующий пример позволяет создать группу ресурсов *myRGNetwork* в расположении *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Создание конфигураций подсети

Создайте конфигурацию подсети с именем *myFrontendSubnet*, выполнив командлет [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig).

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

И создайте конфигурацию подсети с именем *myBackendSubnet*.

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть *myVNet*, использующую *myFrontendSubnet*, и *myBackendSubnet*, выполнив командлет [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork).

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

На этом этапе мы создали сеть и разбили ее на две подсети — одна для интерфейсных служб, а вторая для внутренних служб. В следующем разделе мы создадим виртуальные машины и подключим их к этим подсетям.

## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Общедоступный IP-адрес обеспечивает доступность ресурсов Azure в Интернете. Метод распределения общедоступного IP-адреса может быть динамическим или статическим. По умолчанию выделяется динамический общедоступный IP-адрес. После освобождения виртуальной машины освобождаются также и динамические IP-адреса. Таким образом, IP-адреса изменяются во время каждой операции с освобождением виртуальной машины.

При использовании статического метода выделения IP-адрес остается назначенным виртуальной машине, даже если она в освобожденном состоянии. В этом случае вы не сможете указать IP-адрес. Он выделяется из пула доступных адресов.

Создайте общедоступный IP-адрес с именем *myPublicIPAddress*, выполнив командлет [New AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress).

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Вы можете изменить параметр -AllocationMethod на `Static`, чтобы присвоить статический IP-адрес.

## <a name="create-a-front-end-vm"></a>Создание интерфейсной виртуальной машины

Для взаимодействия в виртуальной сети виртуальной машине требуется виртуальный сетевой адаптер. Создайте сетевой адаптер, выполнив командлет [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface).

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Эти учетные данные используются для подключения к виртуальной машине на дополнительных шагах:

```azurepowershell-interactive
$cred = Get-Credential
```

Создайте виртуальные машины с помощью [New AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>защищают сетевой трафик;

Группа безопасности сети (NSG) содержит перечень правил безопасности, которые разрешают или запрещают передачу сетевого трафика к ресурсам, подключенным к виртуальным сетям Azure. Группы безопасности сети (NSG) можно связать с подсетями или отдельными сетевыми интерфейсами. Группа безопасности сети, связанная с сетевым интерфейсом, применяется только к связанной виртуальной машине. Если группа безопасности сети связана с подсетью, эти правила применяются ко всем ресурсам в подсети.

### <a name="network-security-group-rules"></a>Правила группы безопасности сети

Правила группы безопасности сети определяют сетевые порты, через которые разрешен или запрещен трафик. Эти правила также могут включать исходные и целевые диапазоны IP-адресов, что позволяет контролировать трафик между определенными системами и подсетями. Правилам также присваивается приоритет (от 1 до 4096), и они оцениваются в порядке приоритета. Правило с приоритетом 100 оценивается перед правилом с приоритетом 200.

Все группы NSG содержат набор правил по умолчанию. Эти правила нельзя удалить, но у них самый низкий приоритет, поэтому их можно переопределить, создав другие правила.

- **Виртуальная сеть.** Входящий и исходящий трафик виртуальной сети разрешен в обоих направлениях.
- **Интернет.** Исходящий трафик разрешен, но входящий трафик заблокирован.
- **Подсистема балансировки нагрузки.** Разрешает Azure Load Balancer инициировать проверку работоспособности виртуальных машин и экземпляров роли. Если набор балансировки нагрузки не используется, это правило можно переопределить.

### <a name="create-network-security-groups"></a>Создание групп безопасности сети

Создайте правило входящих подключений с именем *myFrontendNSGRule*, чтобы разрешить входящий веб-трафик в *myFrontendVM*, выполнив командлет [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Можно ограничить внутренний трафик для *myBackendVM* и разрешить только трафик из *myFrontendVM*, создав NSG для внутренней подсети. В следующем примере создается правило группы безопасности сети с именем *myBackendNSGRule*.

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

Добавьте группу безопасности сети *myFrontendNSG* с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Теперь добавьте группу безопасности сети *myBackendNSG* с помощью командлета New-AzureRmNetworkSecurityGroup.

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Добавьте группы безопасности сети к подсетям:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Создание внутренней виртуальной машины

Внутреннюю виртуальную машину для этого руководства проще всего создать с помощью образа SQL Server. В этом учебнике лишь создается виртуальная машина с сервером базы данных, и не приводятся сведения о доступе к базе данных.

Создайте *myBackendNic*.

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Создайте *myBackendVM*.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

В рассматриваемом образе система SQL Server установлена, однако в данном руководстве она не используется. Она показывает, как можно настроить виртуальную машину для обработки веб-трафика, а также для обработки операций управления базой данных.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали и защитили сети Azure с точки зрения виртуальных машин. 

> [!div class="checklist"]
> * Создание виртуальной сети и подсети
> * Создание общедоступного IP-адреса
> * Создание интерфейсной виртуальной машины
> * защищают сетевой трафик;
> * Создание внутренней виртуальной машины.

Перейдите к следующему руководству, чтобы узнать о мониторинге защиты данных на виртуальных машинах с помощью службы архивации Azure.

> [!div class="nextstepaction"]
> [Архивация виртуальных машин Windows в Azure](./tutorial-backup-vms.md)
