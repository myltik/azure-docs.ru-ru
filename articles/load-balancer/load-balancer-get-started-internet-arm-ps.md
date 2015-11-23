<properties 
   pageTitle="Создание балансировщика нагрузки для Интернета в диспетчере ресурсов с помощью PowerShell | Microsoft Azure"
   description="Узнайте, как создать балансировщик нагрузки для Интернета в диспетчере ресурсов с помощью PowerShell."
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
   ms.date="10/21/2015"
   ms.author="joaoma" />

# Начало работы по созданию балансировщика нагрузки для Интернета в диспетчере ресурсов с помощью PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье описывается модель развертывания с использованием менеджера ресурсов. Если вас интересует классическая модель развертывания Azure, перейдите к статье [Начало работы по созданию балансировщика нагрузки для Интернета с помощью классического развертывания](load-balancer-get-started-internet-classic-portal.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Ниже описана процедура создания подсистемы балансировки нагрузки для Интернета с помощью диспетчера ресурсов Azure и Azure PowerShell. Диспетчер ресурсов Azure позволяет по отдельности настраивать элементы, необходимые для создания подсистемы балансировки нагрузки для Интернета, после чего на их основе создается единый ресурс.

На этой странице приводится порядок выполнения отдельных операций, которые требуется выполнить для создания подсистемы балансировки нагрузки, а также подробно объясняется, что необходимо сделать для решения задачи создания подсистемы балансировки нагрузки.

## Что необходимо для создания подсистемы балансировки нагрузки для Интернета?

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты:

- Конфигурация IP-адресов клиентской части. Содержит общедоступные IP-адреса для входящего сетевого трафика. 

- Пул адресов серверной части. Содержит сетевые интерфейсы (сетевые адаптеры) для получения трафика от балансировщика нагрузки.

- Правила балансировки нагрузки. Содержат правила сопоставления общего порта в балансировщике нагрузки с портами на сетевых адаптерах в пуле адресов серверной части.

- Правила преобразования сетевых адресов (NAT) для входящего трафика. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом на отдельной сетевой карте в пуле адресов серверной части.

- Пробы. Содержат пробы работоспособности, с помощью которых можно проверить доступность виртуальных машин, связанных с сетевыми адаптерами в пуле адресов серверной части.

Дополнительные сведения о настройке компонентов балансировщика нагрузки с помощью диспетчера ресурсов Azure см. в статье [Поддержка диспетчера ресурсов Azure для балансировщика нагрузки](load-balancer-arm.md).


## Настройка PowerShell для использования диспетчера ресурсов
Убедитесь, что у вас установлена последняя рабочая версия модуля Azure для PowerShell и PowerShell правильно настроена для доступа к подписке Azure.

### Шаг 1

1. Если вы ранее не использовали Azure PowerShell, следуйте указаниям в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md) до этапа входа в Azure и выбора подписки.
2. В командной строке Azure PowerShell выполните командлет **Switch-AzureMode**, чтобы включить режим диспетчера ресурсов, как показано ниже.

		Switch-AzureMode AzureResourceManager
	
	Ожидаемые выходные данные:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]Командлет Switch-AzureMode является устаревшим. По этой причине все командлеты диспетчера ресурсов вскоре будут переименованы.



### Шаг 2

Войдите в свою учетную запись Azure.


    PS C:\> Add-AzureAccount

Вам будет предложено указать свои учетные данные для проверки подлинности.


### Шаг 3.

Выберите подписку Azure.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Чтобы просмотреть перечень доступных подписок, воспользуйтесь командлетом Get-AzureSubscription.

## Создание группы ресурсов

Создайте новую группу ресурсов с именем *NRP-RG* в расположении Azure *Запад США*.

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

## Создание виртуальной сети и общедоступного IP-адреса для пула IP-адресов клиентской части

### Шаг 1

Создайте подсеть и виртуальную сеть.

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Шаг 2

