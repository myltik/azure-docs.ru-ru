### <a name="to-verify-your-connection-by-using-powershell"></a>Проверка подключения с помощью PowerShell
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

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>Проверка подключения с помощью портала Azure
Чтобы просмотреть состояние подключения на портале Azure, перейдите к нужному подключению. Это можно сделать несколькими способами. Ниже показано, как перейти к подключению и проверить его.

1. На [портале Azure](http://portal.azure.com) щелкните **Все ресурсы** и перейдите к шлюзу виртуальной сети.
2. В колонке шлюза виртуальной сети щелкните **Подключения**. Вы увидите состояние каждого подключения.
3. Чтобы открыть вкладку **Основные компоненты**, щелкните имя подключения, которое необходимо проверить. На вкладке "Основные компоненты" можно просмотреть дополнительные сведения о подключении. В случае успешного подключения будет отображаться **состояние** "Успешно" и "Подключено".
   
    ![Проверка подключения](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)

<!--HONumber=Oct16_HO2-->


