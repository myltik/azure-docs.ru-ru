## Создание виртуальной сети с помощью PowerShell

Чтобы создать виртуальную сеть с помощью PowerShell, выполните указанные ниже действия.

1. Если вы ранее не использовали Azure PowerShell, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md). Войдите в Azure и выберите подписку.
2. В командной строке Azure PowerShell выполните командлет **Switch-AzureMode**, чтобы включить режим диспетчера ресурсов, как показано ниже.

	Switch-AzureMode AzureResourceManager

	ПРЕДУПРЕЖДЕНИЕ. Командлет Switch-AzureMode является устаревшим и будет удален в следующих версиях.

	>[AZURE.WARNING]Командлет Switch-AzureMode является устаревшим. По этой причине все командлеты диспетчера ресурсов вскоре будут переименованы.
	
3. При необходимости выполните командлет **New-AzureResourceGroup**, чтобы создать новую группу ресурсов, как показано ниже. В нашем сценарии необходимо создать группу ресурсов с именем *RG1*.

	New-AzureResourceGroup -Name RG1 -Location centralus

	ResourceGroupName : RG1 Location : centralus ProvisioningState : Succeeded Tags : Permissions : Actions NotActions ======= ========== *
	
	ResourceId : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1

4. Выполните командлет **New-AzureVirtualNetwork**, чтобы создать виртуальную сеть, как показано ниже.

	New-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet ` -AddressPrefix 192.168.0.0/16 -Location centralus
	
	Name : TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"5b89894f-db7f-4634-9870-f9b97e209510" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": null } NetworkInterfaces : null Subnets :

5. Выполните командлет **Get-AzureVirtualNetwork**, чтобы сохранить объект виртуальной сети в переменной, как показано ниже.

	$vnet = Get-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet

	>[AZURE.TIP]Можно объединить шаги 4 и 5, выполнив команду **$vnet = New-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.

6. Выполните командлет **Add-AzureVirtualNetworkSubnetConfig**, чтобы добавить подсеть в новую виртуальную сеть, как показано ниже.

	Add-AzureVirtualNetworkSubnetConfig -Name FrontEnd ` -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
	
	Name : TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"5b89894f-db7f-4634-9870-f9b97e209510" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": null } NetworkInterfaces : null Subnets : [ { "Name": "FrontEnd", "Etag": null, "Id": null, "AddressPrefix": "192.168.1.0/24", "IpConfigurations": null, "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": null } ]

7. Повторите шаг 6 выше для каждой подсети, которую необходимо создать. Приведенная ниже команда создает подсеть *BackEnd* для нашего сценария.

	Add-AzureVirtualNetworkSubnetConfig -Name BackEnd ` -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Несмотря на создание подсетей, в настоящее время они существуют только в локальной переменной, используемой для получения виртуальной сети, созданной на шаге 4. Чтобы сохранить изменения в Azure, выполните командлет **Set-AzureVirtualNetwork**, как показано ниже.

	Set-AzureVirtualNetwork -VirtualNetwork $vnet
	
	Name : TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"2d3496d8-2b85-4238-bde2-377fe660aa4a" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": } NetworkInterfaces : null Subnets : [ { "Name": "FrontEnd", "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"", "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Micro soft.Network/virtualNetworks/TestVNet/subnets/FrontEnd", "AddressPrefix": "192.168.1.0/24", "IpConfigurations": , "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": "Succeeded" }, { "Name": "BackEnd", "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"", "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Micro soft.Network/virtualNetworks/TestVNet/subnets/BackEnd", "AddressPrefix": "192.168.2.0/24", "IpConfigurations": , "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": "Succeeded" } ]

<!---HONumber=August15_HO8-->