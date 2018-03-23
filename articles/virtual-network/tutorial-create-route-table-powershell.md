---
title: Маршрутизация сетевого трафика | Документация Майкрософт
description: Сведения о маршрутизации сетевого трафика с помощью таблицы маршрутов при использовании PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Маршрутизация сетевого трафика с помощью таблицы маршрутов при использовании PowerShell

По умолчанию Azure автоматически маршрутизирует трафик между всеми подсетями в виртуальной сети. Для переопределения маршрутизации Azure по умолчанию можно создать собственные маршруты. Возможность создания настраиваемых маршрутов полезна, если, к примеру, требуется маршрутизировать трафик между подсетями через брандмауэр. В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Создание таблицы маршрутов
> * Создание маршрута
> * Связывание таблицы маршрутов с подсетью виртуальной сети.
> * Тестирование маршрутизации.
> * Устранение неполадок с маршрутизацией.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="create-a-route-table"></a>Создание таблицы маршрутов

По умолчанию Azure маршрутизирует трафик между всеми подсетями в виртуальной сети. Дополнительные сведения о маршрутах Azure по умолчанию см. в разделе о [системных маршрутах](virtual-networks-udr-overview.md). Чтобы переопределить маршрутизацию Azure по умолчанию, создайте заполненную таблицу маршрутов и свяжите ее с подсетью виртуальной сети.

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

Таблица маршрутов содержит несколько маршрутов или не содержит их вообще. Создайте маршрут путем извлечения объекта таблицы маршрутов с помощью командлета [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), создайте маршрут с помощью командлета [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), а затем запишите конфигурацию маршрута в таблицу маршрутов с помощью командлета [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

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

Маршрут будет направлять весь трафик, предназначенный для префикса адреса 10.0.1.0/24, через сетевой виртуальный модуль с IP-адресом 10.0.2.4. Сетевой виртуальный модуль и подсеть с указанным префиксом адреса создаются на последующих этапах. Этот маршрут переопределяет маршрут по умолчанию Azure, который перенаправляет трафик между подсетями напрямую. Каждый маршрут определяет тип следующего прыжка, который указывает Azure, как маршрутизировать трафик. В этом примере тип следующего прыжка — *VirtualAppliance*. Дополнительные сведения обо всех доступных типах следующих прыжков в Azure, а также случаях их использования см. в [этой статье](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Связывание таблицы маршрутов с подсетью

Чтобы можно было связать таблицу маршрутов с подсетью, необходимо создать виртуальную сеть и подсеть. Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть с именем *myVirtualNetwork* и префиксом адреса *10.0.0.0/16*.

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

Значения префиксов адресов, заданные для подсети, должны быть в пределах префикса адреса, определенного для виртуальной сети. Префиксы адресов подсети не могут перекрывать друг друга.

Запишите конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), который создает подсети в виртуальной сети:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Таблицу маршрутов можно связать с несколькими подсетями или не связывать ни с одной. Подсеть может иметь не более одной таблицы маршрутов, связанной с ней. Исходящий трафик из подсети маршрутизируется на основе маршрутов по умолчанию Azure. Любые настраиваемые маршруты, добавленные в таблицу маршрутов, связываются с подсетью. Свяжите таблицу маршрутов *myRouteTablePublic* с *общей* подсетью с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig), а затем запишите конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