Создайте общедоступный IP-адрес *PublicIP*, который будет использоваться пулом IP-адресов клиентской части, с DNS-именем *loadbalancernrp.westus.cloudapp.azure.com*. В команде ниже используется статическое выделение.

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]Балансировщик нагрузки будет использовать метку домена общедоступного IP-адреса в качестве своего полного доменного имени (FQDN). В этом заключается отличие от классической модели развертывания, где в качестве полного доменного имени балансировщика нагрузки используется облачная служба. В этом примере используется полное доменное имя *loadbalancernrp.westus.cloudapp.azure.com*.

## Создание пула IP-адресов клиентской части и пула адресов серверной части

### Шаг 1 

Создайте пул IP-адресов клиентской части с именем *LB-Frontend*, использующий общедоступный IP-адрес *PublicIp*.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Шаг 2. 

Создайте пул адресов серверной части с именем *LB-backend*.

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## Создание правил балансировки нагрузки, правил преобразования сетевых адресов, пробы и балансировщика нагрузки

В примере ниже создаются следующие элементы:

- правило NAT, которое направляет весь входящий трафик с порта 3441 на порт 3389<sup>1</sup>;
- правило NAT, которое направляет весь входящий трафик с порта 3442 на порт 3389;
- правило балансировщика нагрузки, которое балансирует весь входящий трафик на порту 80, перенаправляя трафик на порт 80 других адресов в пуле серверной части;
- правило пробы, согласно которому будет проверяться состояние работоспособности на странице *HealthProbe.aspx*.
- балансировщик нагрузки, который использует все объекты выше.


<sup>1</sup> Правила NAT сопоставлены с конкретным экземпляром виртуальной машины, находящимся в зоне действия балансировщика нагрузки. Входящий сетевой трафик на порт 3341 будет отправляться в конкретную виртуальную машину через порт 3389, сопоставленный с правилом NAT в приведенном ниже примере. Необходимо выбрать для правила NAT протокол UDP или TCP. Нельзя назначить оба протокола одному и тому же порту.

### Шаг 1

Создайте правила NAT.

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Шаг 2

Создайте правило балансировщика нагрузки.

	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Шаг 3.

Создайте пробу работоспособности.

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Шаг 4.

Создайте балансировщик нагрузки с помощью объектов, созданных ранее.

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## Создание сетевых адаптеров

Вам необходимо создать сетевые адаптеры (или изменить существующие) и связать их с правилами NAT, правилами балансировщика нагрузки и пробами.

### Шаг 1 

Получите виртуальную сеть и подсеть, где нужно создать сетевые адаптеры.

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Шаг 2

Создайте сетевую карту *lb-nic1-be* и сопоставьте ее с первым правилом NAT, а также с первым (и единственным) пулом адресов серверной части.
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Шаг 3.

Создайте сетевую карту *lb-nic2-be* и сопоставьте ее со вторым правилом NAT, а также с первым (и единственным) пулом адресов серверной части.

	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Шаг 4.

Проверьте сетевые адаптеры.


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



### Шаг 5

С помощью командлета `Add-AzureVMNetworkInterface` назначьте сетевые карты различным виртуальным машинам.

Инструкции по созданию виртуальной машины и назначению сетевой карты см. в статье [Создание и предварительная настройка виртуальной машины Windows с помощью диспетчера ресурсов и Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) (вариант 5).

## Обновление существующего балансировщика нагрузки


### Шаг 1

Используя балансировщик нагрузки из предыдущего примера, присвойте переменной $slb ссылку на объект балансировщика нагрузки, используя метод Get-AzureLoadBalancer.

	$slb=get-azureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Шаг 2

В следующем примере вы добавите новое входящее правило NAT для порта 81 на клиентской части и порта 8181 на серверной части, которое будет применяться к пулу существующего балансировщика нагрузки.

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Шаг 3.

Сохраните новую конфигурацию, используя командлет Set-AzureLoadBalancer.

	$slb | Set-AzureLoadBalancer

## Удалите балансировщика нагрузки.

Воспользуйтесь командой Remove-AzureLoadBalancer, чтобы удалить ранее созданного балансировщика нагрузки с именем NRP-LB в группе ресурсов NRP RG.

	Remove-AzureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]Чтобы пропустить подтверждение удаления, можно использовать необязательный ключ -Force.

## Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-internal-getstarted.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->