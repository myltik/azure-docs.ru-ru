### Проверка подключения с помощью PowerShell

Убедиться в успешном выполнении подключения можно с помощью командлета `Get-AzureRmVirtualNetworkGatewayConnection` с параметром `-Debug` или без него.

1. Используйте командлет из следующего примера, подставив свои значения. При появлении запроса выберите "A", чтобы выполнить команду All (Все). В примере `-Name` ссылается на имя созданного подключения, которое требуется проверить.

		Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. После завершения работы командлета просмотрите результаты. В следующем примере показано, что подключение установлено (состояние "Подключено"), а также указан объем полученных и отправленных данных в байтах.

		Body:
		{
		  "name": "MyGWConnection",
		  "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
		  "properties": {
		    "provisioningState": "Succeeded",
		    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
		    "virtualNetworkGateway1": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
		teways/vnetgw1"
		    },
		    "localNetworkGateway2": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
		ways/LocalSite"
		    },
		    "connectionType": "IPsec",
		    "routingWeight": 10,
		    "sharedKey": "abc123",
		    "connectionStatus": "Connected",
		    "ingressBytesTransferred": 33509044,
		    "egressBytesTransferred": 4142431
		  }

### Проверка подключения с помощью портала Azure

Чтобы просмотреть состояние подключения на портале Azure, перейдите к нужному подключению. Это можно сделать несколькими способами. Ниже приведен один из способов перехода к подключению.

1. На [портале Azure](http://portal.azure.com) перейдите в раздел **Шлюзы виртуальной сети**. Щелкните имя нужного шлюза.
2. В области под **параметрами** щелкните **Подключения**. Вы увидите состояние каждого подключения.
3. Чтобы просмотреть дополнительные сведения о подключении, щелкните его имя. На странице основных компонентов подключения обратите внимание на **состояние подключения**. В случае успешного подключения будет отображаться состояние "Успешно" и "Подключено". Объем передаваемых данных можно просмотреть в разделах **Входящие данные** и **Исходящие данные**.

	В следующем примере для **состояния подключения** отображается значение "Не подключено".

	![Проверка подключения](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)

<!---HONumber=AcomDC_0810_2016-->