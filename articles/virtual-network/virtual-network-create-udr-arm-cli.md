.<properties 
   pageTitle="Управление маршрутизацией и использование виртуальных модулей в диспетчере ресурсов с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Сведения о том, как управлять маршрутизацией и использовать виртуальные модули с помощью интерфейса командной строки Azure"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#Создание определяемых пользователем маршрутов (UDR) в интерфейсе командной строки Azure

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [создавать определяемые пользователем маршруты на основе классической модели развертывания](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Для приведенных ниже примеров команд интерфейса командной строки Azure требуется уже созданная простая среда, основанная на приведенном выше сценарии. Чтобы выполнять команды в том виде, в котором они представлены в этом документе, сначала создайте тестовую среду, развернув [этот шаблон](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), щелкните **Deploy to Azure** (Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Создание определяемого пользователем маршрута для подсети переднего плана
Чтобы создать таблицу маршрутов и маршрут, необходимые для подсети переднего плана, на основании приведенного выше сценария, выполните следующие действия.

3. Выполните команду **`azure network route-table create`**, чтобы создать таблицу маршрутов для подсети переднего плана.

		azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

	Выходные данные:

		info:    Executing command network route-table create
		info:    Looking up route table "UDR-FrontEnd"
		info:    Creating route table "UDR-FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    Name                            : UDR-FrontEnd
		data:    Type                            : Microsoft.Network/routeTables
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		info:    network route-table create command OK

	Параметры
	- **-g (или --resource-group)**. Имя группы ресурсов, в которой будет создана группа безопасности сети. В данном сценарии это *TestRG*.
	- **-l (или --location)**. Регион Azure, в котором будет создана группа безопасности сети. В нашем случае это *westus*.
	- **-n (или --name)**. Имя новой группы безопасности сети. В данном сценарии это *NSG-FrontEnd*.

4. Выполните команду **`azure network route-table route create`**, чтобы создать маршрут в созданной ранее таблице маршрутов для отправки всего трафика, предназначенного для серверной подсети (192.168.2.0/24), в виртуальную машину **FW1** (192.168.0.4).

		azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

	Выходные данные:

		info:    Executing command network route-table route create
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd/routes/RouteToBackEnd
		data:    Name                            : RouteToBackEnd
		data:    Provisioning state              : Succeeded
		data:    Next hop type                   : VirtualAppliance
		data:    Next hop IP address             : 192.168.0.4
		data:    Address prefix                  : 192.168.2.0/24
		info:    network route-table route create command OK

	Параметры
	- **-r (или --route-table-name)**. Имя таблицы маршрутов, куда будет добавлен маршрут. В данном сценарии это *UDR-FrontEnd*.
	- **-a (или --address-prefix)**. Префикс адреса для подсети, в которую адресованы пакеты. В данном сценарии это *192.168.2.0/24*.
	- **-y (или --next-hop-type)**. Тип объекта, куда будет отправляться трафик. Возможные значения: *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* или *None*.
	- **-p (или --next-hop-ip-address**). IP-адрес следующего прыжка. В нашем случае это *192.168.0.4*.

5. Выполните команду **`azure network vnet subnet set`**, чтобы сопоставить созданную ранее таблицу маршрутов с подсетью **FrontEnd**.

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

	Выходные данные:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
		igurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
		igurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

	Параметры
	- **-e (или --vnet-name)**. Имя виртуальной сети, в которой расположена подсеть. В нашем случае это *TestVNet*.
 
## Создание определяемого пользователем маршрута для серверной подсети
Чтобы создать таблицу маршрутов и маршрут, необходимые для серверной подсети, на основании приведенного выше сценария, выполните следующие действия.

1. Выполните команду **`azure network route-table create`**, чтобы создать таблицу маршрутов для серверной подсети.

		azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Выполните команду **`azure network route-table route create`**, чтобы создать маршрут в созданной ранее таблице маршрутов для отправки всего трафика, предназначенного для подсети переднего плана (192.168.1.0/24), в виртуальную машину **FW1** (192.168.0.4).

		azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Выполните команду **`azure network vnet subnet set`**, чтобы сопоставить созданную ранее таблицу маршрутов с подсетью **BackEnd**.

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## Включение IP-пересылки на FW1
Чтобы включить IP-пересылку на сетевой карте, используемой **FW1**, выполните следующие действия.

1. Выполните команду **`azure network nic show`** и проверьте значение параметра **Enable IP forwarding** (Включить IP-пересылку). Оно должно быть равно *false*.

		azure network nic show -g TestRG -n NICFW1

	Выходные данные:
		
		info:    Executing command network nic show
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : false
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic show command OK

2. Выполните команду **`azure network nic set`**, чтобы включить IP-пересылку.

		azure network nic set -g TestRG -n NICFW1 -f true

	Выходные данные:

		info:    Executing command network nic set
		info:    Looking up the network interface "NICFW1"
		info:    Updating network interface "NICFW1"
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : true
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic set command OK

	Параметры

	- **-f (или --enable-ip-forwarding)**. *true* или *false*.

<!---HONumber=AcomDC_0810_2016-->