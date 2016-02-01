<properties 
   pageTitle="Связывание виртуальных сетей с каналами ExpressRoute | Microsoft Azure"
   description="В этой статье кратко описывается процедура связывания виртуальных сетей с каналами ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="ganesr" />

# Связывание виртуальных сетей с каналами ExpressRoute

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)
- [Template - Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

В этой статье приводится краткое описание связывания виртуальных сетей с каналами ExpressRoute. Виртуальные сети могут быть в той же или другой подписке. Процедуры в этой статье относятся к виртуальным сетям, развернутым с помощью модели развертывания диспетчера ресурсов. Если вам нужно дать ссылку на виртуальную сеть, развернутую по классической модели развертывания, обратитесь к статье [Привязка виртуальной сети к каналу ExpressRoute](expressroute-howto-linkvnet-classic.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Предварительные требования для настройки

- Вам потребуются последние модули Azure PowerShell версии 1.0 или более поздней. 
- Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
- Вам потребуется активный канал ExpressRoute. Перед тем как продолжить, [создайте канал ExpressRoute](expressroute-howto-circuit-classic.md) и включите его на стороне поставщика услуг подключения. Для выполнения описанных ниже командлетов канал ExpressRoute должен быть подготовлен и включен.
- Вам потребуется активный канал ExpressRoute. 
	- Следуйте инструкциям, чтобы [создать канал ExpressRoute](expressroute-howto-circuit-arm.md) и включить его на стороне поставщика услуг подключения. 
	- Убедитесь, что для вашего канала настроен частный пиринг Azure. Инструкции по маршрутизации см. в статье [Настройка маршрутизации](expressroute-howto-routing-arm.md). 
	- Для создания сквозного подключения вам потребуется настроить частный пиринг Azure, а также пиринг BGP между вашей сетью и сетью Майкрософт.
	- Вам необходимо иметь созданные и полностью подготовленные виртуальную сеть и шлюз виртуальной сети. Следуйте инструкциям по созданию [VPN-шлюза](../articles/vpn-gateway-create-site-to-site-rm-powershell.md).

С каналом ExpressRoute можно связать до 10 виртуальных сетей. Все каналы ExpressRoute должны находиться в одном геополитическом регионе. При использовании надстройки Premium вы сможете связать больше виртуальных сетей с каналом ExpressRoute. Подробная информация о надстройке Premium приведена в статье [Вопросы и ответы](expressroute-faqs.md).

## Связывание виртуальной сети и канала ExpressRoute из одной и той же подписки Azure

Вы можете связать шлюз виртуальной сети с каналом ExpressRoute, используя следующий командлет. Убедитесь в наличии шлюза виртуальной сети и его готовности к связыванию, прежде чем запускать командлет.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## Связывание виртуальной сети и канала ExpressRoute из разных подписок Azure

Один канал ExpressRoute может одновременно использоваться несколькими подписками. На рисунке ниже схематично показан способ совместного использования каналов ExpressRoute несколькими подписками. Каждое маленькое облако внутри большого облака представляет подписки, принадлежащие различным подразделениям одной организации. Любое подразделение в организации может использовать свою собственную подписку для развертывания служб. Кроме того, оно может совместно использовать один выделенный канал ExpressRoute для подключения к корпоративной сети. Владельцем канала ExpressRoute может выступать одно подразделение (в данном примере — ИТ-подразделение). Другие подписки в организации могут использовать канал ExpressRoute.

>[AZURE.NOTE]Плата за подключение выделенного канала ExpressRoute и использование полосы пропускания будет взиматься с владельца выделенного канала. Полоса пропускания распределяется между всеми виртуальными сетями.

![Подключение между подписками](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Администрирование

*Владелец канала* является авторизованным администратором ресурса канала ExpressRoute. Владелец канала может создавать разрешения, которые могут быть активированы *пользователями канала*. *Пользователи канала* являются владельцами шлюзов виртуальной сети (которые находятся в разных подписках с каналом ExpressRoute). *Пользователи канала* могут активировать разрешения (по одному разрешению для каждой виртуальной сети).

*Владелец канала* имеет право изменить или отменить разрешение в любое время. Отмена разрешения приводит к удалению всех ссылок из подписки, доступ к которой был отменен.

### Действия владельца канала 

#### Создание разрешения
	
Владелец канала создает разрешение. Это приводит к созданию ключа разрешения, который может использоваться пользователем канала для подключения шлюзов виртуальной сети к каналу ExpressRoute. Разрешение действительно только для одного подключения.

В следующем фрагменте показано создание разрешение с помощью командлета.

		Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
		$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

		$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		

Ответ будет содержать ключ и состояние разрешения.

		Name                   : MyAuthorization1
		Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
		Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
		AuthorizationKey       : ####################################
		AuthorizationUseStatus : Available
		ProvisioningState      : Succeeded

		

#### Просмотр разрешений

Владелец канала может просмотреть все разрешения, выданные для определенного канала, выполнив следующий командлет.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
	

#### Добавление разрешений

Владелец канала может добавлять разрешения с помощью следующего командлета.

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

	
#### Удаление разрешений

Владелец канала может отзывать (удалять) разрешения, выданные пользователю, с помощью следующего командлета.

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### Действия пользователя канала

Пользователь канала должен получить от владельца канала идентификатор однорангового узла и ключ разрешения. Ключ канала выглядит, как показано ниже:


Ключ разрешения представляет собой идентификатор GUID.

#### Активация разрешений на подключение

Пользователь канала может активировать разрешение на связь, запустив следующий командлет.

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### Освобождение разрешений на подключение

Разрешение можно освободить, удалив подключение, связывающее канал ExpressRoute и виртуальную сеть.

## Дальнейшие действия

Дополнительную информацию об ExpressRoute см. в статье [Часто задаваемые вопросы об ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0121_2016-->