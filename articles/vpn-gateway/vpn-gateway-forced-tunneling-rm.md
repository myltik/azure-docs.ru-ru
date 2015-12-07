<properties pageTitle="Configure Forced Tunneling for VPN Gateways using Resource Manager | Microsoft Azure" description="If you have a virtual network with a cross-premises VPN Gateway, you can redirect or "force" all Internet-bound traffic back to your on-premises location. This article applies to the Resource Manager deployment model. " services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carolz" editor="" tags="azure-resource-manager"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/17/2015"
   ms.author="cherylmc" />

# Настройка принудительного туннелирования с помощью PowerShell и диспетчера ресурсов Azure

Сведения в этой статье применимы к виртуальным сетям и VPN-шлюзам, созданным с использованием модели развертывания диспетчера ресурсов. Сведения о настройке принудительного туннелирования с помощью модели развертывания "Управление службами" см. в разделе [Настройка принудительного туннелирования](vpn-gateway-about-forced-tunneling.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Сведения о принудительном туннелировании

Принудительное туннелирование позволяет перенаправлять или "принудительно направлять" весь Интернет-трафик обратно в локальное расположение через VPN типа "сеть-сеть" для проверки и аудита. Это критически важное требование безопасности, имеющееся в большинстве корпоративных ИТ-политик. Без принудительного туннелирования Интернет-трафик из виртуальных машин в Azure будет всегда поступать из инфраструктуры сети Azure непосредственно в Интернет, без возможности его проверки или аудита. Неавторизованный доступ в Интернет может привести к раскрытию информации или другим нарушениям безопасности.

На схеме ниже показан принцип работы принудительного туннелирования.

![Принудительное туннелирование](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

В примере выше для интерфейсной подсети не применяется принудительное туннелирование. Рабочие нагрузки в интерфейсной подсети могут продолжать принимать запросы клиентов непосредственно из Интернета и отвечать на них. Для подсетей среднего уровня и внутренних подсетей применяется принудительное туннелирование. Любые исходящие подключения из этих двух подсетей к Интернету будут принудительно перенаправлены обратно на локальный сайт через один из VPN-туннелей, работающих по протоколу S2S. Это позволяет ограничивать и проверять доступ в Интернет с виртуальных машин или облачных служб в Azure, при этом поддерживая необходимую многоуровневую архитектуру служб. Кроме того, вы можете применять принудительное туннелирование для всех виртуальных сетей, если в них нет рабочих нагрузок, требующих взаимодействия с Интернетом.

## Требования и рекомендации

В Azure принудительное туннелирование настраивается с помощью определяемых пользователем маршрутов виртуальной сети. Перенаправление трафика на локальный сайт выполняется с помощью маршрута по умолчанию к VPN-шлюзу Azure. Сведения об определяемых пользователем маршрутах см. в статье [Определяемые пользователем маршруты и перенаправление IP-адресов](../virtual-network/virtual-networks-udr-overview.md).

- Каждая подсеть виртуальной сети имеет встроенные системные таблицы маршрутизации. Системная таблица маршрутизации имеет указанные ниже 3 группы маршрутов.

	- **Маршруты локальной виртуальной сети.** Они ведут непосредственно к виртуальным машинам назначения в той же виртуальной сети.
	
	- **Локальные маршруты.** Ведут к VPN-шлюзу Azure.
	
	- **Маршрут по умолчанию.** Ведет напрямую в Интернет. Обратите внимание, что пакеты, предназначенные для частных IP-адресов, не входящих в предыдущие два маршрута, будут потеряны.

-  В этой процедуре используются определяемые пользователем маршруты для создания таблицы маршрутизации с целью добавления маршрута по умолчанию и последующего сопоставления таблицы маршрутизации с подсетями вашей виртуальной сети для включения принудительного туннелирования в этих подсетях.

- Принудительное туннелирование должно быть связано с виртуальной сетью, в которой есть VPN-шлюз на основе маршрута. Из числа локальных межорганизационных сайтов, подключенных к виртуальной сети, необходимо выбрать "сайт по умолчанию".

- Обратите внимание, что с помощью этого механизма невозможно настроить принудительное туннелирование ExpressRoute. Такое туннелирование включается, когда предлагается маршрут по умолчанию с помощью сеансов пиринга BGP ExpressRoute. Дополнительные сведения см. в [документации по ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## Настройка принудительного туннелирования

### Общие сведения о настройке

Следующая процедура поможет создать группу ресурсов и виртуальную сеть. Затем вы создадите VPN-шлюз и настроите принудительное туннелирование.

В данном примере у виртуальной сети MultiTier-VNet имеются 3 подсети (*Frontend*, *Midtier* и *Backend*) с 4 межорганизационными подключениями *DefaultSiteHQ* и 3 *филиалами*. Выполнив указанные ниже действия, можно настроить подключение *DefaultSiteHQ* в качестве подключения к сайту по умолчанию для принудительного туннелирования и настроить принудительное туннелирование для подсетей *Midtier* и *Backend*.

	
### Подготовка

Перед началом настройки убедитесь, что у вас есть следующие компоненты.

- Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрироваться в бесплатной пробной версии](http://azure.microsoft.com/pricing/free-trial/).

- Последняя версия командлетов Azure PowerShell, использующая установщик веб-платформы. Можно загрузить и установить последнюю версию из [установщика веб-платформы](http://aka.ms/webpi-azps/). Это документация была написана для PowerShell 1.0 или более поздней версии. Командлеты, необходимые для этой конфигурации, отсутствуют в более ранних версиях. Дополнительные сведения о PowerShell 1.0 см. в публикации [Предварительная версия Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/).

- Если вы не знакомы с использованием диспетчера ресурсов Azure и PowerShell, дополнительные сведения см. в [этой статье](../articles/powershell-azure-resource-manager.md).

### Этапы настройки

1. В консоли PowerShell войдите в свою учетную запись Azure. Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он загружает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

		Login-AzureRmAccount 

2. Получите список подписок Azure.

		Get-AzureRmSubscription

2. Укажите имя подписки Azure.

		Get-AzureRmSubscription -SubscriptionName "YourSubscriptionName" | Select-AzureRmSubscription
		
3. Создайте группу ресурсов.

		New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Создайте виртуальную сеть и укажите подсети.

		$s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
		$s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
		$s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
		$s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
		$vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Создайте шлюзы локальной сети.

		$lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
		$lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
		$lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
		$lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
		
6. Создайте таблицу маршрутизации и правило маршрутизации.

		New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
		$rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
		Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
		Set-AzureRmRouteTable -RouteTable $rt


6. Сопоставьте таблицу маршрутизации с подсетями "Midtier" и "Backend".

		$vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
		Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

9. Создайте шлюза с узлом по умолчанию. Выполнение этого шага занимает некоторое время (иногда 20 минут или более), поскольку происходит процесс создания и настройки шлюза. Несмотря на то, что на консоли можно увидеть лишь несколько командлетов, целый ряд процедур выполняется в фоновом режиме. Обратите внимание, что параметр GatewayDefaultSite командлета используется для приведения в действие конфигурации принудительной маршрутизации, поэтому его нельзя пропускать. Он доступен только в PowerShell 1.0 или более поздней версии.

		$pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
		$gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
		New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

10. Создание VPN-подключений "сайт-сайт".

		$gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
		$lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
		$lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
		$lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
		$lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

		Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
		

<!---HONumber=AcomDC_1125_2015-->