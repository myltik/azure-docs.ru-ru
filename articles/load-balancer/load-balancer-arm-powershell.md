.<properties
   pageTitle="Приступая к настройке подсистемы балансировки нагрузки для Интернета с помощью диспетчера ресурсов Azure | Microsoft Azure "
   description="Как создавать правила подсистемы балансировки нагрузки, правила NAT и пробы для диспетчера ресурсов Azure. Пошаговые инструкции по созданию ресурса подсистемы балансировки нагрузки."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# Приступая к настройке подсистемы балансировки нагрузки для Интернета с помощью диспетчера ресурсов Azure


> [AZURE.SELECTOR]
- [Руководство для классической модели Azure](load-balancer-internet-getstarted.md)
- [Руководство для Resource Manager PowerShell](load-balancer-arm-powershell.md)


Ниже описана процедура создания подсистемы балансировки нагрузки для Интернета с помощью диспетчера ресурсов Azure и Azure PowerShell. Диспетчер ресурсов Azure позволяет по отдельности настраивать элементы, необходимые для создания подсистемы балансировки нагрузки для Интернета, после чего на их основе создается единый ресурс.

На этой странице приводится порядок выполнения отдельных операций, которые требуется выполнить для создания подсистемы балансировки нагрузки, а также подробно объясняется, что необходимо сделать для решения задачи создания подсистемы балансировки нагрузки.


## Что необходимо для создания подсистемы балансировки нагрузки для Интернета?

Перед созданием подсистемы балансировки нагрузки необходимо настроить следующие элементы.

- Конфигурация внешних IP-адресов — добавление общедоступного IP-адреса к внешнему пулу IP-адресов для входящего сетевого трафика, позволяющее распределять нагрузку.

- Внутренний пул адресов — настройка сетевых интерфейсов, которые будут получать трафик с балансировкой нагрузки, поступающий из внешнего пула IP-адресов.

- Правила балансировки нагрузки — конфигурация исходного и локального портов для подсистемы балансировки нагрузки.

- Пробы — настройка проб для проверки состояния работоспособности экземпляров виртуальной машины.

- Входящие правила преобразования сетевых адресов (NAT) — настройка правил порта для прямого доступа к одному из экземпляров виртуальной машины.

Дополнительные сведения о настройке компонентов подсистемы балансировки нагрузки при помощи диспетчера ресурсов Azure см. в статье [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](load-balancer-arm.md).

Далее описана настройка балансировщика нагрузки для двух виртуальных машин.


## Пошаговая инструкция использования PowerShell


### Создание группы ресурсов для подсистемы балансировки нагрузки


### Шаг 1
Убедитесь, что выбран режим PowerShell для использования командлетов ARM. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).


    PS C:\> Switch-AzureMode -Name AzureResourceManager

### Шаг 2

Войдите в свою учетную запись Azure.


    PS C:\> Add-AzureAccount

Вам будет предложено указать свои учетные данные для проверки подлинности.


### Шаг 3.

Выберите подписку Azure.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Чтобы просмотреть перечень доступных подписок, воспользуйтесь командлетом Get-AzureSubscription.


### Шаг 4.

Создайте группу ресурсов (пропустите этот шаг, если вы используете существующую группу).

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется по умолчанию для ресурсов в этой группе. Убедитесь, что во всех командах создания подсистемы балансировки нагрузки используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов под названием «NRP RG» с расположением «Запад США».

## Создание виртуальной сети и общедоступного IP-адреса для пула IP-адресов клиентской части


### Шаг 1

Создайте виртуальную сеть:

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Создает подсеть для виртуальной сети и присваивает ее переменной $backendSubnet

	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Создает виртуальную сеть и добавляет подсеть lb-subnet-be к виртуальной сети NRPVNet.

### Шаг 2

Создайте общедоступный IP-адрес, который будет использоваться пулом IP-адресов клиентской части:

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Dynamic -DomainNameLabel lbip

