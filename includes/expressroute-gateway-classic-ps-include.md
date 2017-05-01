Прежде чем приступить к работе над следующими задачами, необходимо создать виртуальную сеть и подсеть шлюза. Дополнительные сведения см. в статье [Настройка виртуальной сети с помощью классического портала](../articles/expressroute/expressroute-howto-vnet-portal-classic.md).   

## <a name="add-a-gateway"></a>Добавление шлюза
Используйте следующую команду, чтобы создать шлюз. Не забудьте подставить собственные значения.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Проверка создания шлюза
Используйте следующую команду, чтобы проверить, был ли создан шлюз. Эта команда также получает идентификатор шлюза, который нужен для других операций.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Изменение размера шлюза
Существует несколько [номеров SKU шлюзов](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Чтобы изменить SKU шлюза в любое время, можно использовать следующую команду.

> [!IMPORTANT]
> Эта команда не работает для шлюза UltraPerformance. Чтобы заменить шлюз на UltraPerformance, сначала удалите существующий шлюз ExpressRoute, а затем создайте шлюз UltraPerformance. Чтобы заменить шлюз UltraPerformance обычным шлюзом, сначала удалите шлюз UltraPerformance, а затем создайте новый шлюз. 
> 
> 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Удаление шлюза
Используйте следующую команду, чтобы удалить шлюз.

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>