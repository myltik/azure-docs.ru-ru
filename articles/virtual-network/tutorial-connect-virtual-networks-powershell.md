---
title: Установка пирингового подключения между виртуальными сетями — PowerShell | Документация Майкрософт
description: Сведения о том, как установить пиринговое подключение между виртуальными сетями.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Установка пирингового подключения между виртуальными сетями с помощью PowerShell

Виртуальные сети можно подключить друг к другу с помощью пиринговой связи. Пиринговая связь виртуальных сетей обеспечивает обмен данными между их ресурсами с такими же задержкой и пропускной способностью, как если бы эти ресурсы находились в одной виртуальной сети. В этой статье описано, как создать две виртуальные сети и пиринговую связь между ними. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание двух виртуальных сетей;
> * создание пиринговой связи между виртуальными сетями;
> * тестирование пиринговой связи.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этой статьей, вам понадобится модуль Azure PowerShell 3.6 или более поздней версии. Выполните командлет ` Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="create-virtual-networks"></a>Создание виртуальных сетей

Перед созданием виртуальной сети необходимо создать для нее группу ресурсов и другие компоненты, указанные в этой статье. Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть с именем *myVirtualNetwork1* и префиксом адреса *10.0.0.0/16*:

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Создайте конфигурацию подсети с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). В следующем примере создается конфигурация подсети с префиксом адреса 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Запишите конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), который создает подсеть.

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Создайте виртуальную сеть с префиксом адреса 10.1.0.0/16 и одной подсетью:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

Префикс адреса виртуальной сети *myVirtualNetwork2* не пересекается с префиксом адреса виртуальной сети *myVirtualNetwork1*. Пиринговую связь нельзя установить между виртуальными сетями с пересекающимися префиксами адресов.

## <a name="peer-virtual-networks"></a>Установка пиринговой связи между виртуальными сетями

Создайте пиринг с помощью командлета [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). В следующем примере создается пиринговая связь между сетями *myVirtualNetwork1* и *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

В выходных данных, возвращенных после выполнения предыдущей команды, для **состояния пиринга** будет отображаться значение *Инициировано*. Состояние *Инициировано* не изменится, пока не будет создан пиринг сети *myVirtualNetwork2* с сетью *myVirtualNetwork1*. Установите пиринговую связь сети *myVirtualNetwork2* с сетью *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

В выходных данных, возвращенных после выполнения предыдущей команды, для **состояния пиринга** будет отображаться значение *Подключено*. Кроме того, в Azure состояние пиринга *myVirtualNetwork1-myVirtualNetwork2* изменяется на *Подключено*. Убедитесь, что состояние пиринга для *myVirtualNetwork1 myVirtualNetwork2* изменено на *Подключено*, с помощью командлета [Get AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Ресурсы одной виртуальной сети не могут взаимодействовать с ресурсами другой виртуальной сети, пока **состояние пиринга** для обеих виртуальных сетей не будет иметь значение *Подключено*. 

Пиринги устанавливаются между двумя виртуальными сетями, однако не являются транзитивными. Например, если также требуется установить пиринговую связь сети *myVirtualNetwork2* с сетью *myVirtualNetwork3*, необходимо создать дополнительный пиринг между виртуальными сетями *myVirtualNetwork2* и *myVirtualNetwork3*. Между *myVirtualNetwork1* и *myVirtualNetwork2* установлен пиринг, но ресурсы в *myVirtualNetwork1* имеют доступ только к ресурсам *myVirtualNetwork3*, если для *myVirtualNetwork1* также установлен пиринг с *myVirtualNetwork3*. 

Перед установкой пиринговой связи между рабочими виртуальными сетями рекомендуем внимательно ознакомиться с [общими сведениями о пиринге](virtual-network-peering-overview.md), статьей об [управлением пирингом](virtual-network-manage-peering.md) и разделом об [ограничениях виртуальных сетей](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). В этой статье рассматривается пиринг между двумя виртуальными сетями в одних и тех же подписке и расположении, но также можно установить пиринговую связь между виртуальными сетями в [разных регионах](#register) и [разных подписках Azure](create-peering-different-subscriptions.md#powershell). Также с помощью пиринга можно создавать [звездообразные сетевые схемы](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering).

## <a name="test-peering"></a>Тестирование пиринговой связи

Чтобы протестировать взаимодействие между виртуальными машинами в разных виртуальных сетях посредством пиринговой связи, разверните виртуальную машину в каждой подсети, а затем выполните обмен данными между виртуальными машинами. 

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте по одной виртуальной машине в каждой виртуальной сети, чтобы в дальнейшем можно было проверить взаимодействие между ними.

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В приведенном ниже примере в виртуальной сети *myVirtualNetwork1* создается виртуальная машина с именем *myVm1*. Параметр `-AsJob` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу. Когда появится запрос, введите имя пользователя и пароль, которые вы будете использовать для входа в виртуальную машину.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

Azure автоматически назначает виртуальной машине частный IP-адрес 10.0.0.4, так как это первый доступный IP-адрес в подсети *Subnet1* виртуальной сети *myVirtualNetwork1*. 

Создайте виртуальную машину в виртуальной сети *myVirtualNetwork2*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Создание виртуальной машины занимает несколько минут. Хотя этих сведений нет в выходных данных, в Azure виртуальной машине назначен частный IP-адрес 10.1.0.4, так как 10.1.0.4 — это первый доступный IP-адрес в подсети *Subnet1* виртуальной сети *myVirtualNetwork2*. 

Не выполняйте следующие шаги, пока в Azure не будет создана виртуальная машина и не будут возвращены выходные данные в PowerShell.

### <a name="test-virtual-machine-communication"></a>Тестирование обмена данными между виртуальными машинами

Вы можете подключиться к общедоступному IP-адресу виртуальной машины через Интернет. Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Приведенный ниже пример возвращает общедоступный IP-адрес виртуальной машины *myVm1*.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

На вашем локальном компьютере выполните следующую команду для создания сеанса удаленного рабочего стола с виртуальной машиной *myVm1*. Замените `<publicIpAddress>` IP-адресом, полученным от предыдущей команды.

```
mstsc /v:<publicIpAddress>
```

Будет создан файл протокола удаленного рабочего стола (RDP-файл). Он будет скачан на ваш компьютер и открыт. Введите имя пользователя и пароль, а затем нажмите кнопку **ОК**. Возможно, понадобится выбрать **More choices** (Дополнительные варианты) и **Use a different account** (Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины. При входе в систему может появиться предупреждение о сертификате. Чтобы продолжить процесс подключения, нажмите кнопку **Да** или **Продолжить**.

Из командной строки включите проверку связи через брандмауэр Windows, чтобы позднее проверить связь между этой виртуальной машиной и виртуальной машиной *myVm2*.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Хотя проверка связи используется в этой статье для тестирования, не рекомендуем разрешать протокол ICMP через брандмауэр Windows в рабочей среде.

Для подключения к виртуальной машине *myVm2* введите следующую команду в командной строке виртуальной машины *myVm1*:

```
mstsc /v:10.1.0.4
```

Так как вы включили проверку связи на виртуальной машине *myVm1*, теперь можно проверить связь с ней с помощью IP-адреса из командной строки на виртуальной машине *myVm2*:

```
ping 10.0.0.4
```

Вы получите четыре ответа. Если проверить связь по имени виртуальной машины (*myVm1*) вместо ее IP-адреса, команда завершится ошибкой, поскольку *myVm1* — неизвестное имя узла. Разрешение имен Azure по умолчанию работает между виртуальными машинами в одной виртуальной сети, но не между виртуальными машинами в разных виртуальных сетях. Для разрешения имен между виртуальными сетями необходимо [развернуть собственный DNS-сервер](virtual-networks-name-resolution-for-vms-and-role-instances.md) или использовать [частные домены Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Отключите RDP-сеансы с *myVm1* и *myVm2*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив командлет [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>Регистрация для использования предварительной версии функции пиринга между виртуальными сетями в разных регионах**

Функция создания пиринговых связей между виртуальными сетями в одном регионе является общедоступной. Функция пиринга между виртуальными сетями в разных регионах сейчас находится на этапе предварительной версии. Доступные регионы перечислены в разделе [Обновления Azure](https://azure.microsoft.com/updates/?product=virtual-network). Чтобы создать пиринг между виртуальными сетями в разных регионах, сначала нужно зарегистрироваться для использования предварительной версии, выполнив описанные ниже действия (в подписке каждой виртуальной сети, для которой требуется пиринг).

1. Зарегистрируйте подписку, в которой находится каждая виртуальная сеть, для которой нужно создать пиринг, чтобы использовать предварительную версию, введя следующие команды:

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. Убедитесь, что регистрация прошла успешно, выполнив следующую команду:

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Если вы попытаетесь установить пиринговую связь между виртуальными сетями в разных регионах до того, как для **состояния регистрации** в выходных данных предыдущей команды будет остановлено значение **Зарегистрирована** для обеих подписок, пиринговая связь не будет установлена.

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как соединить две виртуальные сети с помощью пиринга. Вы можете [подключить свой компьютер к виртуальной сети](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) через VPN и взаимодействовать с ресурсами в виртуальной сети или в виртуальных сетях, соединенных пиринговой связью.

Перейдите к примерам скриптов для многократного использования, с помощью которых можно выполнять многие задачи, описанные в статьях о виртуальных сетях.

> [!div class="nextstepaction"]
> [Примеры скриптов для виртуальных сетей](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
