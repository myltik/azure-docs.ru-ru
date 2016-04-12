### Добавление или удаление префиксов, если подключение через VPN-шлюз еще не создано

- Чтобы **добавить** дополнительные префиксы адресов для созданного локального сетевого шлюза, у которого пока нет подключения через VPN-шлюз, используйте следующий пример.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- Чтобы **удалить** префикс адреса локального сетевого шлюза без VPN-подключения, используйте следующий пример. Не указывайте префиксы, которые больше не нужны. В данном примере нам больше не нужен префикс 20.0.0.0/24 (из предыдущего примера), поэтому мы обновим сетевой шлюз и исключим этот префикс.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Добавление или удаление префиксов, если подключение через VPN-шлюз уже создано

Если вы уже создали VPN-подключение и желаете добавить или удалить префиксы IP-адресов, указанные для вашего локального сетевого шлюза, вам следует в правильной последовательности выполнить следующие шаги. При этом ваше VPN-подключение будет некоторое время недоступно, так как вы будете удалять и заново создавать шлюз. Но вам не потребуется повторно настраивать локальный VPN-маршрутизатор, если только вы не решите изменить ранее установленные значения, поскольку IP-адрес для подключения уже был запрошен.
 
1. Удалите подключения шлюза. 
2. Измените префиксы IP-адресов локального сетевого шлюза. 
3. Создайте новое подключение шлюза. 

Если нужно, используйте следующий пример.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0323_2016-->