Чтобы изменить IP-адрес шлюза, используйте командлет `New-AzureRmVirtualNetworkGatewayConnection`. Параметры будут перезаписаны, только если вы оставите для шлюза локальной сети существующее имя. Сейчас командлет Set не поддерживает изменение IP-адреса шлюза.

### <a name="gwipnoconnection"></a>Изменение IP-адреса шлюза без подключения шлюза

Ниже описано, как обновить IP-адрес шлюза для шлюза локальной сети, который еще не подключен. При этом вы также можете обновить префиксы адресов. Указав новые параметры, вы перезапишете существующие. Не забудьте указать существующее имя шлюза локальной сети. Иначе вы создадите новый шлюз локальной сети, а не перезапишете существующий.

Используйте следующий пример, подставив собственные значения.

	New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
	-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
	-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Изменение IP-адреса с подключением шлюза

Если шлюз уже подключен, вам сначала нужно удалить подключение. Затем вы сможете изменить IP-адрес шлюза и создать новое подключение. При этом VPN-подключение будет некоторое время недоступно.


>[AZURE.IMPORTANT] Не удаляйте VPN-шлюз. Иначе вам придется заново выполнить все действия, чтобы восстановить его, а также повторно настроить локальный маршрутизатор с IP-адресом, который будет назначен созданному шлюзу.
 

1. Удалите подключение. Имя подключения можно узнать с помощью командлета `Get-AzureRmVirtualNetworkGatewayConnection`.

		Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
		-ResourceGroupName MyRGName

2. Измените значение GatewayIpAddress. При необходимости вы сразу можете изменить префиксы адресов. Обратите внимание, что при этом будут перезаписаны существующие параметры шлюза локальной сети. Изменяя значения, используйте существующее имя шлюза локальной сети, чтобы перезаписать параметры. Иначе вы создадите новый шлюз локальной сети, а не измените существующий.

		New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
		-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
		-GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Создайте подключение. В этом примере мы настраиваем тип подключения IPsec. При повторном создании подключения используйте тип соединения, указанный для вашей конфигурации. Дополнительные типы подключений см. на странице с [командлетами PowerShell](https://msdn.microsoft.com/library/mt603611.aspx). Чтобы получить имя VirtualNetworkGateway, запустите командлет `Get-AzureRmVirtualNetworkGateway`.

	Присвойте переменные:

		$local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
		$vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

	Создайте подключение:
	
		New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
		-Location "West US" `
		-VirtualNetworkGateway1 $vnetgw `
		-LocalNetworkGateway2 $local `
		-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0810_2016-->