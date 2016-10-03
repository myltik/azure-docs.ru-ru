<properties
   pageTitle="Создание балансировщика нагрузки для Интернета в Resource Manager с помощью PowerShell | Microsoft Azure"
   description="Узнайте, как создать балансировщик нагрузки для Интернета в Resource Manager с помощью PowerShell."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="08/31/2016"
  ms.author="sewhee" />

# <a name="get-started"></a>Приступая к созданию балансировщика нагрузки для Интернета в Resource Manager с помощью PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [узнать, как создать балансировщик нагрузки для Интернета с помощью классической модели развертывания](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Развертывание решения с помощью Azure PowerShell

Ниже описана процедура создания балансировщика нагрузки для Интернета с помощью Azure Resource Manager и PowerShell. Azure Resource Manager позволяет по отдельности создавать и настраивать ресурсы, после чего на их основе создается единый ресурс.

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты.

- Конфигурация внешних IP-адресов. Содержит общедоступные IP-адреса для входящего сетевого трафика.
- Пул внутренних адресов. Содержит сетевые интерфейсы (сетевые карты) для получения виртуальными машинами сетевого трафика от балансировщика нагрузки.
- Правила балансировки нагрузки. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом в пуле внутренних адресов.
- Правила преобразования сетевых адресов (NAT) для входящего трафика. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.
- Пробы. Содержат пробы работоспособности, с помощью которых можно проверить доступность экземпляров виртуальных машин в пуле внутренних адресов.

Дополнительные сведения см. в статье, посвященной [поддержке Azure Resource Manager для балансировщика нагрузки](load-balancer-arm.md).

## Настройка PowerShell для использования Resource Manager

Убедитесь, что вы используете последнюю рабочую версию модуля Azure Resource Manager для PowerShell.

1. Войдите в Azure.

        Login-AzureRmAccount

    При появлении запроса введите свои учетные данные.

2. Просмотрите подписки учетной записи.

        Get-AzureRmSubscription

3. Выберите подписку Azure.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Создайте группу ресурсов. Если используется существующая группа ресурсов, пропустите этот шаг.

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## Создание виртуальной сети и общедоступного IP-адреса для пула IP-адресов клиентской части

1. Создайте подсеть и виртуальную сеть.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Создайте ресурс для общедоступного IP-адреса Azure с именем **PublicIP**, который будет использоваться пулом внешних IP-адресов с DNS-именем **loadbalancernrp.westus.cloudapp.azure.com**. В следующей команде используется статическое выделение.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT] Балансировщик нагрузки использует метку домена общедоступного IP-адреса в качестве префикса к полному доменному имени (FQDN). В этом заключается отличие от классической модели развертывания, где в качестве полного доменного имени балансировщика нагрузки используется облачная служба. В этом примере используется полное доменное имя **loadbalancernrp.westus.cloudapp.azure.com**.

## Создание пула внешних IP-адресов и пула внутренних адресов

1. Создайте пул внешних IP-адресов с именем **LB-Frontend**, использующий ресурс **PublicIp**.

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Создайте пул адресов серверной части с именем **LB-backend**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## Создание правил NAT, правила балансировщика нагрузки, пробы и балансировщика нагрузки

В этом примере создаются следующие элементы:

- правило NAT, которое направляет весь входящий трафик с порта 3441 на порт 3389;
- правило NAT, которое направляет весь входящий трафик с порта 3442 на порт 3389;
- правило пробы, согласно которому будет проверяться состояние работоспособности на странице **HealthProbe.aspx**;
- правило балансировщика нагрузки, которое балансирует весь входящий трафик на порту 80, перенаправляя трафик на порт 80 других адресов во внутреннем пуле;
- балансировщик нагрузки, который использует все эти объекты.

Выполните следующие действия.

1. Создайте правила NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Создайте пробу работоспособности. Существует два способа настройки пробы:

    проба HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    проба TCP.

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Создайте правило балансировщика нагрузки.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Создайте балансировщик нагрузки с помощью ранее созданных объектов.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## Создание сетевых адаптеров

Создайте сетевые интерфейсы (или измените существующие) и свяжите их с правилами NAT, правилами балансировщика нагрузки и пробами.

1. Получите виртуальную сеть и подсеть виртуальной сети, в которых должны быть созданы сетевые карты.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Создайте сетевую карту **lb-nic1-be** и сопоставьте ее с первым правилом NAT, а также с первым (и единственным) пулом внутренних адресов.

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Создайте сетевую карту **lb-nic2-be** и сопоставьте ее со вторым правилом NAT, а также с первым (и единственным) пулом внутренних адресов.

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

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
                            "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
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

## Создание виртуальной машины

Инструкции по созданию виртуальной машины и назначению сетевой карты см. в шаге 5 статьи [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](../virtual-machines/virtual-machines-windows-create-powershell.md#Example).

## Добавление сетевого интерфейса в балансировщик нагрузки

1. Извлеките балансировщик нагрузки из Azure.

    Загрузите ресурс балансировщика нагрузки в переменную (если вы это еще не сделали). Имя переменной — **$lb**. Используйте те же имена из созданного ранее балансировщика нагрузки.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Загрузите в переменную конфигурацию серверной части.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Загрузите в переменную созданный ранее сетевой интерфейс. Имя переменной — **$nic**. Имя сетевого интерфейса совпадает с именем в приведенном выше примере.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Измените конфигурацию серверной части в сетевом интерфейсе.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Сохраните объект сетевого интерфейса.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    После того как сетевой интерфейс будет добавлен в пул серверной части балансировщика нагрузки, он начнет получать сетевой трафик согласно правилам балансировки нагрузки для соответствующего ресурса балансировщика нагрузки.

## Обновление существующего балансировщика нагрузки

1. Используя балансировщик нагрузки из предыдущего примера, присвойте переменной **$slb** объект балансировщика нагрузки, используя `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. В следующем примере вы, используя порт 81 во внешнем пуле и порт 8181 во внутреннем пуле, добавите правило NAT для входящего трафика, которое будет применяться к существующему балансировщику нагрузки.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Сохраните новую конфигурацию с помощью `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## Удалите балансировщика нагрузки.

Воспользуйтесь командой `Remove-AzureLoadBalancer`, чтобы удалить ранее созданный балансировщик нагрузки с именем **NRP-LB** в группе ресурсов **NRP-RG**.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Чтобы пропустить подтверждение удаления, можно использовать необязательный переключатель **-Force**.

## Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0921_2016-->