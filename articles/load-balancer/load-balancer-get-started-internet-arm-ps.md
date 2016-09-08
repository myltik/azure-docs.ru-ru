<properties 
   pageTitle="Создание балансировщика нагрузки для Интернета в диспетчере ресурсов с помощью PowerShell | Microsoft Azure"
   description="Узнайте, как создать балансировщик нагрузки для Интернета в диспетчере ресурсов с помощью PowerShell."
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
   ms.date="04/05/2016"
   ms.author="sewhee" />

# Приступая к созданию балансировщика нагрузки для Интернета в диспетчере ресурсов с помощью PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [узнать, как создать балансировщик нагрузки для Интернета, используя классическую модель развертывания](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Ниже описана процедура создания подсистемы балансировки нагрузки для Интернета с помощью диспетчера ресурсов Azure и Azure PowerShell. Диспетчер ресурсов Azure позволяет по отдельности настраивать элементы, необходимые для создания подсистемы балансировки нагрузки для Интернета, после чего на их основе создается единый ресурс.

На этой странице приводится порядок выполнения отдельных операций, которые требуется выполнить для создания балансировщика нагрузки, а также подробно объясняется, что необходимо сделать для решения этой задачи.

## Что необходимо для создания подсистемы балансировки нагрузки для Интернета?

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты:

- Конфигурация IP-адресов клиентской части. Содержит общедоступные IP-адреса для входящего сетевого трафика.

- Пул внутренних адресов. Содержит сетевые интерфейсы (сетевые карты) для получения виртуальными машинами трафика от балансировщика нагрузки.

- Правила балансировки нагрузки. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом в пуле внутренних адресов.

- Правила преобразования сетевых адресов (NAT) для входящего трафика. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.

- Пробы. Содержат пробы работоспособности, с помощью которых можно проверить доступность экземпляров виртуальных машин в пуле внутренних адресов.

Подробнее о настройке компонентов балансировщика нагрузки при помощи диспетчера ресурсов Azure см. в статье [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](load-balancer-arm.md).


## Настройка PowerShell для использования диспетчера ресурсов

Убедитесь, что вы используете последнюю рабочую версию модуля диспетчера ресурсов Azure (ARM) для PowerShell.

### Шаг 1

		Login-AzureRmAccount

Вам будет предложено пройти проверку подлинности с вашими учетными данными.<BR>

### Шаг 2

Проверка подписок для учетной записи

		Get-AzureRmSubscription 

### Шаг 3. 

Выберите, какие подписки Azure будут использоваться. <BR>

		Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

### Шаг 4.

Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).


    	New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## Создание виртуальной сети и общедоступного IP-адреса для пула IP-адресов клиентской части

### Шаг 1

Создайте подсеть и виртуальную сеть.

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Шаг 2

Создайте ресурс для общедоступного IP-адреса Azure с именем *PublicIP*, который будет использоваться пулом IP-адресов клиентской части с DNS-именем *loadbalancernrp.westus.cloudapp.azure.com*. В команде ниже используется статическое выделение.

	$publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT] Балансировщик нагрузки будет использовать метку домена общедоступного IP-адреса в качестве префикса к полному доменному имени (FQDN). В этом заключается отличие от классической модели развертывания, где в качестве полного доменного имени балансировщика нагрузки используется облачная служба. В этом примере используется полное доменное имя *loadbalancernrp.westus.cloudapp.azure.com*.

## Создание пула IP-адресов клиентской части и пула адресов серверной части

### Шаг 1 

Создайте пул IP-адресов клиентской части с именем *LB-Frontend*, использующий общедоступный IP-адрес *PublicIp*.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Шаг 2 

Создайте пул адресов серверной части с именем *LB-backend*.

	$beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## Создание правил балансировки нагрузки, правил преобразования сетевых адресов, пробы и балансировщика нагрузки

В примере ниже создаются следующие элементы:

