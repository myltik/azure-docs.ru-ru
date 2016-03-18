<properties
   pageTitle="Приступая к работе с внутренней подсистемой балансировки нагрузки с помощью диспетчера ресурсов Azure | Microsoft Azure"
   description="Как создавать правила внутренней подсистемы балансировки нагрузки, правила NAT и пробы для диспетчера ресурсов Azure. Пошаговые инструкции по созданию ресурса внутренней подсистемы балансировки нагрузки."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/24/2015"
   ms.author="joaoma" />

# Приступая к настройке внутренней подсистемы балансировки нагрузки с помощью диспетчера ресурсов Azure


> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internal-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-internal-arm-powershell.md)


Ниже описана процедура создания внутренней подсистемы балансировки нагрузки с помощью диспетчера ресурсов Azure и PowerShell. Диспетчер ресурсов Azure позволяет по отдельности настраивать элементы для создания внутренней подсистемы балансировки нагрузки, после чего на их основе создается единый ресурс.

На этой странице приводится порядок выполнения отдельных задач для создания внутренней подсистемы балансировки нагрузки, а также подробно объясняется каждое действие в рамках процесса ее создания.


## Что необходимо для создания внутренней подсистемы балансировки нагрузки?


Перед созданием внутренней подсистемы балансировки нагрузки необходимо настроить следующие элементы.

- Настройка внешнего IP-адреса — настройка частного IP-адреса для входящего сетевого трафика. 

- Внутренний пул адресов — настройка сетевых интерфейсов, которые будут получать трафик с распределенной нагрузкой из внешнего пула IP-адресов.

- Правила балансировки нагрузки — конфигурация исходного и локального портов для подсистемы балансировки нагрузки.

- Пробы — настройка проб для проверки состояния работоспособности экземпляров виртуальной машины.

- Входящие правила преобразования сетевых адресов (NAT) — настройка правил порта для прямого доступа к одному из экземпляров виртуальной машины.

Дополнительные сведения о настройке компонентов подсистемы балансировки нагрузки при помощи диспетчера ресурсов Azure см. в статье [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](load-balancer-arm.md).

Следующие шаги описывают настройку балансировщика нагрузки между двумя виртуальными машинами.


## Пошаговая инструкция для PowerShell


### Создание группы ресурсов для подсистемы балансировки нагрузки


### Шаг 1

		PS C:\> Login-AzureRmAccount



### Шаг 2

Проверка подписок для учетной записи

		PS C:\> get-AzureRmSubscription 

Вам будет предложено пройти проверку подлинности с вашими учетными данными.<BR>

### Шаг 3. Выберите, какие подписки Azure будут использоваться. <BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"



### Шаг 4.

Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).

    PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется по умолчанию для ресурсов в этой группе. Убедитесь, что во всех командах создания подсистемы балансировки нагрузки используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов под названием «NRP RG» с расположением «Запад США».

## Создание виртуальной сети и общедоступного IP-адреса для пула IP-адресов клиентской части


### Шаг 1

Создание подсети для виртуальной сети и присвоение ее переменной $backendSubnet

	$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Создайте виртуальную сеть:

	$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Создание виртуальной сети, добавление подсети lb-subnet-be к виртуальной сети NRPVNet и присвоение ее переменной $vnet



## Создание пула IP-адресов клиентской части и пула адресов серверной части

Настройка пула IP-адресов клиентской части для входящего сетевого трафика подсистемы балансировки нагрузки и пула адресов серверной части для получения распределенного сетевого трафика.

### Шаг 1 

Создание внешнего пула IP-адресов с помощью частного IP-адреса 10.0.2.5 для подсети 10.0.2.0/24, которая будет служить конечной точкой для входящего сетевого трафика.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.Subnets.Id

### Шаг 2. 

Настройте пул адресов серверной части для приема входящего трафика из пула IP-адресов клиентской части:

	$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Создание правил балансировки нагрузки, правил преобразования сетевых адресов, пробы и подсистемы балансировки нагрузки

После создания пула IP-адресов клиентской части и пула адресов серверной части необходимо создать правила, которые будут принадлежать ресурсу подсистемы балансировки нагрузки:

### Шаг 1.

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


В вышеприведенном примере создаются следующие элементы.

- Входящее правило NAT для направления трафика с порта 3441 на порт 3389.
- Второе входящее правило NAT для направления трафика с порта 3442 на порт 3389.
- Правило балансировки нагрузки, согласно которому весь трафик, поступающий на общедоступный порт 80, будет распределяться на локальный порт 80 в пуле адресов серверной части.
- Правило пробы, согласно которому будет проверяться доступность пути «HealthProbe.aspx».



### Шаг 2.

Создайте подсистему балансировки нагрузки, объединив все объекты (правила преобразования сетевых адресов, правила балансировки нагрузки, настройки проб):

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## Создание сетевых интерфейсов

После создания внутренней подсистемы балансировки нагрузки необходимо определить сетевые интерфейсы, к которым будут применяться правила NAT и пробы и которые будут получать сетевой трафик с распределенной нагрузкой. В этом случае сетевой интерфейс настраивается отдельно и может быть назначен виртуальной машине не сразу.


### Шаг 1. 


Определите ресурс виртуальной сети и подсети для создания сетевых интерфейсов:

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


На этом шаге мы создаем сетевой интерфейс, которой будет принадлежать пулу серверной части подсистемы балансировки нагрузки, а затем назначаем первое правило NAT протоколу удаленного рабочего стола для данного сетевого интерфейса:
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Шаг 2.

Создайте второй сетевой интерфейс с названием «LB-Nic2-BE»:

На этом шаге мы создаем второй сетевой интерфейс, присваиваем его тому же пулу серверной части подсистемы балансировки нагрузки и связываем второе правило NAT с протоколом удаленного рабочего стола:

 	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


Результат будет аналогичным следующему:


PS C:\> $backendnic1


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



### Шаг 3. 

Используйте команду Add-AzureRmVMNetworkInterface, чтобы присвоить сетевую карту виртуальной машине.

Пошаговые инструкции по созданию виртуальной машины и назначению сетевой карты см. в статье [Создание и предварительная настройка виртуальной машины под управлением Windows с помощью диспетчера ресурсов и Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) (варианты 4 или 5).


## Обновление существующего балансировщика нагрузки


### Шаг 1

Используя балансировщик нагрузки из предыдущего примера, присвойте переменной $slb ссылку на объект балансировщика нагрузки, используя метод Get-AzureLoadBalancer.

	$slb=get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Шаг 2

В следующем примере вы добавите новое входящее правило NAT для порта 81 на клиентской части и порта 8181 на серверной части, которое будет применяться к пулу существующего балансировщика нагрузки.

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Шаг 3.

Сохраните новую конфигурацию, используя командлет Set-AzureLoadBalancer.

	$slb | Set-AzureRmLoadBalancer

## Удалите балансировщика нагрузки.

Воспользуйтесь командой Remove-AzureLoadBalancer, чтобы удалить ранее созданного балансировщика нагрузки с именем NRP-LB в группе ресурсов NRP RG.

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]Чтобы пропустить подтверждение удаления, можно использовать необязательный ключ -Force.



## См. также

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_1125_2015-->