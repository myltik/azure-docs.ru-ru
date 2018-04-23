---
title: Маршрутизация трафика с помощью Azure PowerShell | Документация Майкрософт
description: Из этой статьи вы узнаете, как маршрутизировать трафик с помощью таблицы маршрутов и PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 2aca1de567dbd4d37daf7f9dd7c407b669396a47
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Маршрутизация сетевого трафика с помощью таблицы маршрутов при использовании PowerShell

По умолчанию Azure автоматически маршрутизирует трафик между всеми подсетями в виртуальной сети. Для переопределения маршрутизации Azure по умолчанию можно создать собственные маршруты. Возможность создания настраиваемых маршрутов полезна, если, к примеру, требуется маршрутизировать трафик между подсетями через виртуальный сетевой модуль. В этой статье раскрываются следующие темы:

* Создание таблицы маршрутов
* Создание маршрута
* Создание виртуальной сети с несколькими подсетями
* Связывание таблицы маршрутов с подсетью
* создание виртуального сетевого модуля, который перенаправляет трафик;
* развертывание виртуальных машин в разных подсетях;
* направление трафика из одной подсети в другую через виртуальный сетевой модуль.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="create-a-route-table"></a>Создание таблицы маршрутов

Прежде чем создать таблицу маршрутов, создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* для всех ресурсов, создаваемых в этой статье. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Создайте таблицу маршрутов с помощью командлета [New AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). В следующем примере создается таблица маршрутов с именем *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Создание маршрута

Создайте маршрут путем извлечения объекта таблицы маршрутов с помощью командлета [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), создайте маршрут с помощью командлета [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), а затем запишите конфигурацию маршрута в таблицу маршрутов с помощью командлета [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Связывание таблицы маршрутов с подсетью

Чтобы связать таблицу маршрутов с подсетью, необходимо создать виртуальную сеть и подсеть. Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть с именем *myVirtualNetwork* и префиксом адреса *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Создайте три подсети путем создания трех конфигураций подсети с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). В следующем примере создается три конфигурации подсети: для *общей* (Public), *частной* (Private)подсети и подсети *DMZ*.

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Запишите конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), который создает подсети в виртуальной сети:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Свяжите таблицу маршрутов *myRouteTablePublic* с *общей* подсетью с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig), а затем запишите конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>Создание виртуального сетевого модуля

Виртуальный сетевой модуль представляет собой виртуальную машину, которая выполняет сетевые функции, такие как маршрутизация, защита брандмауэром или оптимизация доступа в глобальную сеть.

Перед созданием виртуальной машины создайте сетевой интерфейс.

### <a name="create-a-network-interface"></a>Создание сетевого интерфейса

Перед созданием сетевого интерфейса необходимо получить идентификатор виртуальной сети с помощью командлета [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), а затем — идентификатор подсети с помощью командлета [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Создайте сетевой интерфейс с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) в подсети *DMZ* с включенной IP-пересылкой:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Создание виртуальной машины

Чтобы создать виртуальную машину и подключить к ней имеющийся сетевой интерфейс, сначала необходимо создать конфигурацию виртуальной машины с помощью командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Конфигурация включает в себя сетевой интерфейс, созданный на предыдущем шаге. Когда появится запрос на ввод имени пользователя и пароля, выберите те, которые будут использоваться для входа в виртуальную машину. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Создайте виртуальную машину с созданной ранее конфигурацией с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина с именем *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

Чтобы можно было перейти к следующему шагу, параметр `-AsJob` позволяет создать виртуальную машину в фоновом режиме.

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте две виртуальные машины в виртуальной сети, чтобы в дальнейшем можно было проверить, что трафик из подсети *Public* направляется в подсеть *Private* через сетевой виртуальный модуль. 

Создайте виртуальную машину в подсети *Public* с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина с именем *myVmPublic* в подсети *Public* в виртуальной сети *myVirtualNetwork*. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Создайте виртуальную машину в подсети *Private*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Создание виртуальной машины занимает несколько минут. Не переходите к следующему шагу, пока не создастся виртуальная машина и Azure не вернет выходные данные в PowerShell.

## <a name="route-traffic-through-an-nva"></a>Перенаправление трафика через виртуальный сетевой модуль

