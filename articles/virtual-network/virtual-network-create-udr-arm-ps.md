<properties 
   pageTitle="Управление маршрутизацией и использование виртуальных модулей в диспетчере ресурсов с помощью PowerShell | Microsoft Azure"
   description="Сведения о том, как управлять маршрутизацией и использовать виртуальные модули в Azure PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/30/2015"
   ms.author="telmos" />

#Создание определяемых пользователем маршрутов (UDR) в PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [создавать определяемые пользователем маршруты на основе классической модели развертывания](virtual-network-create-udr-classic-ps.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Для приведенных ниже примеров команд PowerShell требуется уже созданная простая среда, основанная на приведенном выше сценарии. Чтобы выполнять команды в том виде, в котором они представлены в этом документе, сначала создайте тестовую среду, развернув [этот шаблон](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), щелкните **Deploy to Azure** (Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Создание определяемого пользователем маршрута для подсети переднего плана
Чтобы создать таблицу маршрутов и маршрут, необходимые для подсети переднего плана, на основании приведенного выше сценария, выполните следующие действия.

2. В командной строке Azure PowerShell выполните командлет **`Switch-AzureMode`**, чтобы включить режим диспетчера ресурсов, как показано ниже.

		Switch-AzureMode AzureResourceManager
	
	Ожидаемые выходные данные:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Командлет Switch-AzureMode является устаревшим. По этой причине все командлеты диспетчера ресурсов вскоре будут переименованы.

3. Создайте маршрут, используемый для отправки всего трафика, адресованного серверной подсети (192.168.2.0/24), в виртуальный модуль **FW1** (192.168.0.4).

		$route = New-AzureRouteConfig -Name RouteToBackEnd `
		    -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. Создайте таблицу маршрутов с именем **UDR-FrontEnd** в регионе **westus**, содержащем созданный ранее маршрут.

		$routeTable = New-AzureRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-FrontEnd -Route $route

5. Создайте переменную, которая содержит виртуальную сеть, где находится подсеть. В нашем случае такая виртуальная сеть имеет называется **TestVNet**.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet

6. Сопоставьте созданную выше таблицу маршрутов с подсетью **FrontEnd**.
		
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -RouteTable $routeTable

	Ожидаемые выходные данные:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mic
		                    rosoft.Network/virtualNetworks/TestVNet
		Etag              : W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"
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
		                        "Etag": "W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/pr
		                    oviders/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestR
		                    G/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestR
		                    G/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/
		                    providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/
		                    providers/Microsoft.Network/routeTables/UDR-FrontEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      },
								...
		                    ]

## Создание определяемого пользователем маршрута для серверной подсети
Чтобы создать таблицу маршрутов и маршрут, необходимые для серверной подсети, на основании приведенного выше сценария, выполните следующие действия.

1. Создайте маршрут, используемый для отправки всего трафика, адресованного подсети переднего плана (192.168.1.0/24), в виртуальный модуль **FW1** (192.168.0.4).

		$route = New-AzureRouteConfig -Name RouteToFrontEnd `
		    -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. Создайте таблицу маршрутов с именем **UDR-BackEnd** в регионе **uswest**, содержащем созданный ранее маршрут.

		$routeTable = New-AzureRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-BackEnd -Route $route

5. Сопоставьте созданную выше таблицу маршрутов с подсетью **BackEnd**.

		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -RouteTable $routeTable

	Ожидаемые выходные данные:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Mic
		                    rosoft.Network/virtualNetworks/TestVNet
		Etag              : W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"
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
		                        "Etag": "W/"7df26c0e-652f-4754-bc4e-733fef7d5b2b"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/pr
		                    oviders/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestR
		                    G/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestR
		                    G/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/
		                    providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/
		                    providers/Microsoft.Network/routeTables/UDR-BackEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

## Включение IP-пересылки на FW1
Чтобы включить IP-пересылку на сетевой карте, используемой **FW1**, выполните следующие действия.

1. Создайте переменную, которая содержит параметры для сетевой карты, используемой FW1. В нашем случае такая сетевая карта называется **NICFW1**.

		$nicfw1 = Get-AzureNetworkInterface -ResourceGroupName TestRG -Name NICFW1

2. Включите IP-пересылку и сохраните параметры сетевой карты.

		$nicfw1.EnableIPForwarding = 1
		Set-AzureNetworkInterface -NetworkInterface $nicfw1

	Ожидаемые выходные данные:

		Name                 : NICFW1
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/
		                       Microsoft.Network/networkInterfaces/NICFW1
		Etag                 : W/"e0f9adc0-d8bf-4def-beab-5ed3e1305c9a"
		ProvisioningState    : Succeeded
		Tags                 : 
		                       Name         Value                  
		                       ===========  =======================
		                       displayName  NetworkInterfaces - DMZ
		                       
		VirtualMachine       : {
		                         "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/p
		                       roviders/Microsoft.Compute/virtualMachines/FW1"
		                       }
		IpConfigurations     : [
		                         {
		                           "Name": "ipconfig1",
		                           "Etag": "W/"e0f9adc0-d8bf-4def-beab-5ed3e1305c9a"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		                       /providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
		                           "PrivateIpAddress": "192.168.0.4",
		                           "PrivateIpAllocationMethod": "Static",
		                           "Subnet": {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/Test
		                       RG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
		                           },
		                           "PublicIpAddress": {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/Test
		                       RG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
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

<!---HONumber=Oct15_HO2-->