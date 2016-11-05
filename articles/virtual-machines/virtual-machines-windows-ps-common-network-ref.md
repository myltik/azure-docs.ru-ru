---
title: Общие сетевые команды PowerShell для виртуальных машин | Microsoft Docs
description: Общие команды PowerShell, позволяющие приступить к созданию виртуальной сети и связанных с ней ресурсов для виртуальных машин.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2016
ms.author: davidmu

---
# Общие сетевые команды Azure PowerShell для виртуальных машин
Если вы хотите создать виртуальную машину, необходимо создать [виртуальную сеть](../virtual-network/virtual-networks-overview.md) или иметь данные существующей виртуальной сети, в которую можно добавить виртуальную машину. Обычно при создании виртуальной машины требуется также рассмотреть возможность создания ресурсов, приведенных в этой статье.

## Создание ресурсов с помощью Azure PowerShell
Сведения о том, как установить последнюю версию Azure PowerShell, выбрать нужную подписку и войти в учетную запись Azure, см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

| Ресурс | Команда |
| --- | --- |
| Подсеть |$internetSubnet = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name internetSubnet -AddressPrefix 10.0.1.0/24<BR>$internalSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name internalSubnet -AddressPrefix 10.0.2.0/24<BR><BR>В обычной сети может быть подсеть для [балансировщика нагрузки с выходом в Интернет](../load-balancer/load-balancer-internet-overview.md) и отдельная подсеть для [внутреннего балансировщика нагрузки](../load-balancer/load-balancer-internal-overview.md). |
| Виртуальная сеть |Создание виртуальной сети:<BR><BR>$rgName = "[имя\_группы\_ресурсов](../powershell-azure-resource-manager.md)"<BR>$locName = "[имя\_расположения](https://msdn.microsoft.com/library/azure/dn495177.aspx)"<BR>$vnetName = "имя\_виртуальной\_сети"<BR>$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $internetSubnet,$internalSubnet<BR><BR>Получение списка виртуальных сетей:<BR><BR>[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName $rgName &#124; $rgName &#124; Select Name<BR><BR>Вывод списка подсетей в виртуальной сети:<BR><BR>Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName Sort Name &#124; Select Subnets<BR><BR>Список подсетей должен иметь следующий вид:<BR><BR>Subnets<BR>-------<BR>{internetSubnet, internalSubnet}<BR><BR>Индекс подсети internetSubnet равен 0, а индекс подсети internalSubnet равен 1. |
| Метка доменного имени |$domName = "доменное\_имя"<BR>[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName $domName -Location $locName<BR><BR>Вы можете указать DNS-имя для ресурса с [общедоступным IP-адресом](../virtual-network/virtual-network-ip-addresses-overview-arm.md). Это приведет к сопоставлению адреса domainnamelabel.location.cloudapp.azure.com с общедоступным IP-адресом на DNS-серверах под управлением Azure. Она может содержать только буквы, цифры и дефисы. Первым и последним знаком должна быть буква или цифра, и метка доменного имени должна быть уникальной в пределах его расположения Azure. Всегда следует проверять, является ли глобально уникальной метка доменного имени. Если возвращается значение **True**, то предложенное имя является глобально уникальным. |
| Общедоступный IP-адрес |$ipName = "имя\_общедоступного\_IP-адреса"<BR>$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name $ipName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic<BR><BR>В общедоступном IP-адресе используется метка доменного имени, которую вы ранее создали и которая используется конфигурацией внешнего интерфейса балансировщика нагрузки. |
| Конфигурация IP внешнего интерфейса |$feConfigName = "имя\_конфигурации\_IP\_внешнего\_интерфейса"<BR>$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name $feConfigName -PublicIpAddress $pip<BR><BR>Конфигурация внешнего интерфейса содержит общедоступный IP-адрес, созданный ранее для входящего сетевого трафика. |
| Пул адресов серверной части |$bePoolName = "имя\_внутреннего\_пула"<BR>$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name $bePoolName<BR><BR>Предоставляет внутренние адреса для серверной части балансировщика нагрузки, доступной через сетевой интерфейс. |
| Проба |$probeName = "имя\_пробы\_работоспособности"<BR>$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name $probeName -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Содержит пробы работоспособности, используемые для проверки доступности экземпляров виртуальных машин в пуле внутренних адресов. |
| Правило балансировки нагрузки |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Содержит правила сопоставления общедоступного порта в балансировщике нагрузки с портом в пуле внутренних адресов. |
| Правило преобразования сетевых адресов для входящих подключений |$ruleName = "имя\_правила\_NAT"<BR>$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name $ruleName -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Содержит правила сопоставления общедоступного порта в балансировщике нагрузки с портом конкретной виртуальной машины в пуле внутренних адресов. |
| Подсистема балансировки нагрузки |$lbName = "имя\_балансировщика\_нагрузки"<BR>$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName $rgName -Name $lbName -Location $locName -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Сетевой интерфейс |$vnet = [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -Name $vnetName -ResourceGroupName $rgName<BR>$internalSubnet = [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "internalSubnet" -VirtualNetwork $vnet<BR>$nicName = "имя\_сетевого\_интерфейса"<BR>$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName $rgName -Name $nicName -Location $locName -PrivateIpAddress 10.0.2.6 -Subnet $internalSubnet -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Создает сетевой интерфейс с использованием общедоступного IP-адреса и подсети виртуальной сети, созданной ранее. |

## Дальнейшие действия
* Использование созданного сетевого интерфейса при [создании виртуальной машины](virtual-machines-windows-ps-create.md).
* Узнайте, как можно [создать виртуальную машину с несколькими сетевыми интерфейсами](../virtual-network/virtual-networks-multiple-nics.md).

<!---HONumber=AcomDC_0629_2016-->