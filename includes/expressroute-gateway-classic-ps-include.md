Прежде чем приступить к работе над следующими задачами, необходимо создать виртуальную сеть и подсеть шлюза. Дополнительные сведения см. в статье [Настройка виртуальной сети с помощью классического портала](../articles/expressroute/expressroute-howto-vnet-portal-classic.md).

## Добавление шлюза

Используйте следующую команду, чтобы создать шлюз. Не забудьте подставить собственные значения.

	New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard

## Проверка создания шлюза

Используйте следующую команду, чтобы проверить, был ли создан шлюз. Эта команда также получает идентификатор шлюза, который нужен для других операций.

	Get-AzureVirtualNetworkGateway

## Изменение размера шлюза

Существует три [SKU шлюза](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md). Чтобы изменить SKU шлюза в любое время, можно использовать следующую команду.

	Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## Удаление шлюза

Используйте следующую команду, чтобы удалить шлюз.

	Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>

<!---HONumber=AcomDC_0413_2016-->