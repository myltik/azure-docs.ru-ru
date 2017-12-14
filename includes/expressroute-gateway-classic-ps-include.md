Прежде чем приступить к работе над следующими задачами, необходимо создать виртуальную сеть и подсеть шлюза.

> [!NOTE]
> Эти примеры не применяются при одновременном использовании конфигураций S2S и ExpressRoute.
> Дополнительные сведения о работе со шлюзами при одновременном использовании конфигураций см. в разделе о [настройке параллельных подключений](../articles/expressroute/expressroute-howto-coexist-classic.md#gw).

## <a name="add-a-gateway"></a>Добавление шлюза

Используйте следующую команду, чтобы создать шлюз. Не забудьте подставить собственные значения.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Проверка создания шлюза

Используйте следующую команду, чтобы проверить, был ли создан шлюз. Эта команда также получает идентификатор шлюза, который нужен для других операций.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Изменение размера шлюза

Существует несколько [номеров SKU шлюзов](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Чтобы изменить SKU шлюза в любое время, можно использовать следующую команду.

> [!IMPORTANT]
> Эта команда не работает для шлюза UltraPerformance. Чтобы заменить шлюз на UltraPerformance, сначала удалите существующий шлюз ExpressRoute, а затем создайте шлюз UltraPerformance. Чтобы заменить шлюз UltraPerformance обычным шлюзом, сначала удалите шлюз UltraPerformance, а затем создайте новый шлюз. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Удаление шлюза

Используйте следующую команду, чтобы удалить шлюз.

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```