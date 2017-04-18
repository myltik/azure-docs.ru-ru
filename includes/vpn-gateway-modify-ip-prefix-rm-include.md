### <a name="noconnection"></a>Как добавлять и удалять префиксы (шлюз не подключен)
### <a name="to-add-additional-prefixes"></a>Добавление дополнительных префиксов

Чтобы добавить дополнительные префиксы адресов в созданный локальный сетевой шлюз, который еще не подключен, используйте приведенный ниже пример. Не забудьте указать собственные значения.

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
```
### <a name="to-remove-an-address-prefix"></a>Удаление префикса адреса

Чтобы удалить префикс адреса локального сетевого шлюза без VPN-подключения, используйте следующий пример. Не указывайте префиксы, которые больше не нужны. В данном примере нам больше не нужен префикс 20.0.0.0/24 (из предыдущего примера), поэтому мы обновим сетевой шлюз и исключим этот префикс.

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
```

### <a name="withconnection"></a>Как добавлять и удалять префиксы (шлюз подключен)
Если шлюз уже подключен и вам нужно добавить или удалить префиксы IP-адресов, указанные для локального сетевого шлюза, выполните приведенные ниже шаги именно в такой последовательности. При этом VPN-подключение будет некоторое время недоступно. При обновлении префиксов сначала следует удалить подключение, изменить префиксы, а затем создать новое подключение. В примерах ниже не забудьте указать собственные значения.

> [!IMPORTANT]
> Не удаляйте VPN-шлюз. В противном случае вам придется заново выполнить все действия, чтобы восстановить его, а также повторно настроить локальный маршрутизатор с новыми параметрами.
> 
> 

1. Удалите подключение.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. Измените префиксы адресов для локального сетевого шлюза.
   
  Задайте переменную для локального сетевого шлюза.

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  Измените префиксы.
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. Создайте подключение. В этом примере мы настраиваем тип подключения IPsec. При повторном создании подключения используйте тип соединения, указанный для вашей конфигурации. Дополнительные типы подключений см. на странице с [командлетами PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).
   
  Задайте переменную для шлюза виртуальной сети.

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  Создайте подключение. Обратите внимание, что в этом примере используется переменная $local, которую вы задали на предыдущем шаге.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```
