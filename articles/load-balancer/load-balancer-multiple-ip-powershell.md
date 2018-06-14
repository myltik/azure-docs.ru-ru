---
title: Балансировка нагрузки в конфигурациях с несколькими IP-адресами в Azure | Документация Майкрософт
description: Балансировка нагрузки между основной и дополнительной IP-конфигурациями.
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 12a978fc85d9502ce484859b436575b67364c9c4
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
ms.locfileid: "30261727"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Балансировка нагрузки в конфигурациях с несколькими IP-адресами с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал](load-balancer-multiple-ip.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


В этой статье описывается, как использовать Azure Load Balancer в конфигурации, когда каждому дополнительному сетевому интерфейсу (сетевой карты) назначено несколько IP-адресов. В этом сценарии у нас есть две виртуальные машины под управлением Windows, оснащенные основной и дополнительной сетевыми картами. В каждом из дополнительных сетевых интерфейсов есть по две IP-конфигурации. На каждой виртуальной машине размещены веб-сайты contoso.com и fabrikam.com. Каждый веб-сайт привязан к одной из IP-конфигураций дополнительной сетевой карты. Мы используем Azure Load Balancer, чтобы предоставить два интерфейсных IP-адреса, по одному для каждого веб-сайта. Это позволит направлять трафик в соответствующую IP-конфигурацию для веб-сайта. В данном сценарии используется одинаковый номер порта для обоих внешних интерфейсов, как и для обоих IP-адресов внутренних пулов.

![Схема балансировки нагрузки для сценария](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Инструкции по балансировке нагрузки в конфигурациях с несколькими IP-адресами

Выполните следующие действия, чтобы реализовать сценарий, описанный в этой статье.

1. Установите Azure PowerShell. Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Как установить и настроить службы Azure PowerShell](/powershell/azure/overview).
2. Создайте группу ресурсов, указав приведенные ниже параметры.

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Чтобы узнать больше, ознакомьтесь с шагом 2 в разделе [Создание группы ресурсов](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Создайте группу доступности](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) для виртуальных машин. В рамках данного сценария воспользуйтесь следующей командой.

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Следуйте инструкциям для шагов 3–5 в разделе [Создание виртуальной машины Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json), чтобы подготовить среду к созданию виртуальной машины с одной сетевой картой. Выполните шаг 6.1, затем вместо шага 6.2 выполните следующую команду.

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    После этого выполните шаги 6.3–6.8 раздела [Создание виртуальной машины Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

5. Добавьте вторую IP-конфигурации в каждую виртуальную машину. Следуйте инструкциям в статье [Назначение виртуальным машинам нескольких IP-адресов](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add). Используйте следующие параметры конфигурации.

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    В рамках данного руководства нет необходимости связывать дополнительные IP-конфигурации с общедоступными IP-адресами. Измените команду, чтобы удалить связывание с общедоступным IP-адресом.

6. Повторите шаги 4–6 данной статьи для VM2. Не забудьте заменить в командах VM на VM2. Обратите внимание, что для второй виртуальной машины не нужно создавать виртуальную сеть. Вы можете создать или не создавать новую подсеть в зависимости от требований ситуации.

7. Создайте два общедоступных IP-адреса и сохраните их в соответствующих переменных, как показано ниже.

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Создайте две интерфейсные IP-конфигурации.

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Создайте внутренние пулы адресов, пробу и правила балансировки нагрузки.

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. После создания этих ресурсов создайте подсистему балансировки нагрузки.

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Добавьте второй внутренний пул адресов и интерфейсную IP-конфигурацию в созданную подсистему балансировки нагрузки.

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Приведенные ниже команды используются для получения сетевых карт и добавления обеих IP-конфигураций каждой дополнительной сетевой карты во внутренний пул адресов подсистемы балансировки нагрузки.

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Наконец, необходимо настроить записи ресурсов DNS, чтобы они указывали на соответствующие интерфейсные IP-адреса подсистемы балансировки нагрузки. Домены можно разместить в Azure DNS. Дополнительные сведения об использовании Azure DNS с подсистемой балансировки нагрузки см. в разделе [Использование Azure DNS с другими службами Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Дополнительная информация
- Узнайте больше о том, как объединять службы балансировки нагрузки, в статье [Использование служб балансировки нагрузки в Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Узнайте, как в Azure можно использовать журналы разных типов для управления подсистемой балансировки нагрузки и устранения неполадок в ее работе, ознакомившись со статьей [Служба анализа журналов для балансировщика нагрузки Azure](../load-balancer/load-balancer-monitor-log.md).
