---
title: "Открытие портов для виртуальной машины с помощью Azure PowerShell | Документация Майкрософт"
description: "Узнайте, как открыть порт или создать конечную точку для виртуальной машины Windows, используя модель развертывания с помощью Azure Resource Manager и Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 91c2e6f53f8ba490d2943a60c7ad519d856707b9
ms.openlocfilehash: 0d57a27c0862b09e2628520a1e50a0cc56a55881


---
# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Открытие портов и конечных точек для виртуальной машины в Azure с помощью PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Быстрые команды
Для создания группы безопасности сети и правил ACL потребуется [последняя версия Azure PowerShell](/powershell/azureps-cmdlets-docs). Эти действия можно также [выполнить с помощью портала Azure](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Войдите в свою учетную запись Azure.

```powershell
Login-AzureRmAccount
```

В следующих примерах замените имена параметров собственными значениями. Используемые имена параметров — `myResourceGroup`, `myNetworkSecurityGroup`, и `myVnet`.

Создайте правило. В следующем примере создается правило `myNetworkSecurityGroupRule`. Это правило разрешает TCP-трафик через порт 80.

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Затем создайте группу безопасности сети и назначьте только что созданное правило HTTP, как показано ниже. В следующем примере создается группа безопасности сети `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Теперь давайте назначим подсети вашу группу безопасности сети. В следующем примере переменной `$vnet` назначается существующая виртуальная сеть `myVnet` :

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Свяжите группу безопасности сети с подсетью. В следующем примере подсеть `mySubnet` связывается с группой безопасности сети:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Наконец, обновите виртуальную сеть, чтобы изменения вступили в действие.

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Дополнительная информация о группах безопасности сети
Приведенные здесь быстрые команды позволят настроить трафик, поступающий в виртуальную машину. Группы безопасности сети предоставляют множество полезных функций и всевозможные настройки для управления доступом к ресурсам. [Здесь](../virtual-network/virtual-networks-create-nsg-arm-ps.md)вы можете больше прочитать о создании группы безопасности сети и правил ACL.

Группы безопасности сети и правила ACL можно также определять в шаблонах Azure Resource Manager. Узнайте больше о [создании групп безопасности сети с помощью шаблонов](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Если необходимо использовать перенаправление портов, чтобы сопоставить уникальный внешний порт с внутренним портом на своей виртуальной машине, то воспользуйтесь балансировщиком нагрузки и правилами преобразования сетевых адресов (NAT). Например, может потребоваться открыть TCP-порт 8080 для доступа извне и направить трафик на TCP-порт 80 виртуальной машины. Вы можете узнать о [создании балансировщика нагрузки с выходом в Интернет](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Дальнейшие действия
В этом примере создано простое правило, разрешающее трафик HTTP. Информацию о создании более детализированных сред можно найти в следующих статьях.

* [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md)
* [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](../load-balancer/load-balancer-arm.md)




<!--HONumber=Feb17_HO2-->


