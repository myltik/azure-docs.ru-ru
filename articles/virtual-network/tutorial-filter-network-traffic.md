---
title: Руководство. Фильтрация сетевого трафика с помощью Azure PowerShell | Документация Майкрософт
description: В этом руководстве описано, как фильтровать сетевой трафик в подсети с помощью группы безопасности сети и PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 165bd6770109348bd19ebb4fa1735bedf83004b1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261323"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-powershell"></a>Руководство. Фильтрация сетевого трафика с помощью группы безопасности сети и PowerShell

Вы можете отфильтровать входящий и исходящий трафик в подсети виртуальной сети с помощью группы безопасности сети. Группы безопасности сети содержат правила безопасности, которые фильтруют трафик по IP-адресу, порту и протоколу. Правила безопасности применяются к ресурсам, развернутым в подсети. Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание группы безопасности сети и правил безопасности.
> * Создание виртуальной сети и привязка группы безопасности сети к подсети.
> * Развертывание виртуальных машин в подсеть.
> * Тестирование фильтров трафика

При необходимости инструкции из этого руководства можно выполнить с помощью [Azure CLI](tutorial-filter-network-traffic-cli.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет ` Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Группа безопасности сети содержит правила безопасности. Правила безопасности указывают источник и назначение. Источники и назначения могут быть группами безопасности приложений.

### <a name="create-application-security-groups"></a>Создание групп безопасности приложений

Создайте группу ресурсов для всех ресурсов, созданных в рамках этого руководства, выполнив командлет [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов в расположении *eastus*. 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Создайте группу безопасности приложений с помощью командлета [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Группа безопасности приложений позволяет группировать серверы с аналогичными требованиями к фильтрации портов. В следующем примере создаются две группы безопасности приложений.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Создание правил безопасности

Создайте правило безопасности с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). В следующем примере создается правило, разрешающее входящий поток трафика из Интернета в группу безопасности приложений *myWebServers* через порты 80 и 443:

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

В этом руководстве RDP-подключение (порт 3389) доступно в Интернете для виртуальной машины *myAsgMgmtServers*. В рабочих средах вместо открытия порта 3389 для доступа из Интернета рекомендуется подключиться к ресурсам Azure, которыми вы хотите управлять, с помощью [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-подключения или [частного](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) сетевого подключения.

### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). В следующем примере создается группа безопасности сети с именем *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть с именем *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Создайте конфигурацию подсети с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig), а затем запишите эту конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). В следующем примере добавляется подсеть с именем *mySubnet* в виртуальную сеть и ей назначается группа безопасности сети *myNsg*:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Прежде чем создавать виртуальные машины, получите объект виртуальной сети с подсетью с помощью командлета [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork).

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Создайте общедоступный IP-адрес для каждой виртуальной машины с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress).

```powershell-interactive
$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Создайте два сетевых интерфейса с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) и назначьте каждому из них общедоступный IP-адрес. В следующем примере создается сетевой интерфейс, которому назначается общедоступный IP-адрес *myVmWeb*. Этот сетевой интерфейс добавляется в группу безопасности приложений *myAsgWebServers*.

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

В следующем примере создается сетевой интерфейс, которому назначается общедоступный IP-адрес *myVmMgmt*. Этот сетевой интерфейс добавляется в группу безопасности приложений *myAsgMgmtServers*.

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Создайте две виртуальные машины в виртуальной сети, чтобы вы могли проверить фильтрацию трафика на более позднем этапе. 

Создайте конфигурацию виртуальной машины с помощью командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), затем создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина, которая будет служить веб-сервером. Чтобы можно было перейти к следующему шагу, параметр `-AsJob` позволяет создать виртуальную машину в фоновом режиме. 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Создайте виртуальную машину в качестве сервера управления:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Создание виртуальной машины занимает несколько минут. Не переходите к следующему шагу, пока Azure не закончит создание виртуальной машины.

## <a name="test-traffic-filters"></a>Тестирование фильтров трафика

Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Приведенный ниже пример возвращает общедоступный IP-адрес виртуальной машины *myVmMgmt*.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

На локальном компьютере выполните следующую команду для создания сеанса удаленного рабочего стола с виртуальной машиной *myVmMgmt*. Замените `<publicIpAddress>` IP-адресом, полученным от предыдущей команды.

```
mstsc /v:<publicIpAddress>
```

Откройте загруженный RDP-файл. При появлении запроса выберите **Подключиться**.

Введите имя пользователя и пароль, которые вы указали при создании виртуальной машины (возможно, для этого придется выбрать **Дополнительные варианты** и **Использовать другую учетную запись**). Затем нажмите кнопку **ОК**. При входе в систему может появиться предупреждение о сертификате. Чтобы продолжить процесс подключения, выберите **Да**. 
   
Подключение будет установлено успешно, так как через порт 3389 разрешено получать входящий трафик из Интернета в группу безопасности приложений *myAsgMgmtServers*, в которой находится сетевой интерфейс, подключенный к виртуальной машине *myVmMgmt*.

Используйте следующую команду PowerShell, чтобы создать подключение к удаленному рабочему столу виртуальной машины *myVmWeb* из виртуальной машины *myVmMgmt*.

``` 
mstsc /v:myvmWeb
```

Подключение будет установлено успешно, так как правило безопасности по умолчанию в каждой группе безопасности сети разрешает трафик через все порты между всеми IP-адресами в виртуальной сети. Невозможно установить подключение к удаленному рабочему столу виртуальной машины *myVmWeb* из Интернета, потому что правило безопасности для *myAsgWebServers* не разрешает получение входящего трафика через порт 3389 из Интернета.

Используйте следующую команду PowerShell для установки Microsoft IIS на виртуальной машине *myVmWeb*.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Когда установка служб IIS завершится, отключитесь от виртуальной машины *myVmWeb*, после чего вы останетесь в сеансе удаленного рабочего стола виртуальной машины *myVmMgmt*. Чтобы просмотреть экран приветствия IIS, откройте браузер и перейдите по адресу http://myVmWeb.

Отключитесь от виртуальной машины *myVmMgmt*.

На своем компьютере введите приведенную ниже команду PowerShell, чтобы получить общедоступный IP-адрес сервера *myVmWeb*.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Чтобы убедиться в наличии доступа к веб-серверу *myVmWeb* извне Azure, откройте веб-браузер на своем компьютере и перейдите по адресу `http://<public-ip-address-from-previous-step>`. Подключение будет установлено успешно, так как через порт 80 разрешено получение входящего трафика из Интернета в группу безопасности приложений *myAsgWebServers*, в которой находится сетевой интерфейс, подключенный к виртуальной машине *myVmWeb*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали группу безопасности сети и связали ее с подсетью виртуальной сети. Дополнительные сведения о группах безопасности сети см. в статьях [Безопасность сети](security-overview.md) и [Create, change, or delete a network security group](manage-network-security-group.md) (Создание, изменение или удаление группы безопасности сети).

Azure маршрутизирует трафик между подсетями по умолчанию. Вместо этого вы можете перенаправлять трафик между подсетями через виртуальную машину, которая используется в качестве брандмауэра. Чтобы узнать, как создать таблицу маршрутов, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Создание таблицы маршрутов](./tutorial-create-route-table-portal.md)