Перед развертыванием таблицы маршрутов для использования в рабочей среде рекомендуется внимательно ознакомиться со статьями о [маршрутизации в Azure](virtual-networks-udr-overview.md) и [ограничениями в Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Тестирование маршрутизации

Чтобы протестировать маршрутизацию, создайте виртуальную машину, которая будет служить в качестве сетевого виртуального модуля, через который проходит маршрут, созданный на предыдущем шаге. После создания сетевого виртуального модуля необходимо развернуть виртуальную машину в подсетях *Public* (Общая) и *Private* (Частная). Затем маршрутизируйте трафик из подсети *Public* в подсеть *Private* через сетевой виртуальный модуль.

### <a name="create-a-network-virtual-appliance"></a>Создание сетевого виртуального модуля

Виртуальная машина, на которой выполняется сетевое приложение, часто называется сетевым виртуальным модулем. Сетевые виртуальные модули обычно получают трафик, выполняют некоторые действия, затем пересылают или прерывают трафик на основе логики, настроенной в сетевом приложении. 

#### <a name="create-a-network-interface"></a>Создание сетевого интерфейса

На предыдущем шаге был создан маршрут, указывающий сетевой виртуальный модуль в качестве типа следующего прыжка. Виртуальная машина, на которой выполняется сетевое приложение, часто называется сетевым виртуальным модулем. В рабочей среде развертываемый сетевой виртуальный модуль чаще всего представляет собой предварительно настроенную виртуальную машину. В [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) доступны несколько сетевых виртуальных модулей. В этой статье описывается создание базовой виртуальной машины.

К виртуальной машине подключен один или несколько сетевых интерфейсов, которые позволяют ей обмениваться данными с сетью. В сетевом интерфейсе должна быть включена IP-пересылка, чтобы он мог перенаправлять отправленный ему сетевой трафик, который не предназначен для его собственного IP-адреса. Перед созданием сетевого интерфейса необходимо получить идентификатор виртуальной сети с помощью командлета [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), а затем — идентификатор подсети с помощью командлета [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Создайте сетевой интерфейс с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) в подсети *DMZ* с включенной IP-пересылкой:

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

#### <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Чтобы создать виртуальную машину и подключить к ней имеющийся сетевой интерфейс, сначала необходимо создать конфигурацию виртуальной машины с помощью командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Конфигурация включает в себя сетевой интерфейс, созданный на предыдущем шаге. Когда появится запрос на ввод имени пользователя и пароля, выберите те, которые будут использоваться для входа в виртуальную машину. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
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

Создайте виртуальную машину с созданной конфигурацией, используя командлет [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина с именем *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

Параметр `-AsJob` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу. Когда появится запрос, введите имя пользователя и пароль, которые вы будете использовать для входа в виртуальную машину. В рабочей среде развертываемый сетевой виртуальный модуль чаще всего представляет собой предварительно настроенную виртуальную машину. В [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) доступны несколько сетевых виртуальных модулей.

Azure назначила виртуальной машине частный IP-адрес 10.0.2.4, так как это первый доступный IP-адрес в подсети *DMZ* сети *myVirtualNetwork*.

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте две виртуальные машины в виртуальной сети, чтобы в дальнейшем можно было проверить, что трафик из подсети *Public* направляется в подсеть *Private* через сетевой виртуальный модуль. 

Создайте виртуальную машину в подсети *Public* с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина с именем *myVmWeb* в *общей* подсети в виртуальной сети *myVirtualNetwork*. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure назначила виртуальной машине частный IP-адрес 10.0.0.4, так как это первый доступный IP-адрес в подсети *Public* сети *myVirtualNetwork*.

Создайте виртуальную машину в *частной* подсети.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

Создание виртуальной машины занимает несколько минут. Azure назначила виртуальной машине частный IP-адрес 10.0.1.4, так как это первый доступный IP-адрес в подсети *Private* сети *myVirtualNetwork*. 

Не переходите к следующему шагу, пока не создастся виртуальная машина и Azure не вернет выходные данные в PowerShell.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Маршрутизация трафика через виртуальный сетевой модуль

Выполните командлет [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension), чтобы разрешить входящий трафик ICMP на виртуальных машинах *myVmWeb* и *myVmMgmt* через брандмауэр Windows. В результате вы сможете использовать команду tracert, чтобы протестировать связь между виртуальными машинами на более позднем этапе:

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

Выполнение предыдущих команд может занять несколько минут. Не переходите к следующему шагу, пока не будут выполнены команды и выходные данные не будут возвращены в PowerShell. Хотя в этой статье и используется команда tracert для тестирования маршрутизации, не рекомендуется разрешать входящий трафик по протоколу ICMP через брандмауэр Windows в рабочей среде.

Вы можете подключиться к общедоступному IP-адресу виртуальной машины через Интернет. Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). В приведенном ниже примере возвращается общедоступный IP-адрес виртуальной машины *myVmMgmt*.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

На вашем локальном компьютере выполните следующую команду, чтобы создать сеанс удаленного рабочего стола с виртуальной машиной *myVmMgmt*. Замените `<publicIpAddress>` IP-адресом, полученным от предыдущей команды.

```
mstsc /v:<publicIpAddress>
```

Будет создан файл протокола удаленного рабочего стола (RDP-файл). Он будет скачан на ваш компьютер и открыт. Введите имя пользователя и пароль, указанный при создании виртуальной машины, а затем нажмите кнопку **ОК**. (Возможно, нужно будет выбрать **Дополнительные варианты** и **Использовать другую учетную запись**, чтобы указать учетные данные, введенные при создании виртуальной машины.) При входе в систему может появиться предупреждение о сертификате. Чтобы продолжить процесс подключения, нажмите кнопку **Да** или **Продолжить**. 

Вы включили IP-пересылку в Azure для сетевого интерфейса виртуальной машины в разделе [Включение IP-пересылки](#enable-ip-forwarding). Внутри виртуальной машины операционная система или приложение, работающее на виртуальной машине, также должны иметь возможность перенаправлять сетевой трафик. При развертывании сетевого виртуального модуля в рабочей среде он обычно фильтрует, регистрирует или выполняет какую-либо другую функцию перед пересылкой трафика. Однако в этой статье операционная система просто перенаправляет весь трафик, который она получает. Чтобы включить IP-пересылку в операционной системе *myVmNva*:

Из командной строки виртуальной машины *myVmMgmt* подключитесь к виртуальной машине *myVmWeb* по протоколу удаленного рабочего стола:

``` 
mstsc /v:myVmNva
```
    
Чтобы включить IP-пересылку в операционной системе виртуальной машины *myVmNva*, введите следующую команду в PowerShell на виртуальной машине *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Перезагрузите виртуальную машину *​myVmNva*, что также отключит сеанс удаленного рабочего стола, оставив вас в сеансе удаленного рабочего стола, инициированного к виртуальной машине *myVmMgmt*.

После перезагрузки виртуальной машины *myVmNva* используйте следующий командлет, чтобы проверить маршрутизацию трафика к виртуальной машине *myVmWeb* из виртуальной машины *myVmMgmt*.

```
tracert myvmweb
```

Ответ будет выглядеть примерно так:

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

Теперь можно видеть, что трафик направляется непосредственно из виртуальной машины *myVmMgmt* на виртуальную машину *myVmWeb*. Маршруты по умолчанию Azure перенаправляют трафик напрямую между подсетями.

Чтобы подключиться к удаленному рабочему столу виртуальной машины *myVmWeb* из виртуальной машины *myVmMgmt*, выполните следующую команду:

``` 
mstsc /v:myVmWeb
```

Для тестирования маршрутизации трафика между виртуальными машинами *myVmMgmt* и *myVmWeb* введите в командной строке следующую команду:

```
tracert myvmmgmt
```

Ответ будет выглядеть примерно так:

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

Можно заметить, что первому прыжку соответствует 10.0.2.4. Это частный IP-адрес сетевого виртуального модуля. Второй прыжок — 10.0.1.4. Это частный IP-адрес виртуальной машины *myVmMgmt*. Из-за маршрута, добавленного в таблицу маршрутов *myRouteTablePublic* и связанного с подсетью *Public*, трафик в Azure был маршрутизирован через NVA, а не напрямую в подсеть *Private*.

Закройте сеансы удаленного подключения к рабочему столу виртуальной машины *myVmWeb* и *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Устранение неполадок с маршрутизацией

Как было упомянуто ранее, Azure применяет маршруты по умолчанию, которые при необходимости можно переопределить собственными вариантами. Иногда трафик может не маршрутизироваться ожидаемым образом. Командлет [New AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) можно использовать, чтобы включить Наблюдатель за сетями в регионе EastUS, если он еще не включен:

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

Командлет [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) позволяет определить, как маршрутизируется трафик между двумя виртуальными машинами. Например, следующая команда проверяет маршрутизацию трафика от виртуальной машины *myVmWeb* (10.0.0.4) к виртуальной машине *myVmMgmt* (10.0.1.4):

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
После короткого ожидания отобразится результат следующего вида:

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

В выходных данных сообщается, что IP-адрес следующего прыжка для трафика из *myVmWeb* в *myVmMgmt* — 10.0.2.4 (*myVmNva*), что следующий тип прыжка — *VirtualAppliance*, а таблица маршрутов, которая вызывает маршрутизацию, — *myRouteTablePublic*.

Действующие маршруты для каждого сетевого интерфейса представляют собой комбинацию маршрутов по умолчанию Azure и любых определяемых вами маршрутов. Чтобы увидеть все маршруты, действующие для сетевого интерфейса на виртуальной машине, используйте командлет [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Например, чтобы показать действующие маршруты для сетевого интерфейса *myVmWeb* на виртуальной машине *myVmWeb*, введите следующую команду:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Будут возвращены все маршруты по умолчанию и маршрут, добавленный на предыдущем шаге.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив командлет [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этой статьей вы создали таблицу маршрутов и связали ее с подсетью. Вы также создали сетевой виртуальный модуль, который направляет трафик из общедоступной подсети в частную подсеть. Хотя в виртуальной сети можно развернуть множество ресурсов Azure, ресурсы для некоторых служб PaaS Azure развернуть невозможно. Тем не менее вы все же можете ограничить доступ к ресурсам некоторых служб PaaS в Azure трафиком только из подсети виртуальной сети. Перейдите к следующей статье, чтобы узнать, как ограничить сетевой доступ к ресурсам PaaS Azure.

> [!div class="nextstepaction"]
> [Настройка конечных точек служб виртуальной сети](virtual-network-service-endpoints-configure.md#azure-powershell)