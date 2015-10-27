## Создание виртуальной сети с помощью PowerShell
Чтобы создать виртуальную сеть с помощью PowerShell, выполните указанные ниже действия.

1. Если вы ранее не использовали Azure PowerShell, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md). Войдите в Azure и выберите подписку.
	
3. При необходимости создайте новую группу ресурсов, как показано ниже. В нашем случае нужно создать группу ресурсов с именем *TestRG*. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md).

		New-AzureResourceGroup -Name TestRG -Location centralus

	Ожидаемые выходные данные:
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG	

4. Создайте новую виртуальную сеть с именем *TestVNet*, как показано ниже.

		New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	Ожидаемые выходные данные:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : []

5. Сохраните объект виртуальной сети в переменной, как показано ниже.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP]Можно объединить шаги 4 и 5, выполнив команду **$vnet = New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.

6. Добавьте подсеть к переменной новой виртуальной сети, как показано ниже.

		Add-AzureVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	Ожидаемые выходные данные:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": null,
		                        "Id": null,
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": null,
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": null
		                      }
		                    ]

7. Повторите шаг 6 выше для каждой подсети, которую необходимо создать. Приведенная ниже команда создает подсеть *BackEnd* для нашего сценария.

		Add-AzureVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Несмотря на создание подсетей, в настоящее время они существуют только в локальной переменной, используемой для получения виртуальной сети, созданной на шаге 4. Чтобы сохранить изменения в Azure, выполните командлет **Set-AzureVirtualNetwork**, как показано ниже.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet	
		
	Ожидаемые выходные данные:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": []
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Oct15_HO4-->