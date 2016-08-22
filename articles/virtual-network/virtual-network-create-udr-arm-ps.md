.<properties 
   pageTitle="Управление маршрутизацией и использование виртуальных модулей в диспетчере ресурсов с помощью PowerShell | Microsoft Azure"
   description="Узнайте, как управлять маршрутизацией и использовать виртуальные модули в диспетчере ресурсов с помощью PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" />

#Создание определяемых пользователем маршрутов (UDR) в диспетчере ресурсов с помощью PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [создавать определяемые пользователем маршруты на основе классической модели развертывания](virtual-network-create-udr-classic-ps.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Для приведенных ниже примеров команд PowerShell требуется уже созданная простая среда, основанная на приведенном выше сценарии. Чтобы выполнять команды в том виде, в котором они представлены в этом документе, сначала создайте тестовую среду, развернув [этот шаблон](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), щелкните **Deploy to Azure** (Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Создание определяемого пользователем маршрута для подсети переднего плана
Чтобы создать таблицу маршрутов и маршрут, необходимые для подсети переднего плана, на основании приведенного выше сценария, выполните следующие действия.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Создайте маршрут, используемый для отправки всего трафика, адресованного серверной подсети (192.168.2.0/24), в виртуальный модуль **FW1** (192.168.0.4).

		$route = New-AzureRmRouteConfig -Name RouteToBackEnd `
		    -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. Создайте таблицу маршрутов с именем **UDR-FrontEnd** в регионе **westus**, содержащем созданный ранее маршрут.

		$routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-FrontEnd -Route $route

5. Создайте переменную, которая содержит виртуальную сеть, где находится подсеть. В нашем случае такая виртуальная сеть называется **TestVNet**.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet

6. Сопоставьте созданную выше таблицу маршрутов с подсетью **FrontEnd**.
		
		Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -RouteTable $routeTable

>[AZURE.WARNING] Результаты выполнения представленной выше команды отображают содержимое объекта конфигурации виртуальной сети, который существует только на том компьютере, где выполняется PowerShell. Чтобы сохранить эти параметры в Azure, выполните командлет **Set-AzureVirtualNetwork**.

7. Сохраните новую конфигурацию подсети в Azure.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Ожидаемые выходные данные:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              : 
		                    Name         Value
		                    ===========  =====
		                    displayName  VNet 
		                    
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
								...,
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      },
								...
		                    ]	

## Создание определяемого пользователем маршрута для серверной подсети
Чтобы создать таблицу маршрутов и маршрут, необходимые для серверной подсети, на основании приведенного выше сценария, выполните следующие действия.

1. Создайте маршрут, используемый для отправки всего трафика, адресованного подсети переднего плана (192.168.1.0/24), в виртуальный модуль **FW1** (192.168.0.4).

		$route = New-AzureRmRouteConfig -Name RouteToFrontEnd `
		    -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. Создайте таблицу маршрутов с именем **UDR-BackEnd** в регионе **uswest**, содержащем созданный ранее маршрут.

		$routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-BackEnd -Route $route

5. Сопоставьте созданную выше таблицу маршрутов с подсетью **BackEnd**.

		Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -RouteTable $routeTable

7. Сохраните новую конфигурацию подсети в Azure.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Ожидаемые выходные данные:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              : 
		                    Name         Value
		                    ===========  =====
		                    displayName  VNet 
		                    
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
		                      ...,
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BacEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

## Включение IP-пересылки на FW1
Чтобы включить IP-пересылку на сетевой карте, используемой **FW1**, выполните следующие действия.

1. Создайте переменную, которая содержит параметры для сетевой карты, используемой FW1. В нашем случае это сетевая карта **NICFW1**.

		$nicfw1 = Get-AzureRmNetworkInterface -ResourceGroupName TestRG -Name NICFW1

2. Включите IP-пересылку и сохраните параметры сетевой карты.

		$nicfw1.EnableIPForwarding = 1
		Set-AzureRmNetworkInterface -NetworkInterface $nicfw1

	Ожидаемые выходные данные:

		Name                 : NICFW1
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
		Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState    : Succeeded
		Tags                 : 
		                       Name         Value                  
		                       ===========  =======================
		                       displayName  NetworkInterfaces - DMZ
		                       
		VirtualMachine       : {
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1"
		                       }
		IpConfigurations     : [
		                         {
		                           "Name": "ipconfig1",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
		                           "PrivateIpAddress": "192.168.0.4",
		                           "PrivateIpAllocationMethod": "Static",
		                           "Subnet": {
		                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
		                           },
		                           "PublicIpAddress": {
		                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
		                           },
		                           "LoadBalancerBackendAddressPools": [],
		                           "LoadBalancerInboundNatRules": [],
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DnsSettings          : {
		                         "DnsServers": [],
		                         "AppliedDnsServers": [],
		                         "InternalDnsNameLabel": null,
		                         "InternalFqdn": null
		                       }
		EnableIPForwarding   : True
		NetworkSecurityGroup : null
		Primary              : True

<!---HONumber=AcomDC_0810_2016-->