- правило NAT, которое направляет весь входящий трафик с порта 3441 на порт 3389;
- правило NAT, которое направляет весь входящий трафик с порта 3442 на порт 3389;
- правило балансировщика нагрузки, которое балансирует весь входящий трафик на порту 80, перенаправляя трафик на порт 80 других адресов в пуле серверной части;
- правило пробы, согласно которому будет проверяться состояние работоспособности на странице *HealthProbe.aspx*.
- балансировщик нагрузки, который использует все объекты выше.


### Шаг 1

Создайте правила NAT.

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Шаг 2

Создайте правило балансировщика нагрузки.

	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Шаг 3.

Создайте пробу работоспособности. Существует два способа настройки пробы:
 
проба HTTP
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
или

проба TCP.
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Шаг 4.

Создайте балансировщик нагрузки с помощью объектов, созданных ранее.

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## Создание сетевых адаптеров

Вам необходимо создать сетевые интерфейсы (или изменить существующие) и связать их с правилами NAT, правилами балансировщика нагрузки и пробами.

### Шаг 1 

Получите виртуальную сеть и подсеть виртуальной сети, в которых должны быть созданы сетевые карты.

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Шаг 2

Создайте сетевую карту *lb-nic1-be* и сопоставьте ее с первым правилом NAT, а также с первым (и единственным) пулом внутренних адресов.
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Шаг 3.

Создайте сетевую карту *lb-nic2-be* и сопоставьте ее со вторым правилом NAT, а также с первым (и единственным) серверным пулом адресов.

	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Шаг 4.

Проверьте сетевые адаптеры.


	$backendnic1

Ожидаемые выходные данные:

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### Шаг 5

С помощью командлета `Add-AzureRmVMNetworkInterface` назначьте сетевые карты различным виртуальным машинам.

Инструкции по созданию виртуальной машины и назначению сетевой карты см. в статье [Создание и предварительная настройка виртуальной машины Windows с помощью диспетчера ресурсов и Azure PowerShell](../virtual-machines/virtual-machines-windows-create-powershell.md#Example) (вариант 5 в примере).


Если виртуальная машина у вас уже есть, добавьте сетевой интерфейс, выполнив описанные ниже действия:

#### Шаг 1 

Загрузите ресурс балансировщика нагрузки в переменную (если вы это еще не сделали). Имя переменной — $lb. Используйте имена из ресурса балансировщика нагрузки, созданного ранее.

	$lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

#### Шаг 2 

Загрузите в переменную конфигурацию серверной части.

	$backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### Шаг 3. 

Загрузите в переменную созданный ранее сетевой интерфейс. Имя переменной — $nic. Имя сетевого интерфейса совпадает с именем в приведенном выше примере.

	$nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

#### Шаг 4.

Измените конфигурацию серверной части в сетевом интерфейсе.

	$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### Шаг 5 

Сохраните объект сетевого интерфейса.

	Set-AzureRmNetworkInterface -NetworkInterface $nic

После того как сетевой интерфейс будет добавлен в пул серверной части балансировщика нагрузки, он начнет получать сетевой трафик согласно правилам балансировки нагрузки для соответствующего ресурса балансировщика.

## Обновление существующего балансировщика нагрузки


### Шаг 1

Используя балансировщик нагрузки из предыдущего примера, присвойте переменной $slb ссылку на объект балансировщика нагрузки, используя метод Get-AzureLoadBalancer.

	$slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Шаг 2

В следующем примере вы добавите новое входящее правило NAT для порта 81 на клиентской части и порта 8181 на серверной части, которое будет применяться к пулу существующего балансировщика нагрузки.

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP


### Шаг 3.

Сохраните новую конфигурацию, используя командлет Set-AzureLoadBalancer.

	$slb | Set-AzureRmLoadBalancer

## Удаление балансировщика нагрузки

Воспользуйтесь командой `Remove-AzureLoadBalancer`, чтобы удалить ранее созданный балансировщик нагрузки с именем NRP-LB в группе ресурсов NRP RG.

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Чтобы пропустить подтверждение удаления, можно использовать необязательный ключ -Force.

## Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!----HONumber=AcomDC_0824_2016-->