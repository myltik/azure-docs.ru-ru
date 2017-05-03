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
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 7015a5317c631dad9079f2694051fa7fb28d232b
ms.lasthandoff: 04/26/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Управление виртуальными сетями Azure и виртуальными машинами Windows с помощью Azure PowerShell

Это руководство содержит сведения о создании нескольких виртуальных машин (ВМ) в виртуальной сети и настройке сетевого взаимодействия между ними. После завершения работы "интерфейсная" виртуальная машина будет доступна из Интернета через порт 80 для HTTP-соединений. "Серверная" виртуальная машина с базой данных SQL Server будет изолирована и доступна только из интерфейсной виртуальной машины через порт 1433.

Для работы с этим руководством можно использовать последнюю версию модуля [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="create-vnet"></a>Создание виртуальной сети

Виртуальная сеть — это представление вашей собственной сети в облаке. Это логическая изоляция облака Azure, выделенного для вашей подписки. В виртуальной сети находятся подсети, правила для их взаимодействия и подключения от виртуальных машин к подсетям.

Прежде чем создавать другие ресурсы Azure, нужно создать группу ресурсов с помощью командлета [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-3.8.0). В следующем примере создается группа ресурсов с именем `myRGNetwork` в расположении `westus`:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location westus
```

Подсеть является дочерним ресурсом виртуальной сети, который помогает определить сегменты адресных пространств в пределах блока CIDR на основе префиксов IP-адресов. Сетевые карты можно добавлять в подсети и подключать к виртуальным машинам, обеспечивая сетевые подключения для различных рабочих нагрузок.

Создайте подсеть с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0).

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Создайте виртуальную сеть `myVNet`, используя `myFrontendSubnet`, с помощью командлета [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork?view=azurermps-3.8.0):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>Создание интерфейсной виртуальной машины

Для взаимодействия в виртуальной сети виртуальной машине требуется виртуальная сетевая карта. Доступ к `myFrontendVM` осуществляется через Интернет, поэтому также необходим общедоступный IP-адрес. 

Создайте общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress?view=azurermps-3.8.0):

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

Создайте сетевую карту с помощью командлета [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface?view=azurermps-3.8.0):


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Создайте виртуальные машины с помощью командлетов [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-3.8.0), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem?view=azurermps-3.8.0), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage?view=azurermps-3.8.0), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-3.8.0), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?view=azurermps-3.8.0) и [New-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm?view=azurermps-3.8.0). 

```powershell
$frontendVM = New-AzureRmVMConfig -VMName myFrontendVM -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem -VM $frontendVM -Windows -ComputerName myFrontendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage -VM $frontendVM -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
$frontendVM = Set-AzureRmVMOSDisk -VM $frontendVM -Name myFrontendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface -VM $frontendVM -Id $frontendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $frontendVM
```

## <a name="install-web-server"></a>Установка веб-сервера

Установить службы IIS на `myFrontendVM` можно с помощью сеанса удаленного рабочего стола. Вам нужно получить общедоступный IP-адрес виртуальной машины для доступа к ней.

Получить общедоступный IP-адрес `myFrontendVM` можно с помощью командлета [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-3.8.0). Следующий пример позволяет получить IP-адрес для созданного ранее `myPublicIPAddress`.

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myRGNetwork -Name myPublicIPAddress | select IpAddress
```

Запишите этот IP-адрес, чтобы использовать его в последующих шагах.

Используйте приведенную ниже команду для создания сеанса удаленного рабочего стола с `myFrontendVM`. Замените `<publicIPAddress>` записанным ранее адресом. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```
mstsc /v:<publicIpAddress>
``` 

После входа в `myFrontendVM` вы можете установить IIS и включить локальное правило брандмауэра, разрешающее веб-трафик, с помощью одной строки кода PowerShell. Откройте командную строку PowerShell и выполните следующую команду:

Используйте командлет [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) для запуска расширения настраиваемых сценариев, которое устанавливает веб-сервер IIS:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Теперь вы можете использовать общедоступный IP-адрес для перехода к виртуальной машине и просмотра сайта IIS.

![Сайт IIS по умолчанию](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>Управление внутренним трафиком

Группа безопасности сети (NSG) содержит перечень правил безопасности, которые разрешают или запрещают передачу сетевого трафика к ресурсам, подключенным к виртуальной сети. Группы безопасности сети можно сопоставить с подсетями или отдельными сетевыми картами, подключенными к виртуальным машинам. Открытие или закрытие доступа к виртуальным машинам осуществляется с помощью правил NSG. При создании `myFrontendVM` входящий порт 3389 автоматически был открыт для RDP-взаимодействия.

С помощью группы безопасности сети можно настроить взаимодействие внутри виртуальных машин. В этом разделе вы узнаете, как создать в сети дополнительную подсеть и назначить ей группу безопасности сети, чтобы разрешить подключение из `myFrontendVM` к `myBackendVM` через порт 1433. После этого подсеть назначается виртуальной машине при ее создании.

Можно ограничить внутренний трафик для `myBackendVM` и разрешить только трафик из `myFrontendVM`, создав группу безопасности сети для внутренней подсети. Следующий пример создает правило группы безопасности сети `myBackendNSGRule` с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig?view=azurermps-3.8.0):

```powershell
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

Добавьте новую группу безопасности сети `myBackendNSG` с помощью командлета [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecuritygroup?view=azurermps-3.8.0):

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>Добавление внутренней подсети

Добавьте `myBackEndSubnet` в `myVNet` с помощью командлета [Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0):

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myRGNetwork -Name myVNet
```

## <a name="create-back-end-vm"></a>Создание внутренней виртуальной машины

Внутреннюю виртуальную машину проще всего создать с помощью образа SQL Server. В этом учебнике лишь создается виртуальная машина с сервером базы данных, и не приводятся сведения о доступе к базе данных.

Создайте `myBackendNic`:

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета Get-Credential:

```powershell
$cred = Get-Credential
```

Создайте `myBackendVM`:

```powershell
$backendVM = New-AzureRmVMConfig -VMName myBackendVM -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem -VM $backendVM -Windows -ComputerName myBackendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage -VM $backendVM -PublisherName MicrosoftSQLServer -Offer SQL2016-WS2016 -Skus Enterprise -Version latest
$backendVM = Set-AzureRmVMOSDisk -VM $backendVM -Name myBackendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface -VM $backendVM -Id $backendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $backendVM
```

В рассматриваемом образе система SQL Server установлена, однако в данном руководстве она не используется. Она показывает, как можно настроить виртуальную машину для обработки веб-трафика, а также для обработки операций управления базой данных.


