<properties 
   pageTitle="Как создать маршруты и включить IP-пересылку в Azure"
   description="Информация о том, как управлять определяемыми пользователем маршрутами и IP-пересылкой"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2015"
   ms.author="telmos" />

# Как создать маршруты и включить IP-пересылку в Azure
Виртуальные устройства в Azure можно использовать для обработки трафика в виртуальной сети Azure. Тем не менее, необходимо создать маршруты, позволяющие виртуальным машинам и облачным службам в виртуальной сети отправлять пакеты на виртуальное устройство, а не в место назначения пакета. Необходимо также включить IP-пересылку на виртуальную машину виртуального устройства, чтобы она могла получать и пересылать пакеты, в действительности не адресованные виртуальной машине виртуального устройства.

## Как управлять маршрутами
Вы можете добавлять, удалять и изменять маршруты в Azure с помощью PowerShell. Прежде чем можно будет создать маршрут, необходимо создать таблицу маршрутов для размещения маршрута.

### Как создать таблицу маршрутов
Чтобы создать таблицу маршрутов *FrontEndSubnetRouteTable*, выполните следующую команду PowerShell:

	```powershell
	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
		-Location uscentral `
		-Label "Route table for front end subnet"
	```

Выходные данные команды выше должны выглядеть следующим образом:

	Name                      Location   Label                          
	----                      --------   -----                          
	FrontEndSubnetRouteTable  West US    Route table for front end subnet

### Как добавить маршрут в таблицу маршрутов
Чтобы добавить в таблицу маршрутов маршрут, который задает *10.1.1.10* в качестве следующего прыжка для подсети *10.2.0.0/16*, созданной ранее, выполните следующую команду PowerShell:

	```powershell
	Get-AzureRouteTable FrontEndSubnetRouteTable `
		|Set-AzureRoute -RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
		-NextHopType VirtualAppliance `
		-NextHopIpAddress 10.1.1.10
	```

Выходные данные команды выше должны выглядеть следующим образом:

	Name     : FrontEndSubnetRouteTable
	Location : Central US
	Label    : Route table for frontend subnet
	Routes   : 
	           Name                 Address Prefix    Next hop type        Next hop IP address
	           ----                 --------------    -------------        -------------------
	           firewallroute        10.2.0.0/16       VirtualAppliance     10.1.1.10    

### Как связать маршрут с подсетью
Таблица маршрутов должна быть связана с одной или несколькими подсетями, чтобы ее можно было использовать. Чтобы связать таблицу маршрутов *FrontEndSubnetRouteTable* с подсетью *FrontEndSubnet* в виртуальной сети *ProductionVnet*, выполните следующую команду PowerShell:

	```powershell
	Set-AzureSubnetRouteTable -VirtualNetworkName ProductionVnet `
		-SubnetName FrontEndSubnet `
		-RouteTableName FrontEndSubnetRouteTable
	```

### Как просмотреть примененные маршруты на виртуальной машине
Можно отправить запрос в Azure, чтобы просмотреть фактические маршруты, применяемые для конкретного экземпляра виртуальной машины или роли. Показанные маршруты включают в себя маршруты по умолчанию, предоставляемые Azure, а также маршруты, объявленные шлюзом VPN. Ограничение показанных маршрутов равно 800.

Чтобы просмотреть маршруты, связанные с основной сетевой картой на виртуальной машине *FWAppliance1*, выполните следующую команду PowerShell:

	```powershell
	Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
		| Get-AzureEffectiveRouteTable
	```

Выходные данные команды выше должны выглядеть следующим образом:

	Effective routes : 
	                   Name            Address Prefix    Next hop type    Next hop IP address Status   Source     
	                   ----            --------------    -------------    ------------------- ------   ------     
	                                   {10.0.0.0/8}      VNETLocal                            Active   Default    
	                                   {0.0.0.0/0}       Internet                             Active   Default    
	                                   {25.0.0.0/8}      Null                                 Active   Default    
	                                   {100.64.0.0/10}   Null                                 Active   Default    
	                                   {172.16.0.0/12}   Null                                 Active   Default    
	                                   {192.168.0.0/16}  Null                                 Active   Default    
	                   firewallroute   {10.2.0.0/16}     Null             10.1.1.10           Active   User      

Чтобы просмотреть маршруты, связанные с дополнительной сетевой картой *backendnic* на виртуальной машине *FWAppliance1*, выполните следующую команду PowerShell:

	```powershell
	Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
		| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic
	```

Чтобы просмотреть маршруты, связанные с основной сетевой картой в экземпляре роли *myRole*, являющемся частью облачной службы *ProductionVMs*, выполните следующую команду PowerShell:

	```powershell
	Get-AzureEffectiveRouteTable -ServiceName ProductionVMs `
		-RoleInstanceName myRole
	```

## Как управлять IP-пересылкой
Как упоминалось выше, необходимо включить IP-пересылку на любом экземпляре виртуальной машины или роли, который будет выступать в качестве виртуального устройства.

### Как включить IP-пересылку
Чтобы включить IP-пересылку на виртуальной машине *FWAppliance1*, выполните следующую команду PowerShell:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Enable
```

Чтобы включить IP-пересылку в экземпляре роли *FWAppliance* в облачной службе *DMZService*, выполните следующую команду PowerShell:

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Enable
```

### Как отключить IP-пересылку
Чтобы отключить IP-пересылку на виртуальной машине *FWAppliance1*, выполните следующую команду PowerShell:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName DMZService `
	| Set-AzureIPForwarding -Disable
```

Чтобы отключить IP-пересылку в экземпляре роли *FWAppliance* в облачной службе *DMZService*, выполните следующую команду PowerShell:

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Disable
```

### Как просмотреть состояние IP-пересылки
Чтобы просмотреть состояние IP-пересылки на виртуальной машине *FWAppliance1*, выполните следующую команду PowerShell:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureIPForwarding
``` 

<!---HONumber=Oct15_HO3-->