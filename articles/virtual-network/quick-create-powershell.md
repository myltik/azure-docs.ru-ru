---
title: Краткое руководство. Создание виртуальной сети с помощью Azure PowerShell | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как создать виртуальную сеть с помощью портала Azure. Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными в частном порядке и взаимодействовать через Интернет.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: b8b67b235b54fb5bde738ed5cc1605e08d182a69
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Краткое руководство. Создание виртуальной сети с помощью PowerShell

Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными друг с другом в частном порядке и взаимодействовать через Интернет. Из этого краткого руководства вы узнаете, как создать виртуальную сеть. Создав виртуальную сеть, разверните в ней две виртуальные машины. Затем вы подключитесь к одной виртуальной машине из Интернета и установите частную связь между двумя виртуальными машинами.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль AzureRM PowerShell 5.4.1 или более поздней версии. Выполните командлет ` Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Перед созданием виртуальной сети необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть по умолчанию *myVirtualNetwork* в расположении *EastUS*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Ресурсы Azure развертываются в подсети виртуальной сети, поэтому необходимо создать подсеть. Создайте конфигурацию подсети с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Запишите конфигурацию подсети в виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), который создает подсеть в пределах виртуальной сети:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте две виртуальные машины в виртуальной сети.

### <a name="create-the-first-vm"></a>Создание первой виртуальной машины

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). При выполнении следующей команды будут запрошены учетные данные. В качестве вводимых значений указываются имя пользователя и пароль для виртуальной машины. Параметр `-AsJob` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу.

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

### <a name="create-the-second-vm"></a>Создание второй виртуальной машины 

Введите следующую команду:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Создание виртуальной машины занимает несколько минут. Не переходите к следующему шагу, пока не будет выполнена предыдущая команда и выходные данные не будут возвращены в PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Чтобы получить общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Приведенный ниже пример возвращает общедоступный IP-адрес виртуальной машины *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Замените `<publicIpAddress>` в следующей команде общедоступным IP-адресом, возвращенным предыдущей командой, а затем введите следующую команду: 

```
mstsc /v:<publicIpAddress>
```

Будет создан и скачан на ваш компьютер файл протокола удаленного рабочего стола (RDP-файл). Откройте этот RDP-файл. При появлении запроса выберите **Подключиться**. Введите имя пользователя и пароль, указанные при создании виртуальной машины. Возможно, потребуется выбрать **More choices** (Дополнительные варианты), а затем **Use a different account** (Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины. Нажмите кнопку **ОК**. При входе в систему может появиться предупреждение о сертификате. Если вы получили предупреждение, выберите **Да** или **Продолжить**.

## <a name="communicate-between-vms"></a>Взаимодействие между виртуальными машинами

В PowerShell на виртуальной машине *myVm1* введите `ping myvm2`. Проверка связи завершается ошибкой, так как в ней используется протокол ICMP, который по умолчанию запрещен брандмауэром Windows.

Чтобы разрешить *myVm2* проверить связь с *myVm1* на дальнейшем этапе, введите в PowerShell следующую команду, которая разрешит входящий трафик ICMP через брандмауэр Windows:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Закройте подключение к удаленному рабочему столу *myVm1*. 

Выполните повторно шаги из раздела [Подключение к виртуальной машине из Интернета](#connect-to-a-vm-from-the-internet), но теперь подключитесь к *myVm2*. 

В командной строке на виртуальной машине *myVm2* введите `ping myvm1`.

Вы получите ответы от *myVm1*, так как на предыдущем шаге разрешили использовать ICMP через брандмауэр Windows на виртуальной машине *myVm1*.

Закройте подключение к удаленному рабочему столу *myVm2*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

Следуя инструкциям в этом кратком руководстве, вы создали виртуальную сеть по умолчанию и две виртуальные машины. Затем вы подключились к одной виртуальной машине из Интернета и установили частную связь между двумя виртуальными машинами. Дополнительные сведения о параметрах виртуальной сети см. в статье об [управлении виртуальной сетью](manage-virtual-network.md). 

По умолчанию Azure не накладывает ограничения на частную связь между виртуальными машинами, но разрешает только входящие подключения к удаленному рабочему столу виртуальных машин Windows из Интернета. Чтобы узнать, как разрешить или ограничить различные типы сетевого взаимодействия с виртуальными машинами в обоих направлениях, перейдите к руководству по [фильтрации сетевого трафика](tutorial-filter-network-traffic.md).
