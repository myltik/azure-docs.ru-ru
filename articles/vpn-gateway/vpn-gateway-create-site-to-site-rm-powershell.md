<properties
   pageTitle="Создание виртуальной сети с VPN-подключением типа сеть-сеть с помощью диспетчера ресурсов Azure и PowerShell | Microsoft Azure"
	description="Создание VPN-подключением типа сеть-сеть между виртуальной сетью и локальным расположением с помощью диспетчера ресурсов Azure и PowerShell"
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""
	tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/21/2015"
	ms.author="cherylmc"/>

# Создание виртуальной сети с VPN-подключением типа "сеть-сеть" с помощью диспетчера ресурсов Azure и PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


В этом разделе показано, как создать виртуальную сеть диспетчера ресурсов Azure и VPN-подключение типа "сеть-сеть" к локальной сети.

Azure в настоящее время поддерживает две модели развертывания: классическую и модель развертывания диспетчера ресурсов Azure. Процедура настройки подключения типа "сеть-сеть" зависит от модели, которая использовалась для развертывания виртуальной сети. Эти инструкции применимы к диспетчеру ресурсов. Если вы хотите создать VPN-подключение шлюза типа "сеть-сеть" с помощью классической модели развертывания, см. статью [Настройка виртуальной сети с VPN-подключением "сеть-сеть"](vpn-gateway-site-to-site-create.md).


## Перед началом работы

Перед началом работы убедитесь, что у вас есть следующие компоненты.

- Совместимое VPN-устройство (и пользователь, который может настроить его). См. [статью о VPN-устройствах](vpn-gateway-vpn-devices.md).
- Внешний общедоступный IP-адрес для VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT).
- Последняя версия командлетов Azure PowerShell. Вы можете скачать ее в разделе Windows PowerShell на [странице загрузки](http://azure.microsoft.com/downloads/), а затем установить. 
- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрироваться в бесплатной пробной версии](http://azure.microsoft.com/pricing/free-trial/).
	

## Подключение к подписке 


Откройте консоль PowerShell и переключитесь в режим диспетчера ресурсов Azure. Для подключения используйте следующий пример.

		Add-AzureAccount

Выполните командлет Select-AzureSubscription, чтобы подключиться к необходимой подписке.

		Select-AzureSubscription "yoursubscription"

Затем переключитесь в режим ARM. Переключение режима позволит использовать командлеты ARM.

		Switch-AzureMode -Name AzureResourceManager


## Создание виртуальной сети и подсети шлюза

- Если у вас уже есть виртуальная сеть с подсетью шлюза, вы можете перейти к разделу [Добавление локального сайта](#add-your-local-site). 
- Если у вас есть виртуальная сеть, к которой нужно добавить подсеть шлюза, перейдите к разделу [Добавление подсети шлюза к виртуальной сети](#gatewaysubnet).

### Создание виртуальной сети и подсети шлюза

Используйте приведенный ниже пример, чтобы создать виртуальную сеть и подсеть шлюза. Подставьте собственные значения.

Сначала создайте группу ресурсов.

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Затем создайте виртуальную сеть. В следующем примере создается виртуальная сеть с именем*testvnet*и две подсети: одна с именем*GatewaySubnet*, а другая — *Subnet1*. Важно создать одну подсеть именно с именем *GatewaySubnet*. Если вы используете другое имя, подключение не будет настроено.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>Добавление подсети шлюза к виртуальной сети

Если вы хотите добавить подсеть шлюза к существующей виртуальной сети, можно создать подсеть шлюза, используя следующий пример. Обязательно присвойте подсети шлюза имя "GatewaySubnet". Если вы используете другое имя, VPN-подключение не будет работать должным образом.


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## Добавление локального сайта

В виртуальной сети *локальный сайт* обычно ссылается на локальное расположение. Этому сайту необходимо присвоить имя, по которому на него может ссылаться служба Azure.

Нужно также указать префикс адресного пространства для локального сайта. С помощью указанного префикса IP-адреса служба Azure сможет определить, какой трафик отправлять на локальный сайт. Это значит, что вам нужно будет указать каждый префикс адреса, который необходимо связать с локальным сайтом. При изменении локальной сети вы с легкостью можете обновить эти префиксы. Для указания локального сайта используйте приведенные ниже примеры PowerShell.

	
- *GatewayIPAddress* — это IP-адрес локального VPN-устройства. Ваше VPN-устройство не может располагаться вне преобразования сетевых адресов (NAT). 
- *AddressPrefix* — это ваше локальное адресное пространство.

Чтобы добавить локальный сайт с одним префиксом адреса, используйте этот пример:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Чтобы добавить локальный сайт с несколькими префиксами адреса, используйте этот пример:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')


Чтобы добавить в уже созданный локальный сайт дополнительные префиксы адреса, используйте этот пример:

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


Чтобы удалить префикс адреса локального сайта, используйте следующий пример: Не указывайте префиксы, которые больше не нужны. В данном примере нам больше не нужен префикс 20.0.0.0/24 (из предыдущего примера), поэтому мы обновим локальный сайт и исключим этот префикс.

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')


## Запрос общедоступного IP-адреса для шлюза виртуальной сети

Затем вам понадобится запросить общедоступный IP-адрес, который нужно выделить для шлюза VPN виртуальной сети Azure. Он отличается от IP-адреса, назначенного VPN-устройству. Этот адрес присваивается шлюзу VPN Azure отдельно. Вы не можете указать необходимый IP-адрес, так как он динамически выделяется для шлюза. Этот IP-адрес будет использоваться при настройке локального VPN-устройства для подключения к шлюзу.

Используйте следующий пример PowerShell. Для этого адреса нужно использовать метод динамического распределения (Dynamic).

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## Создание конфигурации IP-адресации шлюза

Конфигурация шлюза определяет используемые подсеть и общедоступный IP-адрес. Используйте следующий пример, чтобы создать конфигурацию шлюза.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Создание шлюза

На этом шаге вы создадите шлюз виртуальной сети. Используйте следующие значения:

- Тип шлюза — *Vpn*.
- Тип шлюза VPN может основываться на маршрутах * (в некоторых документах такой шлюз называется шлюзом с динамической маршрутизацией) или *политиках* (такой шлюз также известен как шлюз со статической маршрутизацией). Дополнительные сведения о типах VPN-шлюзов см. в статье [VPN-шлюзы](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## Настройка устройства VPN

На этом этапе вам понадобится общедоступный IP-адрес шлюза виртуальной сети для настройки локального VPN-устройства. Обратитесь к производителю устройства, чтобы получить конкретные сведения о конфигурации. Кроме того, дополнительные сведения см. в разделе [VPN-устройства](http://go.microsoft.com/fwlink/p/?linkid=615099).

Чтобы найти общедоступный IP-адрес шлюза виртуальной сети, используйте следующий пример.

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## Создание VPN-подключения

Затем создайте VPN-подключение типа "сеть-сеть" между шлюзом виртуальной сети и локальным VPN-устройством. Обязательно подставьте собственные значения. Общий ключ должен соответствовать значению, использованному в конфигурации VPN-устройства.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Подключение должно установиться через несколько минут. На этом этапе VPN-подключения типа "сеть-сеть", созданные с помощью диспетчера ресурсов, не отображаются на портале.


## Дальнейшие действия

Добавьте виртуальную машину в виртуальную сеть. [Создание виртуальной машины](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=August15_HO9-->