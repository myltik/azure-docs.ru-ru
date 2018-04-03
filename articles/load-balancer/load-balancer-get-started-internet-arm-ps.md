---
title: Создание общедоступной подсистемы балансировки нагрузки с помощью PowerShell | Документация Майкрософт
description: Узнайте, как создать общедоступную подсистему балансировки нагрузки в Resource Manager с помощью PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4ce11b0b06e1feaf55d17e25500c43a7eb1bf3d5
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="get-started"></a>Создание общедоступной подсистемы балансировки нагрузки в Resource Manager с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Шаблон](../load-balancer/load-balancer-get-started-internet-arm-template.md)



[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Развертывание решения с помощью Azure PowerShell

Ниже описано, как создать общедоступную подсистему балансировки нагрузки с помощью Azure Resource Manager и PowerShell. Azure Resource Manager позволяет по отдельности создавать и настраивать ресурсы, после чего на их основе создается балансировщик нагрузки.

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты.

* Конфигурация внешних IP-адресов. Содержит общедоступные IP-адреса для входящего сетевого трафика.
* Пул внутренних адресов. Содержит сетевые интерфейсы (сетевые карты) для получения виртуальными машинами сетевого трафика от балансировщика нагрузки.
* Правила балансировки нагрузки. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом в пуле внутренних адресов.
* Правила преобразования сетевых адресов (NAT) для входящего трафика. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.
* Пробы. Содержат пробы работоспособности, с помощью которых можно проверить доступность экземпляров виртуальных машин в пуле внутренних адресов.

Дополнительные сведения см. в статье [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Настройка PowerShell для использования Resource Manager

Убедитесь, что вы используете последнюю рабочую версию модуля Azure Resource Manager для PowerShell.

1. Войдите в Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    При появлении запроса введите свои учетные данные.

2. Просмотрите подписки учетной записи.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Выберите, какие подписки Azure будут использоваться.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Создайте группу ресурсов. Если используется существующая группа ресурсов, пропустите этот шаг.

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Создание виртуальной сети и общедоступного IP-адреса для пула IP-адресов клиентской части

1. Создайте подсеть и виртуальную сеть.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Создайте ресурс для общедоступного IP-адреса Azure с именем **PublicIP**, который будет использоваться пулом внешних IP-адресов с DNS-именем **loadbalancernrp.westus.cloudapp.azure.com**. В следующей команде используется статическое выделение.

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > Балансировщик нагрузки использует метку домена общедоступного IP-адреса в качестве префикса к полному доменному имени (FQDN). В этом заключается отличие от классической модели развертывания, где в качестве полного доменного имени балансировщика нагрузки используется облачная служба.
   > В этом примере используется полное доменное имя **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Создание пула внешних IP-адресов и пула внутренних адресов

1. Создайте пул внешних IP-адресов с именем **LB-Frontend**, использующий ресурс **PublicIp**.

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. Создайте пул адресов серверной части с именем **LB-backend**.

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Создание правил NAT, правила балансировщика нагрузки, пробы и балансировщика нагрузки

В этом примере создаются следующие элементы:

* правило NAT, которое направляет весь входящий трафик с порта 3441 на порт 3389;
* правило NAT, которое направляет весь входящий трафик с порта 3442 на порт 3389;
* правило пробы, согласно которому будет проверяться состояние работоспособности на странице **HealthProbe.aspx**
* правило балансировщика нагрузки, которое балансирует весь входящий трафик на порту 80, перенаправляя трафик на порт 80 других адресов во внутреннем пуле;
* балансировщик нагрузки, который использует все эти объекты.

Выполните следующие действия.

1. Создайте правила NAT.

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. Создайте пробу работоспособности. Существует два способа настройки пробы:

    проба HTTP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    проба TCP.

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. Создайте правило балансировщика нагрузки.

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. Создайте балансировщик нагрузки с помощью ранее созданных объектов.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>Создание сетевых адаптеров

Создайте сетевые интерфейсы (или измените существующие) и свяжите их с правилами NAT, правилами балансировщика нагрузки и пробами.

1. Получите виртуальную сеть и подсеть виртуальной сети, в которых должны быть созданы сетевые карты.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Создайте сетевую карту **lb-nic1-be**и сопоставьте ее с первым правилом NAT, а также с первым (и единственным) пулом внутренних адресов.

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. Создайте сетевую карту **lb-nic2-be**и сопоставьте ее со вторым правилом NAT, а также с первым (и единственным) пулом внутренних адресов.

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. Проверьте сетевые адаптеры.

        $backendnic1

    Ожидаемые выходные данные:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. С помощью командлета `Add-AzureRmVMNetworkInterface` назначьте сетевые карты различным виртуальным машинам.

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Рекомендации по созданию виртуальной машины и назначении сетевой карты см. в статье [Create an Azure VM using PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) (Создание виртуальной машины в Azure с помощью PowerShell).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Добавление сетевого интерфейса в балансировщик нагрузки

1. Извлеките балансировщик нагрузки из Azure.

    Загрузите ресурс балансировщика нагрузки в переменную (если вы это еще не сделали). Имя переменной — **$lb**. Используйте те же имена из созданного ранее балансировщика нагрузки.

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. Загрузите в переменную конфигурацию серверной части.

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
    ```

3. Загрузите в переменную созданный ранее сетевой интерфейс. Имя переменной — **$nic**. Имя сетевого интерфейса совпадает с именем в приведенном выше примере.

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. Измените конфигурацию серверной части в сетевом интерфейсе.

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. Сохраните объект сетевого интерфейса.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    После того как сетевой интерфейс будет добавлен в пул серверной части балансировщика нагрузки, он начнет получать сетевой трафик согласно правилам балансировки нагрузки для соответствующего ресурса балансировщика нагрузки.

## <a name="update-an-existing-load-balancer"></a>Обновление существующего балансировщика нагрузки

1. Используя балансировщик нагрузки из предыдущего примера, присвойте переменной **$slb** объект балансировщика нагрузки, используя `Get-AzureLoadBalancer`.

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
    ```

2. В следующем примере вы, используя порт 81 во внешнем пуле и порт 8181 во внутреннем пуле, добавите правило NAT для входящего трафика, которое будет применяться к существующему балансировщику нагрузки.

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. Сохраните новую конфигурацию с помощью `Set-AzureLoadBalancer`.

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>Удалите балансировщика нагрузки.

Воспользуйтесь командой `Remove-AzureLoadBalancer`, чтобы удалить ранее созданный балансировщик нагрузки с именем **NRP-LB** в группе ресурсов **NRP-RG**.

```powershell
Remove-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Чтобы пропустить подтверждение удаления, можно использовать необязательный переключатель **-Force** .

## <a name="next-steps"></a>Дополнительная информация

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
