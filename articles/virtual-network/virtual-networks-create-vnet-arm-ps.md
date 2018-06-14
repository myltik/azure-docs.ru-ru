---
title: Создание виртуальной сети Azure с несколькими подсетями с помощью PowerShell | Документация Майкрософт
description: Узнайте, как создать виртуальную сеть с несколькими подсетями с помощью PowerShell.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880494"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Создание виртуальной сети с несколькими подсетями с помощью PowerShell

Виртуальная сеть позволяет разным типам ресурсов Azure обмениваться данными друг с другом в частном порядке и взаимодействовать через Интернет. Создание нескольких подсетей в виртуальной сети помогает сегментировать сеть, чтобы вы могли фильтровать потоки трафика между подсетями и управлять ими. В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Создать виртуальную сеть
> * Создание подсети
> * Тестирование взаимодействия по сети между виртуальными машинами

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится модуль Azure PowerShell 3.6 или более поздней версии. Выполните командлет ` Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть с именем *myVirtualNetwork* и префиксом адреса *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

**AddressPrefix** указывается в нотации CIDR. Указанный префикс адреса включает IP-адреса 10.0.0.0–10.0.255.254.

## <a name="create-a-subnet"></a>Создание подсети

При создании подсети сначала создается ее конфигурация, которая затем обновляется в виртуальной сети. Создайте конфигурацию подсети с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). В следующем примере создается две конфигурации подсети: для *общей* и *частной* подсети.

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

Значение параметра **AddressPrefix**, заданное для подсети, должно быть в пределах префикса адреса, определенного для виртуальной сети. Azure DHCP назначает ресурсам, развертываемым в подсети, IP-адреса на основе префикса адреса подсети. Azure назначает ресурсам, развертываемым в **общей** подсети, только адреса 10.0.0.4–10.0.0.254, так как первые четыре адреса (10.0.0.0–10.0.0.3 для подсети в этом примере) и последний адрес (в этом примере — 10.0.0.255) зарезервированы в каждой подсети.

Запишите конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), который создает подсети в виртуальной сети:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Прежде чем развертывать виртуальные сети и подсети Azure для использования в рабочей среде, мы рекомендуем внимательно ознакомиться с [рекомендациями по использованию адресного пространства](manage-virtual-network.md#create-a-virtual-network) и [ограничениями виртуальной сети](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). После развертывания ресурсов в подсетях для некоторых изменений виртуальной сети и подсети, например изменений диапазонов адресов, может потребоваться повторно развернуть существующие ресурсы Azure, которые были развернуты в подсетях.

## <a name="test-network-communication"></a>Тестирование взаимодействия по сети

Виртуальная сеть позволяет разным типам ресурсов Azure обмениваться данными друг с другом в частном порядке и взаимодействовать через Интернет. Одним из таких ресурсов, который можно развернуть в виртуальной сети, является виртуальная машина. Создайте в виртуальной сети две виртуальные машины, чтобы в дальнейшем можно было проверить обмен данными между ними по сети и через Интернет. 

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создается виртуальная машина с именем *myVmWeb* в *общей* подсети в виртуальной сети *myVirtualNetwork*. Параметр `-AsJob` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу. Когда появится запрос, введите имя пользователя и пароль, которые вы будете использовать для входа в виртуальную машину.

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

Azure автоматически назначает виртуальной машине частный IP-адрес 10.0.0.4, так как это первый доступный IP-адрес в *общей* подсети. 

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

Создание виртуальной машины занимает несколько минут. Хотя это не указано в выходных данных, в Azure виртуальной машине назначен частный IP-адрес 10.0.0.4, так как это первый доступный IP-адрес в *частной* подсети виртуальной сети *myVirtualNetwork*. 

Не переходите к остальным шагам, пока не создастся виртуальная машина и PowerShell не вернет выходные данные.

Виртуальные машины, создаваемые в этой статье, имеют один [сетевой интерфейс](virtual-network-network-interface.md) с одним динамически назначенным IP-адресом. Развернув виртуальную машину, вы можете [добавить несколько общедоступных и частных IP-адресов или изменить способ назначения IP-адреса на статический](virtual-network-network-interface-addresses.md#add-ip-addresses). Вы можете [добавлять сетевые интерфейсы](virtual-network-network-interface-vm.md#vm-add-nic) в количестве, поддерживаемом [размером виртуальной машины](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json), который вы выбрали при ее создании. Вы также можете [включить виртуализацию операций ввода-вывода с единым корнем (SR-IOV)](create-vm-accelerated-networking-powershell.md) для виртуальной машины, но только если размер создаваемой виртуальной машины поддерживает такую возможность.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Обмен данными между виртуальными машинами и через Интернет

Вы можете подключиться к общедоступному IP-адресу виртуальной машины через Интернет. При создании виртуальной машины *myVmMgmt* в Azure ей также создается и назначается общедоступный IP-адрес с именем *myVmMgmt*. Хотя виртуальной машине не обязательно иметь общедоступный IP-адрес, Azure по умолчанию назначает его каждой виртуальной машине, которую вы создаете. Чтобы подключиться к виртуальной машине через Интернет, ей нужно назначить общедоступный IP-адрес. Все виртуальные машины могут устанавливать исходящие подключения к Интернету, независимо от того, назначен ли им общедоступный IP-адрес. См. дополнительные сведения об [исходящих интернет-подключениях в Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). В приведенном ниже примере возвращается общедоступный IP-адрес виртуальной машины *myVmMgmt*.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

На вашем локальном компьютере выполните следующую команду, чтобы создать сеанс удаленного рабочего стола с виртуальной машиной *myVmMgmt*. Замените `<publicIpAddress>` IP-адресом, полученным от предыдущей команды.

```
mstsc /v:<publicIpAddress>
```

Будет создан файл протокола удаленного рабочего стола (RDP-файл). Он будет скачан на ваш компьютер и открыт. Введите имя пользователя и пароль, указанный при создании виртуальной машины, а затем нажмите кнопку **ОК**. (Возможно, понадобится выбрать **Дополнительные варианты** и **Использовать другую учетную запись**, чтобы указать учетные данные, введенные при создании виртуальной машины.) При входе в систему может появиться предупреждение о сертификате. Чтобы продолжить процесс подключения, нажмите кнопку **Да** или **Продолжить**. 

В дальнейшем для обмена данными между виртуальными машинами *myVmMgmt* и *myVmWeb* будет использоваться проверка связи. При проверке связи используется протокол ICMP, который брандамауэр Windows отклоняет по умолчанию. Включите протокол ICMP через брандмауэр Windows, введя в командной строке следующую команду:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Хотя проверка связи используется в этой статье, мы не рекомендуем разрешать протокол ICMP через брандмауэр Windows в рабочей среде.

Часто в целях безопасности ограничивается число виртуальных машин, к которым могут устанавливаться удаленные подключения в виртуальной сети. В этом руководстве виртуальная машина *myVmMgmt* используется для управления виртуальной машиной *myVmWeb* в виртуальной сети. Чтобы подключиться к удаленному рабочему столу виртуальной машины *myVmWeb* из виртуальной машины *myVmMgmt*, выполните следующую команду:

``` 
mstsc /v:myVmWeb
```

Для обмена данными между виртуальными машинами *myVmMgmt* и *myVmWeb*, введите в командной строке следующую команду:

```
ping myvmmgmt
```

Вы получите результат, аналогичный приведенному ниже.
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
Как видите, адрес виртуальной машины *myVmMgmt* — 10.0.1.4. Это первый доступный IP-адрес в диапазоне адресов *частной* подсети, в которой вы развернули виртуальную машину *myVmMgmt* на предыдущем шаге.  Как видите, полное доменное имя виртуальной машины — *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Хотя часть доменного имени *dar5p44cif3ulfq00wxznl3i3f* отличается для виртуальной машины, остальные части одинаковы. По умолчанию все виртуальные машины Azure используют службу Azure DNS. Все виртуальные машины в виртуальной сети могут разрешать имена других виртуальных машин в той же виртуальной сети, используя службу Azure DNS по умолчанию. Вместо этой службы вы можете использовать свой DNS-сервер или возможность размещения частного домена, которую предоставляет служба Azure DNS. Дополнительные сведения см. в разделе [Разрешение имен с помощью собственного DNS-сервера](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) или в статье [Использование Azure DNS для частных доменов](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Чтобы установить службы Internet Information Services (IIS) для Windows Server на виртуальной машине *myVmWeb*, введите следующую команду в сеансе PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Когда установка служб IIS завершится, отключите сеанс удаленного рабочего стола *myVmWeb*, после чего вы останетесь в сеансе удаленного рабочего стола *myVmMgmt*. Откройте веб-браузер и перейдите по адресу http://myvmweb. Появится страница приветствия служб IIS.

Отключите сеанс удаленного рабочего стола *myVmMgmt*.

Получите общедоступный адрес, назначенный виртуальной машине *myVmWeb* в Azure:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

На своем компьютере перейдите по общедоступному IP-адресу виртуальной машины *myVmWeb*. Попытка просмотреть страницу приветствия служб IIS на вашем компьютере завершится ошибкой. Это происходит потому, что при развертывании виртуальной машины в Azure по умолчанию создается группа безопасности сети для каждой виртуальной машины. 

В этой группе содержатся правила безопасности, которые разрешают или запрещают исходящий и входящий сетевой трафик по портам или IP-адресам. Сетевая группа безопасности по умолчанию, созданная Azure, поддерживает взаимодействие между ресурсами через все порты в одной виртуальной сети. Для виртуальных машин Windows группа безопасности сети по умолчанию отклоняет весь входящий трафик из Интернета на всех портах, кроме TCP-порта 3389 (RDP). В результате, по умолчанию вы также можете подключиться по протоколу RDP напрямую к виртуальной машине *myVmWeb* из Интернета, даже если порт 3389 может не быть открытым для веб-сервера. Так как для просмотра веб-страниц используется порт 80, подключение через Интернет завершится сбоем из-за того, что в группе безопасности сети по умолчанию нет такого правила, которое разрешает прохождение трафика через порт 80.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив командлет [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как развернуть виртуальную сеть с несколькими подсетями. Вы также узнали, что при создании виртуальной машины Windows в Azure создается сетевой интерфейс, который подключается к виртуальной машине, и группа безопасности сети, которая разрешает только тот трафик из Интернета, который проходит через порт 3389. Перейдите к следующему руководству, чтобы узнать, как фильтровать сетевой трафик для подсетей, а не отдельных виртуальных машин.

> [!div class="nextstepaction"]
> [Создание групп безопасности сети с помощью Azure CLI](./virtual-networks-create-nsg-arm-ps.md)