>[AZURE.NOTE]Свойство label в имени домена общедоступного IP-адреса будет содержать префикс к полному доменному имени балансировщика нагрузки.

## Создание пула IP-адресов клиентской части и пула адресов серверной части

Настройка пула IP-адресов клиентской части для входящего сетевого трафика подсистемы балансировки нагрузки и пула адресов серверной части для получения распределенного сетевого трафика.

### Шаг 1

Создайте пул IP-адресов клиентской части, используя переменную общедоступного IP-адреса ($publicIP).

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP


### Шаг 2.

Настройте пул адресов серверной части для приема входящего трафика из пула IP-адресов клиентской части:

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Создание правил балансировки нагрузки, правил преобразования сетевых адресов, пробы и подсистемы балансировки нагрузки

После создания пула IP-адресов клиентской части и пула адресов серверной части необходимо создать правила, которые будут принадлежать ресурсу подсистемы балансировки нагрузки:

### Шаг 1

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


В вышеприведенном примере создаются следующие элементы.

- Входящее правило NAT для направления трафика с порта 3441 на порт 3389.
- Второе входящее правило NAT для направления трафика с порта 3442 на порт 3389.
- Правило балансировки нагрузки, согласно которому весь трафик, поступающий на общедоступный порт 80, будет распределяться на локальный порт 80 в пуле адресов серверной части.
- Правило пробы, согласно которому будет проверяться доступность пути «HealthProbe.aspx».



### Шаг 2

Создайте подсистему балансировки нагрузки, объединив все объекты (правила преобразования сетевых адресов, правила балансировки нагрузки, настройки проб):

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## Создание сетевых интерфейсов

После создания подсистемы балансировки нагрузки необходимо определить сетевые интерфейсы, к которым будут применяться правила распределения сетевого трафика, правила преобразования сетевых адресов и пробы. В этом случае сетевой интерфейс настраивается отдельно и может быть назначен виртуальной машине не сразу.


### Шаг 1.


Определите ресурс виртуальной сети и подсети для создания сетевых интерфейсов:

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


На этом шаге мы создаем сетевой интерфейс, которой будет принадлежать пулу серверной части подсистемы балансировки нагрузки, а затем назначаем первое правило NAT протоколу удаленного рабочего стола для данного сетевого интерфейса:

	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Шаг 2

Создайте второй сетевой интерфейс с названием «LB-Nic2-BE»:

На этом шаге мы создаем второй сетевой интерфейс, присваиваем его тому же пулу серверной части подсистемы балансировки нагрузки и связываем второе правило NAT с протоколом удаленного рабочего стола:

 	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


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

Используйте команду Add-AzureVMNetworkInterface, чтобы присвоить сетевую карту виртуальной машине.

Пошаговые инструкции по созданию виртуальной машины и назначению сетевой карты см. в статье [Создание и настройка виртуальной машины Windows с помощью диспетчера ресурсов и Azure PowerShell](../virtual-machines/virtual-machines-windows-create-powershell.md#Example) (вариант 4 или 5).

## Обновление существующего балансировщика нагрузки


### Шаг 1

Используя балансировщик нагрузки из предыдущего примера, присвойте переменной $slb ссылку на объект балансировщика нагрузки, используя метод Get-AzureLoadBalancer.

	$slb=get-azureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

### Шаг 2

В следующем примере вы добавите новое входящее правило NAT для порта 81 на клиентской части и порта 8181 на серверной части, которое будет применяться к пулу существующего балансировщика нагрузки.

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Шаг 3.

Сохраните новую конфигурацию, используя командлет Set-AzureLoadBalancer.

	$slb | Set-AzureLoadBalancer

## Удалите балансировщика нагрузки.

Воспользуйтесь командой Remove-AzureLoadBalancer, чтобы удалить ранее созданного балансировщика нагрузки с именем NRP-LB в группе ресурсов NRP RG.

	Remove-AzureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Чтобы пропустить подтверждение удаления, можно использовать необязательный ключ -Force.


## См. также

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->