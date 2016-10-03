<properties 
   pageTitle="Сведения о параметрах VPN-шлюза для шлюзов виртуальной сети | Microsoft Azure"
   description="Узнайте о параметрах VPN-шлюза для виртуальной сети Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" />

# Сведения о параметрах VPN-шлюза

Решение, использующее подключения VPN-шлюза, зависит от конфигурации нескольких ресурсов, необходимых для передачи сетевого трафика между виртуальными сетями и локальными расположения. У каждого ресурса есть настраиваемые параметры. Сочетание этих ресурсов и параметров определяет результат подключения.

В разделах этой статьи рассматриваются ресурсы и параметры, относящиеся к VPN-шлюзу в модели развертывания с помощью **Resource Manager**. Возможно, вы захотите рассмотреть доступные конфигурации на примере схем топологий подключений. Описание и схему топологии для каждого варианта подключения можно найти в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).

## <a name="gwtype"></a>Типы шлюзов

У каждой виртуальной сети может быть только один шлюз виртуальной сети каждого типа. При создании шлюза виртуальной сети необходимо убедиться, что в конфигурации указан правильный тип шлюза.

Для -GatewayType доступны приведенные ниже значения.

- Vpn
- ExpressRoute

Для VPN-шлюза требуется `-GatewayType` *Vpn*.

Пример:

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased
 

## <a name="gwsku"></a>SKU шлюзов


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

**Указание SKU шлюза на портале Azure**

При использовании портала Azure для создания шлюза виртуальный сети Resource Manager по умолчанию для него указывается SKU Standard. На данный момент на портале Azure нельзя указать другой SKU для модели развертывания с помощью Resource Manager. Тем не менее после создания шлюз можно обновить до SKU более мощного шлюза. Например, с помощью командлета PowerShell `Resize-AzureRmVirtualNetworkGateway`, шлюз со SKU Basic или Standard можно обновить до HighPerformance. Можно также уменьшить размер шлюза, указав соответствующий SKU с помощью PowerShell.

**Указание SKU шлюза с помощью PowerShell**


В следующем примере PowerShell используется `-GatewaySku` со значением *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased

**Изменение SKU шлюза**

Можно изменить размер шлюза с помощью SKU. В следующем примере PowerShell показано изменение размера шлюза до SKU со значением HighPerformance.

	$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

<br>


###  <a name="aggthroughput"></a>Расчетная суммарная пропускная способность в зависимости от SKU и типа шлюза


В следующей таблице приведены типы шлюзов с приблизительной суммарной пропускной способностью. Эта таблица относится к классической модели развертывания и модели диспетчера ресурсов.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]


## <a name="connectiontype"></a>Типы подключения

В модели развертывания с помощью Resource Manager каждой конфигурации необходим определенный тип подключения шлюза виртуальной сети. Для `-ConnectionType` в PowerShell можно указать такие значения (развертывание Resource Manager):

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

В следующем примере PowerShell создается подключение типа "сеть — сеть", для которого требуется тип *IPsec*.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Типы VPN

При создании шлюза виртуальной сети для конфигурации VPN-шлюза необходимо указать тип VPN. Выбор типа VPN зависит от топологии подключений, которую вы хотите создать. Например, для подключения типа "точка — сеть" требуется тип VPN на основе маршрутов. Тип VPN может также зависеть от оборудования, которое будет использоваться. Для конфигураций "сеть — сеть" требуется VPN-устройство. Некоторые VPN-устройства поддерживают только определенный тип VPN.

Выбранный тип VPN должен удовлетворять всем требованиям к подключению решения, которое вы хотите создать. Например, если вы хотите создать подключение VPN-шлюза типа "сеть — сеть" и подключение VPN-шлюза типа "точка — сеть" для одной и той же виртуальной сети, то вам следует использовать тип VPN *RouteBased*, так как он необходим для подключения типа "точка — сеть". Необходимо также проверить, поддерживает ли ваше VPN-устройство VPN-подключение на основе маршрутов.

После создания шлюза виртуальной сети изменить тип VPN невозможно. Для этого потребуется удалить шлюз виртуальной сети и создать новый. Существует два типа VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


В следующем примере PowerShell используется `-VpnType` со значением *RouteBased*. При создании шлюза необходимо убедиться, что в конфигурации указано правильное значение -VpnType.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Требования к шлюзу

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Подсеть шлюза

Чтобы настроить шлюз виртуальной сети, сначала нужно создать подсеть шлюза для виртуальной сети. Чтобы подсети шлюзов работали правильно, каждую подсеть нужно назвать *GatewaySubnet*. Это имя указывает Azure, что данную подсеть следует использовать для шлюза.

Минимальный размер подсети шлюза зависит исключительно от того, какую конфигурацию вы хотите создать. Хотя можно создать подсеть шлюза всего лишь с размером /29, мы рекомендуем создавать подсети с размером /28 или больше (/28, /27, /26 и т. д.).

Создание шлюза большего размера предотвращает превышение ограничений его размера при работе. Например, вы создали шлюз виртуальной сети с размером подсети /29 для подключения типа "сеть — сеть". Теперь требуется конфигурацию сосуществования подключений типа "сеть — сеть" и ExpressRoute. Для такой конфигурации требуется подсеть шлюза с размером не менее /28. Чтобы создать конфигурацию, придется изменить подсеть шлюза для соблюдения минимальных требований к подключению, то есть настроить для нее размер /28.

В примере PowerShell для Resource Manager ниже показана подсеть шлюза GatewaySubnet. Здесь приведена нотация CIDR, указывающая размер /27, при котором можно использовать достаточно IP-адресов для большинства существующих конфигураций.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Убедитесь, что к подсети шлюза не применены какие-либо группы безопасности сети (NSG), так как из-за этого подключение может не работать.


## <a name="lng"></a>Локальные сетевые шлюзы

При создании конфигурации VPN-шлюза нередко шлюз локальной сети представляет ваше локальное расположение. В классической модели развертывания шлюз локальной сети называется "локальным сайтом".

Для локального сетевого шлюза следует задать имя и общий IP-адрес локального VPN-устройства, а также указать префиксы адресов, принадлежащие к локальному расположению. Azure проверяет наличие сетевого трафика по префиксам адресов назначения, учитывает конфигурацию, указанную для локального сетевого шлюза, и соответствующим образом направляет пакеты. Можно также указать шлюзы локальной сети для конфигураций типа "виртуальная сеть — виртуальная сеть", использующих подключение к VPN-шлюзу.

Следующий пример PowerShell создает шлюз локальной сети.

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Иногда возникает необходимость изменить параметры локального сетевого шлюза. Например, при добавлении или изменении диапазона адресов, либо при изменении IP-адреса VPN-устройства. Для классической виртуальной сети эти параметры можно изменить на классическом портале, на странице "Локальные сети". В случае использования Resource Manager ознакомьтесь с разделом [Изменение параметров шлюза локальной сети с помощью PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Интерфейсы REST API и командлеты PowerShell

Дополнительные технические материалы и специальные требования к синтаксису, действующие при использовании интерфейсов REST API и командлетов PowerShell для настройки конфигураций VPN-шлюзов, доступны на приведенных ниже страницах.

|**Классический** | **Диспетчер ресурсов**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## Дальнейшие действия

Дополнительные сведения о доступных конфигурациях подключений см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).







 

<!---HONumber=AcomDC_0921_2016-->