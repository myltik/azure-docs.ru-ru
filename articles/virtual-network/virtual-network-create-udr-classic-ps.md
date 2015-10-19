<properties 
   pageTitle="Управление маршрутизацией и использование виртуальных модулей с помощью PowerShell в классической модели развертывания | Microsoft Azure"
   description="Сведения об управлении маршрутизацией и виртуальными сетями с помощью PowerShell в классической модели развертывания"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2015"
   ms.author="telmos" />

#Управление маршрутизацией и использование виртуальных модулей (классический режим) с помощью PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье рассматривается классическая модель развертывания. Вы также можете [ВВЕСТИ ЗДЕСЬ ДЕЙСТВИЕ ДЛЯ ARM](armToken).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Для приведенных ниже примеров команд интерфейса командной строки Azure требуется уже созданная простая среда, основанная на приведенном выше сценарии. Для выполнения команд в том виде, в каком они представлены в данном документе, создайте среду, описанную в разделе о [создании виртуальной сети (классический режим) с помощью PowerShell](virtual-networks-create-vnet-classic-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Создание определяемого пользователем маршрута для подсети переднего плана
Чтобы создать таблицу маршрутов и маршрут, необходимые для подсети переднего плана, на основании приведенного выше сценария, выполните следующие действия.

3. Выполните командлет **`New-AzureRouteTable`**, чтобы создать таблицу маршрутов для подсети переднего плана.

		```powershell
		New-AzureRouteTable -Name UDR-FrontEnd `
			-Location uswest `
			-Label "Route table for front end subnet"
		```

	Выходные данные:

		Name         Location   Label                          
		----         --------   -----                          
		UDR-FrontEnd West US    Route table for front end subnet

4. Выполните командлет **`Set-AzureRoute`**, чтобы создать маршрут в созданной ранее таблице маршрутов для отправки всего трафика, предназначенного для серверной подсети (192.168.2.0/24), в виртуальную машину **FW1** (192.168.0.4).
	
		```powershell
		Get-AzureRouteTable UDR-FrontEnd `
			|Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4
		```

	Выходные данные:

		Name     : UDR-FrontEnd
		Location : West US
		Label    : Route table for frontend subnet
		Routes   : 
		           Name                 Address Prefix    Next hop type        Next hop IP address
		           ----                 --------------    -------------        -------------------
		           RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Выполните командлет **`Set-AzureSubnetRouteTable`**, чтобы сопоставить созданную ранее таблицу маршрутов с подсетью **FrontEnd**.

		```powershell
		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
		```
 
## Создание определяемого пользователем маршрута для серверной подсети
Чтобы создать таблицу маршрутов и маршрут, необходимые для серверной подсети, на основании приведенного выше сценария, выполните следующие действия.

3. Выполните командлет **`New-AzureRouteTable`**, чтобы создать таблицу маршрутов для серверной подсети.

		```powershell
		New-AzureRouteTable -Name UDR-BackEnd `
			-Location uswest `
			-Label "Route table for back end subnet"
		```

4. Выполните командлет **`Set-AzureRoute`**, чтобы создать маршрут в созданной ранее таблице маршрутов для отправки всего трафика, предназначенного для подсети переднего плана (192.168.1.0/24), в виртуальную машину **FW1** (192.168.0.4).

		```powershell
		Get-AzureRouteTable UDR-BackEnd `
			|Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4
		```

5. Выполните командлет **`Set-AzureSubnetRouteTable`**, чтобы сопоставить созданную ранее таблицу маршрутов с подсетью **BackEnd**.

		```powershell
		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
		```
## Включение IP-пересылки на виртуальной машине FW1
Чтобы включить IP-пересылку на виртуальной машине FW1, выполните следующие действия.

1. Запустите командлет **`Get-AzureIPForwarding`**, чтобы проверить состояние IP-пересылки.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Get-AzureIPForwarding

	Выходные данные:

		Disabled

2. Выполните команду **`Set-AzureIPForwarding`**, чтобы включить IP-пересылку для виртуальной машины *FW1*.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Set-AzureIPForwarding -Enable

<!---HONumber=Oct15_HO2-->