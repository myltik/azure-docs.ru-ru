Чтобы изменить IP-адрес шлюза, используйте командлет New-AzureRmVirtualNetworkGatewayConnection. Сейчас командлет Set не поддерживает изменение IP-адреса шлюза.

### <a name="gwipnoconnection"></a>Изменение IP-адреса шлюза без подключения шлюза
Чтобы изменить IP-адрес шлюза для шлюза локальной сети, который еще не подключен, воспользуйтесь примером ниже. При этом вы также можете изменить префиксы адресов. Не забудьте указать существующее имя шлюза локальной сети для перезаписи текущих параметров. Иначе вы создадите новый шлюз локальной сети, а не перезапишете существующий.

Используйте следующий пример, подставив собственные значения:

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>Изменение IP-адреса шлюза с подключением шлюза
Если шлюз уже подключен, сначала вам нужно удалить подключение. Затем вы сможете изменить IP-адрес шлюза и создать новое подключение. При этом вы также можете изменить префиксы адресов. После этого VPN-подключение будет некоторое время недоступно.

> [!IMPORTANT]
> Не удаляйте VPN-шлюз. Если вы это сделаете, вам нужно вернуться назад, чтобы восстановить его. Кроме того, необходимо обновить локальное VPN-устройство, используя новый IP-адрес VPN-шлюза.
> 
> 

1. Удалите подключение. Вы можете найти имя своего подключения с помощью командлета Get-AzureRmVirtualNetworkGatewayConnection.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Измените значение GatewayIpAddress. При этом вы также можете изменить префиксы адресов. Не забудьте указать существующее имя шлюза локальной сети для перезаписи текущих параметров. Иначе вы создадите новый шлюз локальной сети, а не перезапишете существующий.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Создайте подключение. В этом примере мы настраиваем тип подключения IPsec. При повторном создании подключения используйте тип соединения, указанный для вашей конфигурации. Дополнительные типы подключений см. на странице с [командлетами PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).  Чтобы получить имя VirtualNetworkGateway, запустите командлет Get-AzureRmVirtualNetworkGateway.
   
    Задайте переменные.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Создайте подключение.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```