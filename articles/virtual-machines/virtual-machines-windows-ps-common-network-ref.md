---
title: "Общие сетевые команды PowerShell для виртуальных машин | Документация Майкрософт"
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
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: ce08e9dcd0585e00bdd42b8aa34ac2c597a53a6e


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>Общие сетевые команды Azure PowerShell для виртуальных машин
Если вы хотите создать виртуальную машину, необходимо создать [виртуальную сеть](../virtual-network/virtual-networks-overview.md) или иметь данные существующей виртуальной сети, в которую можно добавить виртуальную машину. Обычно при создании виртуальной машины требуется также рассмотреть возможность создания ресурсов, приведенных в этой статье.

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-network-resources"></a>Создание сетевых ресурсов
| Задача | Команда |
| --- | --- |
| Создание конфигураций подсети |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "имя_подсети" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "имя_подсети" -AddressPrefix XX.X.X.X/XX<BR><BR>В обычной сети может быть подсеть для [балансировщика нагрузки с выходом в Интернет](../load-balancer/load-balancer-internet-overview.md) и отдельная подсеть для [внутреннего балансировщика нагрузки](../load-balancer/load-balancer-internal-overview.md). |
| Создать виртуальную сеть |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "имя_виртуальной сети" -ResourceGroupName "имя_группы_ресурсов" -Location "имя_расположения" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Проверка доменного имени на уникальность |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName "доменное_имя" -Location "имя_расположения"<BR><BR>Вы можете указать DNS-имя домена для [ресурса с общедоступным IP-адресом](../virtual-network/virtual-network-ip-addresses-overview-arm.md). В результате этого будут сопоставлены адрес доменное_имя.расположение.cloudapp.azure.com и общедоступный IP-адрес на DNS-серверах под управлением Azure. Имя может содержать только буквы, цифры и дефисы. Первым и последним знаком должна быть буква или цифра, а доменное имя должно быть уникальным в пределах его расположения Azure. Если возвращается значение **True**, то предложенное имя является глобально уникальным. |
| Создание общедоступного IP-адреса |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "имя_ip-адреса" -ResourceGroupName "имя_группы_ресурсов" -DomainNameLabel "доменное_имя" -Location "имя_расположения" -AllocationMethod Dynamic<BR><BR>В общедоступном IP-адресе используется доменное имя, которое вы ранее проверили и которое используется интерфейсной конфигурацией балансировщика нагрузки. |
| Создание интерфейсной IP-конфигурации |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "имя_интерфейсного_IP-адреса" -PublicIpAddress $pip<BR><BR>Конфигурация внешнего интерфейса содержит общедоступный IP-адрес, созданный ранее для входящего сетевого трафика. |
| Создание внутреннего пула адресов |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "имя_внутреннего_пула"<BR><BR>Предоставляет внутренние адреса для серверной части балансировщика нагрузки, доступной через сетевой интерфейс. |
| Создание пробы |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "имя_пробы" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Содержит пробы работоспособности, используемые для проверки доступности экземпляров виртуальных машин в пуле внутренних адресов. |
| Создание правила балансировки нагрузки |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Содержит правила, которые назначают общедоступный порт в балансировщике нагрузки порту во внутреннем пуле адресов. |
| Создание правила NAT для входящего трафика |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "имя_правила" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Содержит правила сопоставления общедоступного порта в балансировщике нагрузки с портом конкретной виртуальной машины в пуле внутренних адресов. |
| Создание балансировщика нагрузки |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "имя_группы_ресурсов" -Name "имя_балансировщика_нагрузки" -Location "имя_расположения" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Создание сетевого интерфейса |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "имя_группы_ресурсов" -Name "имя_сетевого_интерфейса" -Location "имя_расположения" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Создает сетевой интерфейс с использованием общедоступного IP-адреса и подсети виртуальной сети, созданной ранее. |

## <a name="get-information-about-network-resources"></a>Получение сведений о сетевых ресурсах
| Задача | Команда |
| --- | --- |
| Получение списка виртуальных сетей |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Выводит список всех виртуальных сетей в группе ресурсов. |
| Получение сведений о виртуальной сети |Get-AzureRmVirtualNetwork -Name "имя_виртуальной_сети" -ResourceGroupName "имя_группы_ресурсов" |
| Получение списка подсетей в виртуальной сети |Get-AzureRmVirtualNetwork -Name "имя_виртуальной_сети" -ResourceGroupName "имя_группы_ресурсов" &#124; Select Subnets |
| Получение сведений о подсети |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "имя_подсети" -VirtualNetwork $vnet<BR><BR>Возвращает сведения о подсети в указанной виртуальной сети. Значение $vnet представляет собой объект, возвращаемый командлетом Get-AzureRmVirtualNetwork. |
| Получение списка IP-адресов |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Выводит список общедоступных IP-адресов в группе ресурсов. |
| Получение списка балансировщиков нагрузки |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Выводит список всех балансировщиков нагрузки в группе ресурсов. |
| Получение списка сетевых интерфейсов |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Выводит список всех виртуальных интерфейсов в группе ресурсов. |
| Получение сведений о виртуальном интерфейсе |Get-AzureRmNetworkInterface -Name "имя_сетевого_интерфейса" -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Получение сведений об определенном сетевом интерфейсе. |
| Получение IP-конфигурации сетевого интерфейса |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "имя_IP-конфигурации" -NetworkInterface $nic<BR><BR>Возвращает сведения об IP-конфигурации указанного сетевого интерфейса. Значение $nic представляет собой объект, возвращаемый командлетом Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Управление сетевыми ресурсами
| Задача | Команда |
| --- | --- |
| Добавление подсети в виртуальную сеть |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "имя_подсети" -VirtualNetwork $vnet<BR><BR>Добавляет подсеть в существующую виртуальную сеть. Значение $vnet представляет собой объект, возвращаемый командлетом Get-AzureRmVirtualNetwork. |
| Удаление виртуальной сети |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "имя_виртуальной_сети" -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Удаляет указанную виртуальную сеть из группы ресурсов. |
| Удаление сетевого интерфейса |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "имя_сетевого_интерфейса" -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Удаляет указанный сетевой интерфейс из группы ресурсов. |
| Удаление балансировщика нагрузки |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "имя_балансировщика_нагрузки" -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Удаляет указанный балансировщик нагрузки из группы ресурсов. |
| Удаление общедоступного IP-адреса |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "имя_ip-адреса" -ResourceGroupName "имя_группы_ресурсов"<BR><BR>Удаляет указанный общедоступный IP-адрес из группы ресурсов. |

## <a name="next-steps"></a>Дальнейшие действия
* Использование созданного сетевого интерфейса при [создании виртуальной машины](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Узнайте, как можно [создать виртуальную машину с несколькими сетевыми интерфейсами](../virtual-network/virtual-networks-multiple-nics.md).




<!--HONumber=Dec16_HO2-->


