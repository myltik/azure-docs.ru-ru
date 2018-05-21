---
title: Краткое руководство. Диагностика проблемы с фильтром трафика на виртуальной машине с помощью Azure PowerShell | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как диагностировать проблему с фильтром трафика на виртуальной машине с помощью функции для проверки IP-потока в Наблюдателе за сетями Azure.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: d98a804961defc80bebe3e3a838dd229c23044bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Краткое руководство. Диагностика проблемы с фильтром трафика на виртуальной машине с помощью Azure PowerShell

С помощью этого краткого руководства вы развернете виртуальную машину (VM) и проверите доступ к IP-адресу и URL-адресу, а также доступ с IP-адреса. Затем вы определите причину проблемы с подключением и найдете способ ее устранения.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль AzureRM PowerShell 5.4.1 или более поздней версии. Выполните командлет ` Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-a-vm"></a>Создание виртуальной машины

Перед созданием виртуальной машины необходимо создать группу ресурсов, которая будет содержать эту виртуальную машину. Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). При выполнении этого шага будут запрошены учетные данные. В качестве вводимых значений указываются имя пользователя и пароль для виртуальной машины.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Создание виртуальной машины занимает несколько минут. Не приступайте к дальнейшим инструкциям, пока не завершится создание виртуальной машины и PowerShell не вернет выходные данные.

## <a name="test-network-communication"></a>Тестирование взаимодействия по сети

Чтобы проверить сетевое подключение с помощью Наблюдателя за сетями, сначала включите Наблюдатель за сетями в регионе, где размещается тестируемая виртуальная машина. Затем примените в Наблюдателе за сетями функцию проверки IP-потока.

### <a name="enable-network-watcher"></a>Включение Наблюдателя за сетями

Если Наблюдатель за сетями уже включен в регионе "Восточная часть США", получите его экземпляр с помощью командлета [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). В следующем примере извлекается существующий Наблюдатель за сетями с именем *NetworkWatcher_eastus*, размещенный в группе ресурсов *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Если Наблюдатель за сетями еще не включен в регионе "Восточная часть США", создайте его с помощью командлета [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher):

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-ip-flow-verify"></a>Применение проверки IP-потока

При создании виртуальной машины Azure применяет к ней стандартные правила разрешений и запретов трафика. Позднее вы можете переопределить эти значения по умолчанию, чтобы разрешить или запретить дополнительные типы трафика. Чтобы проверить, разрешено или запрещено поступление трафика в определенные назначения с исходного IP-адреса, используйте команду [Test-AzureRmNetworkWatcherIPFlow](/powershell/module/azurerm.network/test-azurermnetworkwatcheripflow).

Проверьте исходящее подключение виртуальной машины по любому из IP-адресов сайта www.bing.com.

```azurepowershell-interactive
Test-AzureRmNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Через несколько секунд вы получите результат, свидетельствующий о том, что такой доступ разрешен правилом безопасности с именем **AllowInternetOutbound**.

Проверка исходящего подключения виртуальной машины по адресу 172.31.0.100:

```azurepowershell-interactive
Test-AzureRmNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

Результат свидетельствует о том, что такой доступ запрещен правилом безопасности с именем **DefaultOutboundDenyAll**.

Проверка исходящего подключения с адреса 172.31.0.100 к виртуальной машине:

```azurepowershell-interactive
Test-AzureRmNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

Результат свидетельствует о том, что такой доступ запрещен правилом безопасности с именем **DefaultInboundDenyAll**. Теперь вы знаете, какие правила безопасности разрешают или запрещают трафик к виртуальной машине или от нее. Это поможет вам решить проблему.

## <a name="view-details-of-a-security-rule"></a>Просмотр сведений о правиле безопасности

