Если вам нужно изменить префиксы своего локального сайта, выполните следующие действия. Мы приводим два набора инструкций. Выбор между ними определяется тем, создано ли у вас подключение через VPN-шлюз.

### Добавление или удаление префиксов, если нет подключения через VPN-шлюз


- Чтобы **добавить** дополнительные префиксы адресов для созданного локального сайта, у которого пока нет подключения через VPN-шлюз, используйте следующий пример.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- Чтобы **удалить** префикс адреса локального сайта без VPN-подключения, используйте следующий пример. Не указывайте префиксы, которые больше не нужны. В данном примере нам больше не нужен префикс 20.0.0.0/24 (из предыдущего примера), поэтому мы обновим локальный сайт и исключим этот префикс.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Добавление или удаление префиксов, если есть подключение через VPN-шлюз

Если вы уже создали VPN-подключение и желаете добавить или удалить префиксы IP-адресов, указанные для вашего локального сайта, вам следует в правильной последовательности выполнить следующие шаги. При этом ваше VPN-подключение будет некоторое время недоступно, так как вы будете удалять и заново создавать шлюз. Но вам не потребуется повторно настраивать локальный VPN-маршрутизатор, если только вы не решите изменить ранее установленные значения, поскольку IP-адрес для подключения уже был запрошен.

 
1. Удалите подключения шлюза. 
2. Измените префиксы IP-адресов локального сайта. 
3. Создайте новое подключение шлюза. 

Если нужно, используйте следующий пример.


	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0107_2016-->