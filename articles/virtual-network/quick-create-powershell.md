---
title: "Создание виртуальной сети в Azure с помощью PowerShell | Документация Майкрософт"
description: "Быстро узнайте, как создать виртуальную сеть с помощью PowerShell. Виртуальная сеть позволяет ресурсам Azure множества типов взаимодействовать друг с другом в частном порядке."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 091e7e6cabf325cdd9d4289e7d22e71c583d91db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Создание виртуальной сети с помощью PowerShell

В этой статье вы узнаете, как создать виртуальную сеть. После создания виртуальной сети в ней развертываются две виртуальные машины, которые взаимодействуют друг с другом в частном порядке.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится модуль Azure PowerShell 5.1.1 или более поздней версии. Выполните командлет ` Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*. Все ресурсы Azure создаются в расположении Azure (или регионе).

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть по умолчанию *myVirtualNetwork* в расположении *EastUS*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

Каждой виртуальной сети назначается один или несколько префиксов адреса. Диапазон адресов указывается в нотации CIDR. Диапазон адресов 10.0.0.0/24 охватывает адреса 10.0.0.0–10.0.0.254. Виртуальные сети могут содержать подсети. Ресурсы развертываются в подсети виртуальной сети. 

Создайте конфигурацию подсети с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Все подсети имеют префикс адреса, содержащийся в префиксе адреса виртуальной сети. В этом примере создается конфигурация подсети с таким же префиксом адреса, как и у виртуальной сети.

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Хотя префикс адреса подсети охватывает адреса 10.0.0.0–10.0.0.254, доступны только адреса 10.0.0.4–10.0.0.254, так как Azure резервирует первые четыре адреса (0–3) и последний адрес в каждой подсети. Так как префикс адреса подсети совпадает с префиксом адреса виртуальной сети, в этой виртуальной сети может существовать только одна подсеть.

Запишите конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), который создает подсеть.

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Виртуальная сеть позволяет ресурсам Azure разных типов взаимодействовать друг с другом в частном порядке. Одним из таких ресурсов, который можно развернуть в виртуальной сети, является виртуальная машина. Создайте две виртуальные машины в виртуальной сети, чтобы позже можно было проверить и понять, как работает связь между виртуальными машинами в виртуальной сети.

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). При выполнении этого шага будут запрошены учетные данные. В качестве вводимых значений указываются имя пользователя и пароль для виртуальной машины. Расположение, в котором создается виртуальная машина, должно совпадать с расположением виртуальной сети. Виртуальная машина не обязательно должна быть в той же группе ресурсов, что и виртуальная сеть, хотя в данной статье это так. Параметр `-AsJob` позволяет выполнить команду в фоновом режиме, поэтому можно перейти к следующей задаче.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Будут получены выходные данные, аналогичные приведенным в следующем примере, и Azure начнет создание виртуальной машины в фоновом режиме.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

Azure DHCP автоматически назначит адрес 10.0.0.4 виртуальной машине во время ее создания, так как это первый доступный адрес в подсети *default*.

Создайте вторую виртуальную машину. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
Создание виртуальной машины занимает несколько минут. После создания Azure возвращает выходные данные о созданной виртуальной машине. Хотя этого нет в полученных выходных данных, платформа Azure назначила адрес *10.0.0.5* виртуальной машине *myVm2*, так как это был следующий доступный адрес в подсети.

## <a name="connect-to-a-virtual-machine"></a>Подключение к виртуальной машине

Получите общедоступный IP-адрес виртуальной машины, выполнив командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). По умолчанию Azure назначает общедоступный IP-адрес с маршрутизацией через Интернет каждой виртуальной машине. Виртуальной машине назначается общедоступный IP-адрес из [пула адресов, назначенных каждому региону Azure](https://www.microsoft.com/download/details.aspx?id=41653). Хотя Azure знает, какой общедоступный IP-адрес назначен виртуальной машине, операционная система на этой виртуальной машине не знает его. Приведенный ниже пример возвращает общедоступный IP-адрес виртуальной машины *myVm1*.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

На вашем локальном компьютере выполните следующую команду для создания сеанса удаленного рабочего стола с виртуальной машиной *myVm1*. Замените `<publicIpAddress>` IP-адресом, полученным от предыдущей команды.

```
mstsc /v:<publicIpAddress>
```

Будет создан файл протокола удаленного рабочего стола (RDP-файл). Он будет скачан на ваш компьютер и открыт. Введите имя пользователя и пароль, указанные при создании виртуальной машины, и нажмите кнопку **ОК**. При входе в систему может появиться предупреждение о сертификате. Чтобы продолжить процесс подключения, нажмите кнопку **Да** или **Продолжить**.

## <a name="validate-communication"></a>Проверка связи

Попытка проверить связь с виртуальной машиной Windows завершится неудачей, так как проверка связи запрещена в брандмауэре Windows по умолчанию. Чтобы разрешить проверку связи с *myVm1*, введите следующую команду в командной строке.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Чтобы проверить связь с *myVm2*, введите следующую команду в командной строке виртуальной машины *myVm1*. Укажите учетные данные, использованные при создании виртуальной машины, а затем выполните подключение.

```
mstsc /v:myVm2
```

Подключение к удаленному рабочему столу будет успешно установлено, так как обе виртуальные машины используют частные IP-адреса, назначенные из подсети *default*, и удаленный рабочий стол открыт в брандмауэре Windows по умолчанию. Вы можете подключиться к *myVm2* по имени узла, так как Azure автоматически обеспечивает разрешение DNS-имен для всех узлов в виртуальной сети. В командной строке проверьте связь с *myVm1* из *myVm2*.

```
ping myvm1
```

Проверка связи будет выполнена успешно, так как вы разрешили ее в брандмауэре Windows на виртуальной машине *myVm1* на предыдущем шаге. Чтобы подтвердить возможность исходящих подключений к Интернету, введите следующую команду.

```
ping bing.com
```

Вы получите четыре ответа с сайта bing.com. По умолчанию любая виртуальная машина в виртуальной сети может устанавливать исходящие подключения к Интернету.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив командлет [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Завершите сеанс удаленного рабочего стола, а затем выполните приведенную ниже команду на своем компьютере, чтобы удалить группу ресурсов.

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы развернули виртуальную сеть по умолчанию с одной подсетью и двумя виртуальными машинами. Чтобы узнать, как создать настраиваемую виртуальную сеть с несколькими подсетями и выполнить основные задачи управления виртуальной сетью, перейдите к изучению руководства по созданию настраиваемой виртуальной сети и управлению ею.


> [!div class="nextstepaction"]
> [Создание виртуальной сети с несколькими подсетями](virtual-networks-create-vnet-arm-pportal.md#powershell)
