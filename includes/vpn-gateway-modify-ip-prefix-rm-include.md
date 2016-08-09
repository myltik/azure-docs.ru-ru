### <a name="noconnection"></a>Добавление или удаление префиксов без подключения через VPN-шлюз

- Чтобы **добавить** дополнительные префиксы адресов для созданного локального сетевого шлюза, у которого пока нет подключения через VPN-шлюз, используйте следующий пример.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- Чтобы **удалить** префикс адреса локального сетевого шлюза без VPN-подключения, используйте следующий пример. Не указывайте префиксы, которые больше не нужны. В данном примере нам больше не нужен префикс 20.0.0.0/24 (из предыдущего примера), поэтому мы обновим сетевой шлюз и исключим этот префикс.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Добавление или удаление префиксов при подключении через VPN-шлюз

Если вы уже создали VPN-подключение и желаете добавить или удалить префиксы IP-адресов, указанные для вашего локального сетевого шлюза, вам следует в правильной последовательности выполнить следующие шаги. При этом VPN-подключение будет некоторое время недоступно. При обновлении префиксов сначала следует удалить подключение, изменить префиксы, а затем создать новое подключение.

>[AZURE.IMPORTANT] Не удаляйте VPN-шлюз. В противном случае вам придется заново выполнить все действия, чтобы восстановить его, а также повторно настроить локальный маршрутизатор с новыми параметрами.
 
1. Укажите переменные.

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

2. Удалите подключение.

		Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

3. Измените префиксы.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Создайте подключение. В этом примере мы настраиваем тип подключения IPsec. Дополнительные типы подключений см. на странице с [командлетами PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).
	
		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
		-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec `
		-RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0803_2016-->