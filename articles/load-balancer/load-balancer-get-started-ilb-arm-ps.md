<properties 
   pageTitle="Создание внутреннего балансировщика нагрузки в диспетчере ресурсов с помощью PowerShell | Microsoft Azure"
   description="Узнайте, как создать внутренний балансировщик нагрузки в диспетчере ресурсов с помощью PowerShell."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
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
   ms.date="01/21/2015"
   ms.author="joaoma" />

# Начало работы по созданию внутреннего балансировщика нагрузки с помощью PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


Ниже описана процедура создания внутренней подсистемы балансировки нагрузки с помощью диспетчера ресурсов Azure и PowerShell. Диспетчер ресурсов Azure позволяет по отдельности настраивать элементы для создания внутренней подсистемы балансировки нагрузки, после чего на их основе создается единый ресурс.

В этой статье рассматривается порядок выполнения отдельных задач для создания внутреннего балансировщика нагрузки, а также подробно объясняется каждое действие в рамках процесса его создания.


## Что необходимо для создания внутренней подсистемы балансировки нагрузки?


Перед созданием внутренней подсистемы балансировки нагрузки необходимо настроить следующие элементы.

- Настройка внешнего IP-адреса — настройка частного IP-адреса для входящего сетевого трафика. 

- Внутренний пул адресов — настройка сетевых интерфейсов, которые будут получать трафик с распределенной нагрузкой из внешнего пула IP-адресов.

- Правила балансировки нагрузки — конфигурация исходного и локального портов для подсистемы балансировки нагрузки.

- Пробы — настройка проб для проверки состояния работоспособности экземпляров виртуальной машины.

- Входящие правила преобразования сетевых адресов (NAT) — настройка правил порта для прямого доступа к одному из экземпляров виртуальной машины.

Дополнительную информацию о настройке компонентов балансировщика нагрузки при помощи диспетчера ресурсов Azure см. в статье [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](load-balancer-arm.md).

Следующие шаги описывают настройку балансировщика нагрузки между двумя виртуальными машинами.


## Пошаговая инструкция для PowerShell


### Настройка PowerShell для использования диспетчера ресурсов

Убедитесь, что у вас установлена последняя рабочая версия модуля Azure для PowerShell и PowerShell правильно настроена для доступа к подписке Azure.

### Шаг 1

		PS C:\> Login-AzureRmAccount



### Шаг 2

Проверка подписок для учетной записи

		PS C:\> get-AzureRmSubscription 

Вам будет предложено пройти проверку подлинности с вашими учетными данными.<BR>

### Шаг 3. 

Выберите, какие подписки Azure будут использоваться. <BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Создание группы ресурсов для подсистемы балансировки нагрузки

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

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

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



### Шаг 3. 

Используйте команду Add-AzureRmVMNetworkInterface, чтобы присвоить сетевую карту виртуальной машине.

Пошаговые инструкции по созданию виртуальной машины и назначению сетевой карты см. в статье [Создание и настройка виртуальной машины Windows с помощью диспетчера ресурсов и Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) (вариант 4 или 5).

Если виртуальная машина у вас уже есть, добавьте сетевой интерфейс, выполнив описанные ниже действия:

#### Шаг 1 

Загрузите ресурс балансировщика нагрузки в переменную (если вы это еще не сделали). Имя переменной — $lb. Используйте имена из ресурса балансировщика нагрузки, созданного ранее.

	$lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### Шаг 2. 

Загрузите в переменную конфигурацию серверной части.

	$backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### Шаг 3. 

Загрузите в переменную созданный ранее сетевой интерфейс. Имя переменной — $nic. Имя сетевого интерфейса совпадает с именем в приведенном выше примере.

	$nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### Шаг 4.

Измените конфигурацию серверной части в сетевом интерфейсе.

	PS C:\> $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### Шаг 5 

Сохраните объект сетевого интерфейса.

	PS C:\> Set-AzureRmNetworkInterface -NetworkInterface $nic

После того как сетевой интерфейс будет добавлен в пул серверной части балансировщика нагрузки, он начнет получать сетевой трафик согласно правилам балансировки нагрузки для соответствующего ресурса балансировщика.

## Обновление существующего балансировщика нагрузки


### Шаг 1

Используя балансировщик нагрузки из предыдущего примера, присвойте переменной $slb ссылку на объект балансировщика нагрузки, используя метод Get-AzureRmLoadBalancer.

	$slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Шаг 2

В следующем примере вы добавите новое входящее правило NAT для порта 81 на клиентской части и порта 8181 на серверной части, которое будет применяться к пулу существующего балансировщика нагрузки.

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Шаг 3.

Сохраните новую конфигурацию, используя командлет Set-AzureLoadBalancer.

	$slb | Set-AzureRmLoadBalancer

## Удалите балансировщика нагрузки.

Воспользуйтесь командой Remove-AzureRmLoadBalancer, чтобы удалить ранее созданного балансировщика нагрузки с именем NRP-LB в группе ресурсов NRP RG.

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Чтобы пропустить подтверждение удаления, можно использовать необязательный ключ -Force.



## Дальнейшие действия

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_0128_2016-->