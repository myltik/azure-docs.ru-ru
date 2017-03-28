---
title: "Общие команды PowerShell для виртуальных сетей Azure | Документация Майкрософт"
description: "Общие команды PowerShell, позволяющие приступить к созданию виртуальной сети и связанных с ней ресурсов для виртуальных машин."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 0b1785341380df401f91462e09ea35b1e37c8f0d
ms.lasthandoff: 03/03/2017


---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Общие команды PowerShell для виртуальных сетей Azure

Если вы хотите создать виртуальную машину, необходимо создать [виртуальную сеть](../virtual-network/virtual-networks-overview.md) или иметь данные существующей виртуальной сети, в которую можно добавить виртуальную машину. Обычно при создании виртуальной машины требуется также рассмотреть возможность создания ресурсов, приведенных в этой статье.

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs).

При выполнении нескольких команд, описываемых в этой статье, могут пригодиться некоторые переменные.

- $location — расположение сетевых ресурсов. Можно использовать командлет [Get-AzureRmLocation](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/get-azurermlocation), чтобы найти [географический регион](https://azure.microsoft.com/regions/), который вы используете.
- $myResourceGroup — имя группы ресурсов, в которой находятся сетевые ресурсы.

## <a name="create-network-resources"></a>Создание сетевых ресурсов

| Задача | Команда |
| ---- | ------- |
| Создание конфигураций подсети |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmVirtualNetworkSubnetConfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>В обычной сети может быть подсеть для [балансировщика нагрузки с выходом в Интернет](../load-balancer/load-balancer-internet-overview.md) и отдельная подсеть для [внутреннего балансировщика нагрузки](../load-balancer/load-balancer-internal-overview.md). |
| Создать виртуальную сеть |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmVirtualNetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Проверка доменного имени на уникальность |[Test-AzureRmDnsAvailability](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Test-AzureRmDnsAvailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Вы можете указать DNS-имя домена для [ресурса с общедоступным IP-адресом](../virtual-network/virtual-network-ip-addresses-overview-arm.md). В результате этого будут сопоставлены адрес доменное_имя.расположение.cloudapp.azure.com и общедоступный IP-адрес на DNS-серверах под управлением Azure. Имя может содержать только буквы, цифры и дефисы. Первым и последним знаком должна быть буква или цифра, а доменное имя должно быть уникальным в пределах его расположения Azure. Если возвращается значение **True**, то предложенное имя является глобально уникальным. |
| Создание общедоступного IP-адреса |$pip = [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmPublicIpAddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>В общедоступном IP-адресе используется доменное имя, которое вы ранее проверили и которое используется интерфейсной конфигурацией балансировщика нагрузки. |
| Создание интерфейсной IP-конфигурации |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmLoadBalancerFrontendIpConfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Конфигурация внешнего интерфейса содержит общедоступный IP-адрес, созданный ранее для входящего сетевого трафика. |
| Создание внутреннего пула адресов |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmLoadBalancerBackendAddressPoolConfig) -Name "myBackendAddressPool"<BR><BR>Предоставляет внутренние адреса для серверной части балансировщика нагрузки, доступной через сетевой интерфейс. |
| Создание пробы |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmLoadBalancerProbeConfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Содержит пробы работоспособности, используемые для проверки доступности экземпляров виртуальных машин в пуле внутренних адресов. |
| Создание правила балансировки нагрузки |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmLoadBalancerRuleConfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Содержит правила, которые назначают общедоступный порт в балансировщике нагрузки порту во внутреннем пуле адресов. |
| Создание правила NAT для входящего трафика |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmLoadBalancerInboundNatRuleConfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Содержит правила сопоставления общедоступного порта в балансировщике нагрузки с портом конкретной виртуальной машины в пуле внутренних адресов. |
| Создание балансировщика нагрузки |$loadBalancer = [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmLoadBalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Создание сетевого интерфейса |$nic1= [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/New-AzureRmNetworkInterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Создает сетевой интерфейс с использованием общедоступного IP-адреса и подсети виртуальной сети, созданной ранее. |

## <a name="get-information-about-network-resources"></a>Получение сведений о сетевых ресурсах

| Задача | Команда |
| ---- | ------- |
| Получение списка виртуальных сетей |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Get-AzureRmVirtualNetwork) -ResourceGroupName $myResourceGroup<BR><BR>Выводит список всех виртуальных сетей в группе ресурсов. |
| Получение сведений о виртуальной сети |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Получение списка подсетей в виртуальной сети |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Получение сведений о подсети |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Get-AzureRmVirtualNetworkSubnetConfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Возвращает сведения о подсети в указанной виртуальной сети. Значение $vnet представляет собой объект, возвращаемый командлетом Get-AzureRmVirtualNetwork. |
| Получение списка IP-адресов |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Get-AzureRmPublicIpAddress) -ResourceGroupName $myResourceGroup<BR><BR>Выводит список общедоступных IP-адресов в группе ресурсов. |
| Получение списка балансировщиков нагрузки |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Get-AzureRmLoadBalancer) -ResourceGroupName $myResourceGroup<BR><BR>Выводит список всех балансировщиков нагрузки в группе ресурсов. |
| Получение списка сетевых интерфейсов |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Get-AzureRmNetworkInterface) -ResourceGroupName $myResourceGroup<BR><BR>Выводит список всех виртуальных интерфейсов в группе ресурсов. |
| Получение сведений о виртуальном интерфейсе |Get-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Получение сведений об определенном сетевом интерфейсе. |
| Получение IP-конфигурации сетевого интерфейса |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Get-AzureRmNetworkInterfaceIpConfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Возвращает сведения об IP-конфигурации указанного сетевого интерфейса. Значение $nic представляет собой объект, возвращаемый командлетом Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Управление сетевыми ресурсами

| Задача | Команда |
| ---- | ------- |
| Добавление подсети в виртуальную сеть |[Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Add-AzureRmVirtualNetworkSubnetConfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Добавляет подсеть в существующую виртуальную сеть. Значение $vnet представляет собой объект, возвращаемый командлетом Get-AzureRmVirtualNetwork. |
| Удаление виртуальной сети |[Remove-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Remove-AzureRmVirtualNetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Удаляет указанную виртуальную сеть из группы ресурсов. |
| Удаление сетевого интерфейса |[Remove-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Remove-AzureRmNetworkInterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Удаляет указанный сетевой интерфейс из группы ресурсов. |
| Удаление балансировщика нагрузки |[Remove-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Remove-AzureRmLoadBalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Удаляет указанный балансировщик нагрузки из группы ресурсов. |
| Удаление общедоступного IP-адреса |[Remove-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Remove-AzureRmPublicIpAddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Удаляет указанный общедоступный IP-адрес из группы ресурсов. |

## <a name="next-steps"></a>Дальнейшие действия
* Использование созданного сетевого интерфейса при [создании виртуальной машины](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Узнайте, как можно [создать виртуальную машину с несколькими сетевыми интерфейсами](../virtual-network/virtual-networks-multiple-nics.md).