Чтобы получить общедоступный IP-адрес виртуальной машины *myVmPrivate*, выполните командлет [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Приведенный ниже пример возвращает общедоступный IP-адрес виртуальной машины *myVmPrivate*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Для создания сеанса удаленного рабочего стола с виртуальной машиной *myVmPrivate* на локальном компьютере выполните следующую команду. Замените `<publicIpAddress>` IP-адресом, полученным от предыдущей команды.

```
mstsc /v:<publicIpAddress>
```

Откройте загруженный RDP-файл. При появлении запроса выберите **Подключиться**.

Введите имя пользователя и пароль, которые вы указали при создании виртуальной машины (возможно, для этого придется выбрать **Дополнительные варианты** и **Использовать другую учетную запись**). Затем нажмите кнопку **ОК**. При входе в систему может появиться предупреждение о сертификате. Чтобы продолжить процесс подключения, выберите **Да**. 

Для проверки маршрутизации позже будет использоваться команда tracert.exе. Tracert использует протокол ICMP, который запрещен в брандмауэре Windows. Разрешите протокол ICMP в брандмауэре Windows, выполнив в интерфейсе PowerShell на виртуальной машине *myVmPrivate* следующую команду:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Хотя в этой статье и используется команда трассировки для тестирования маршрутизации, мы рекомендуем не пропускать входящий трафик по протоколу ICMP через брандмауэр Windows в рабочей среде.

Вы уже включили в Azure IP-пересылку для сетевого интерфейса виртуальной машины, выполняя инструкции из раздела [Включение IP-пересылки](#enable-ip-forwarding). Операционная система или приложение, работающие на виртуальной машине, также должны уметь перенаправлять сетевой трафик. Включите IP-пересылку в операционной системе на *myVmNva*.

Из командной строки на виртуальной машине *myVmPrivate* подключитесь к виртуальной машине *myVmNva* по протоколу удаленного рабочего стола:

``` 
mstsc /v:myvmnva
```
    
Чтобы включить IP-пересылку в операционной системе, введите следующую команду PowerShell на виртуальной машине *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Перезапустите виртуальную машину *myVmNva*. При этом автоматически завершится сеанс удаленного рабочего стола.

Когда виртуальная машина *myVmNva* перезапустится, создайте сеанс удаленного рабочего стола для виртуальной машины *myVmPublic*, сохраняя подключение к виртуальной машине *myVmPrivate*:

``` 
mstsc /v:myVmPublic
```
    
Разрешите протокол ICMP в брандмауэре Windows с помощью следующей команды PowerShell на виртуальной машине *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Протестируйте маршрутизацию сетевого трафика к виртуальной машине *myVmPrivate* от виртуальной машины *myVmPublic*, выполнив следующую команду PowerShell на виртуальной машине *myVmPublic*:

```
tracert myVmPrivate
```

Ответ будет выглядеть примерно так:
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
Можно заметить, что первым прыжком указан частный IP-адрес виртуального сетевого модуля (10.0.2.4). Второй прыжок — 10.0.1.4. Это частный IP-адрес виртуальной машины *myVmPrivate*. Из-за маршрута, добавленного в таблицу маршрутов *myRouteTablePublic* и связанного с подсетью *Public*, трафик в Azure был маршрутизирован через NVA, а не напрямую в подсеть *Private*.

Закройте сеанс удаленного рабочего стола с виртуальной машиной *myVmPublic*, сохраняя подключение к виртуальной машине *myVmPrivate*.

Протестируйте маршрутизацию сетевого трафика к виртуальной машине *myVmPublic* от виртуальной машины *myVmPrivate*, выполнив следующую команду интерфейса командной строки на виртуальной машине *myVmPrivate*:

```
tracert myVmPublic
```

Ответ будет выглядеть примерно так:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
   
Trace complete.
```

Вы увидите, что трафик направляется непосредственно от виртуальной машины *myVmPrivate* на виртуальную машину *myVmPublic*. По умолчанию Azure маршрутизирует трафик между подсетями напрямую.

Закройте сеанс удаленного рабочего стола с виртуальной машиной *myVmPrivate*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив командлет [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этой статьей вы создали таблицу маршрутов и связали ее с подсетью. Вы создали простой сетевой виртуальный модуль, который направляет трафик из общедоступной подсети в частную подсеть. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) предоставляет широкий ассортимент предварительно настроенных виртуальных сетевых модулей для разных сетевых функций, например модули брандмауэра и оптимизации доступа к глобальной сети. См. дополнительные сведения о [маршрутизации](virtual-networks-udr-overview.md) и [управлении таблицей маршрутов](manage-route-table.md).

Хотя в виртуальной сети можно развернуть множество ресурсов Azure, ресурсы для некоторых служб PaaS Azure развернуть невозможно. Тем не менее вы все же можете ограничить доступ к ресурсам некоторых служб PaaS в Azure трафиком только из подсети виртуальной сети. Сведения о том, как это сделать, см. в статье [Ограничение сетевого доступа к ресурсам PaaS посредством конечных точек службы виртуальной сети с помощью Azure CLI](tutorial-restrict-network-access-to-resources-powershell.md).
