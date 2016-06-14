<properties
   pageTitle="Разрешение внешнего доступа к виртуальной машине с помощью PowerShell | Microsoft Azure"
   description="Узнайте, как открыть порт или создать конечную точку, которая обеспечивает внешний доступ к виртуальной машине Windows, используя модель развертывания с помощью Resource Manager и Azure PowerShell."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="05/24/2016"
   ms.author="iainfou"/>

# Разрешение внешнего доступа к виртуальной машине с помощью PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Быстрые команды
Для создания группы безопасности сети и правил ACL потребуется [последняя версия Azure PowerShell](../powershell-install-configure.md). Эти действия также можно [выполнить с помощью портала Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Сначала нужно создать правило, пропускающее трафик HTTP через TCP-порт 80, как показано ниже, введя собственное имя и описание.

```
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name http-rule -Description "Allow HTTP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Затем создайте группу безопасности сети и назначьте только что созданное правило HTTP, как показано ниже, введя имя группы ресурсов и расположение.

```
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus 
    -Name "TestNSG" -SecurityRules $httprule
```

Теперь давайте назначим вашу группу безопасности сети в подсеть. Сначала выберите виртуальную сеть следующим образом.

```
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
```

Свяжите группу безопасности сети с подсетью, как показано ниже.

```
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet `
    -NetworkSecurityGroup $nsg
```

Наконец, обновите виртуальную сеть, чтобы изменения вступили в действие.

```
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## Дополнительная информация о группах безопасности сети
Приведенные здесь быстрые команды позволят настроить трафик, поступающий в виртуальную машину. Группы безопасности сети предоставляют множество полезных функций и степеней детализации для управления доступом к ресурсам. [Здесь](../virtual-network/virtual-networks-create-nsg-arm-ps.md) вы можете больше прочитать о создании группы безопасности сети и правил ACL.

Группы безопасности сети и правила ACL можно также определить в шаблонах Azure Resource Manager. Узнайте больше о [создании групп безопасности сети с помощью шаблонов](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Если необходимо использовать перенаправление портов, чтобы сопоставить уникальный внешний порт с внутренним портом на своей виртуальной машине, нужно воспользоваться балансировщиком нагрузки и правилами преобразования сетевых адресов (NAT). Например, может потребоваться открыть TCP-порт 8080 для доступа извне и направить трафик на TCP-порт 80 виртуальной машины. Вы можете узнать о [создании балансировщика нагрузки с выходом в Интернет](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## Дальнейшие действия
В этом примере создано простое правило, разрешающее трафик HTTP. Информацию о создании более детализированных сред можно найти в следующих статьях.

- [Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md)
- [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md)
- [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0601_2016-->