Чтобы определить, почему в разделе [проверки сетевой связи](#test-network-communication) мы получили такие ответы о разрешении и (или) запрете доступа, изучите правила безопасности для сетевого интерфейса с помощью командлета [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup):

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

Возвращаемые данные содержат следующий текст для правила **AllowInternetOutbound**, которое разрешало исходящий доступ к www.bing.com в разделе [проверки IP-потока](#use-ip-flow-verify):

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

Этих выходные данные свидетельствуют о том, что параметр **DestinationAddressPrefix** имеет значение **Internet**. Не совсем ясно, почему примененный в разделе [проверки IP-потока](#use-ip-flow-verify) адрес 13.107.21.200 относится к категории **Internet**. Рядом в разделе **ExpandedDestinationAddressPrefix** перечислены несколько префиксов адресов. Один из этих префиксов имеет значение **12.0.0.0/6**, которое охватывает диапазон IP-адресов с 12.0.0.1 по 15.255.255.254. Так как 13.107.21.200 попадает в этот диапазон адресов, правило **AllowInternetOutBound** разрешает исходящий трафик. Кроме того, в выходных данных `Get-AzureRmEffectiveNetworkSecurityGroup` нет правил с более высоким **приоритетом** (чем меньше это число, тем выше приоритет), которые переопределяют указанное выше правило. Чтобы запретить исходящий трафик по адресу 13.107.21.200, вы можете добавить правило безопасности с более высоким приоритетом, которое запрещает исходящие подключения по этому IP-адресу через порт 80.

Когда вы выполняли команду `Test-AzureRmNetworkWatcherIPFlow`, чтобы проверить исходящее подключение по адресу 172.131.0.100 в разделе [проверки IP-потока](#use-ip-flow-verify), выходные данные указывали на то, что правило **DefaultOutboundDenyAll** запрещает такую связь. Правило **DefaultOutboundDenyAll** аналогично правилу **DenyAllOutBound**, которое указано в следующих выходных данных команды `Get-AzureRmEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

Это правило содержит значение **0.0.0.0/0** для параметра **DestinationAddressPrefix**. Такое правило запрещает исходящие подключения по адресу 172.131.0.100, так как он не входит в диапазон **DestinationAddressPrefix** любого из правил для исходящего трафика,перечисленных в выходных данных команды `Get-AzureRmEffectiveNetworkSecurityGroup`. Чтобы разрешить исходящий трафик, вы можете добавить правило безопасности с более высоким приоритетом, которое разрешает исходящие подключения по IP-адресу 172.131.0.100 через порт 80.

Когда вы выполняли команду `Test-AzureRmNetworkWatcherIPFlow`, чтобы проверить входящее подключение с адреса 172.131.0.100 в разделе [проверки IP-потока](#use-ip-flow-verify), выходные данные указывали на то, что правило **DefaultInboundDenyAll** запрещает такую связь. Правило **DefaultInboundDenyAll** аналогично правилу **DenyAllInBound**, которое указано в следующих выходных данных команды `Get-AzureRmEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

Правило **DenyAllInBound** применяется, так как в выходных данных команды `Get-AzureRmEffectiveNetworkSecurityGroup` нет правил с более высоким приоритетом, которые разрешают входящие подключения к виртуальной машине с адреса 172.131.0.100 через порт 80. Чтобы разрешить входящий трафик, вы можете добавить правило безопасности с более высоким приоритетом, которое разрешает входящий трафик на порт 80 с адреса 172.131.0.100.

Проверки в этом кратком руководстве предназначены для тестирования конфигурации Azure. Если все эти проверки возвращают ожидаемые результаты, но сетевые проблемы при этом сохраняются, проверьте, нет ли брандмауэров между этой виртуальной машиной и конечной точкой, с которой вы взаимодействуете. Кроме того, проверьте, нет ли брандмауэра в операционной системе самой виртуальной машины. Такие брандмауэры также могут разрешать или запрещать обмен данными.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали виртуальную машину и проверили работу фильтров входящего и исходящего трафика. Вы узнали, что правила групп безопасности сети могут разрешать или запрещать исходящий и входящий трафик виртуальной машины. Изучите дополнительные сведения о [правилах безопасности](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) и [создании правил безопасности](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Даже если фильтры трафика правильно настроены, обмен данными с виртуальной машиной может завершиться сбоем из-за настроек маршрутизации. Сведения о диагностике проблем с маршрутизацией в сети виртуальной машины вы найдете [здесь](diagnose-vm-network-routing-problem-powershell.md). Единое средство для диагностики проблем с исходящей маршрутизацией, задержками и фильтрацией описано в статье об [устранении неполадок с подключением](network-watcher-connectivity-powershell.md).