<properties 
   pageTitle="Как создать маршруты и включить IP-пересылку в Azure"
   description="Как создать маршруты и включить IP-пересылку в Azure"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Как создать маршруты и включить IP-пересылку в Azure
Виртуальные устройства в Azure можно использовать для обработки трафика в виртуальной сети Azure. Тем не менее, необходимо создать маршруты, позволяющие виртуальным машинам и облачным службам в виртуальной сети отправлять пакеты на виртуальное устройство, а не в место назначения пакета. Необходимо также включить IP-пересылку на виртуальную машину виртуального устройства, чтобы она могла получать и пересылать пакеты, в действительности не адресованные виртуальной машине виртуального устройства.

##Как управлять маршрутами
Вы можете добавлять, удалять и изменять маршруты в Azure с помощью PowerShell. Прежде чем можно будет создать маршрут, необходимо создать таблицу маршрутов для размещения маршрута.

### Как создать таблицу маршрутов
Чтобы создать таблицу маршрутов *FrontEndSubnetRouteTable*, выполните следующую команду PowerShell:

	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location usnorth `
	-Label "Route table for frontend subnet"

### Как добавить маршрут в таблицу маршрутов
Чтобы добавить в таблицу маршрутов маршрут, который задает *10.1.1.10* в качестве следующего прыжка для подсети *10.2.0.0/16*, созданной ранее, выполните следующую команду PowerShell:

	Set-AzureRoute -RouteTableName FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### Как связать маршрут с подсетью
Таблица маршрутов должна быть связана с одной или несколькими подсетями, чтобы ее можно было использовать. Чтобы связать таблицу маршрутов *FrontEndSubnetRouteTable* с подсетью *FrontEndSubnet* в виртуальной сети *ProductionVnet*, выполните следующую команду PowerShell:

	Set-AzureSubnetRouteTable -VNetName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### Как просмотреть примененные маршруты на виртуальной машине
Можно отправить запрос в Azure, чтобы просмотреть фактические маршруты, применяемые для конкретного экземпляра виртуальной машины или роли. Показанные маршруты включают в себя маршруты по умолчанию, предоставляемые Azure, а также маршруты, объявленные шлюзом VPN. Ограничение показанных маршрутов равно 800.

Чтобы просмотреть маршруты, связанные с основной сетевой картой на виртуальной машине *FirewallAppliance1*, выполните следующую команду PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

Чтобы просмотреть маршруты, связанные с дополнительной сетевой картой *backendnic* на виртуальной машине *FirewallAppliance1*, выполните следующую команду PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

Чтобы просмотреть маршруты, связанные с основной сетевой картой в экземпляре роли *myRole*, являющемся частью облачной службы *myservice*, выполните следующую команду PowerShell:

	Get-AzureEffectiveRouteTable -ServiceName myservice `
	-RoleInstanceName myRole

## Как управлять IP-пересылкой
Как упоминалось выше, необходимо включить IP-пересылку на любом экземпляре виртуальной машины или роли, который будет выступать в качестве виртуального устройства.

### Как включить IP-пересылку
Чтобы включить IP-пересылку на виртуальной машине *FirewallAppliance1*, выполните следующую команду PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

Чтобы включить IP-пересылку в экземпляре роли *FirewallAppliance1* в облачной службе *myService*, выполните следующую команду PowerShell:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Enable

### Как отключить IP-пересылку
Чтобы отключить IP-пересылку на виртуальной машине *FirewallAppliance1*, выполните следующую команду PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

Чтобы отключить IP-пересылку в экземпляре роли *FirewallAppliance1* в облачной службе *myService*, выполните следующую команду PowerShell:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Disable

### Как просмотреть состояние IP-пересылки
Чтобы просмотреть состояние IP-пересылки на виртуальной машине *FirewallAppliance1*, выполните следующую команду PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureIPForwarding

## См. также

[Обзор определяемых пользователем маршрутов и IP-пересылки](../virtual-networks-udr-overview)

[Общедоступный IP-адрес уровня экземпляра (ILIP)](../virtual-networks-instance-level-public-ip)

[Обзор виртуальной сети](https://msdn.microsoft.com/library/azure/jj156007.aspx) 

<!--HONumber=52-->
 