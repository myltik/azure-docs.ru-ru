---
title: Открытие портов для виртуальной машины с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как открыть порт или создать конечную точку для виртуальной машины Windows, используя модель развертывания с помощью Azure Resource Manager и Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: a7564c19f8318d62260d03b92f8115c8f3fa887a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367064"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Как открыть порты и конечные точки для виртуальной машины в Azure с помощью PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Быстрые команды
Для создания группы безопасности сети и правил ACL потребуется [последняя версия Azure PowerShell](/powershell/azureps-cmdlets-docs). Эти действия можно также [выполнить с помощью портала Azure](nsg-quickstart-portal.md).

Войдите в свою учетную запись Azure.

```powershell
Connect-AzureRmAccount
```

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *mystorageaccount* и *myVM*.

Создайте правило с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). В следующем примере создается правило с именем *myNetworkSecurityGroupRule*. Это правило разрешает *TCP*-трафик через порт *80*:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Затем с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) создайте группу безопасности сети и назначьте только что созданное правило HTTP, как показано ниже. В следующем примере создается группа безопасности сети с именем *myNetworkSecurityGroup*.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Теперь давайте назначим подсети вашу группу безопасности сети. В следующем примере с помощью [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) переменной *$vnet* назначается существующая виртуальная сеть *myVnet*:

```powershell
$vnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Свяжите группу безопасности сети с подсетью с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig): В следующем примере подсеть *mySubnet* связывается с группой безопасности сети:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Наконец, с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) обновите виртуальную сеть, чтобы изменения вступили в силу.

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Дополнительная информация о группах безопасности сети
Приведенные здесь быстрые команды позволят настроить трафик, поступающий в виртуальную машину. Группы безопасности сети предоставляют множество полезных функций и всевозможные настройки для управления доступом к ресурсам. [Здесь](tutorial-virtual-network.md#secure-network-traffic)вы можете больше прочитать о создании группы безопасности сети и правил ACL.

Для веб-приложений с высокой доступностью необходимо поместить виртуальную машину за Azure Load Balancer. Балансировщик нагрузки распределяет трафик между виртуальными машинами с группой безопасности сети, обеспечивающей фильтрацию трафика. Подробные сведения см. в статье [Балансировка нагрузки виртуальных машин Windows в Azure для создания высокодоступного приложения](tutorial-load-balancer.md).

## <a name="next-steps"></a>Дополнительная информация
В этом примере создано простое правило, разрешающее трафик HTTP. Информацию о создании более детализированных сред можно найти в следующих статьях.

* [Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Безопасность сети](../../virtual-network/security-overview.md)
* [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](../../load-balancer/load-balancer-arm.md)

