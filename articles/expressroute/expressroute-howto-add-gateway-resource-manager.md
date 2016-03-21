<properties
   pageTitle="Добавление VPN-шлюза в виртуальную сеть для ExpressRoute с помощью Resource Manager и PowerShell | Microsoft Azure"
   description="В этой статье рассматривается добавление VPN-шлюза в созданную виртуальную сеть Resource Manager для ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/26/2016"
   ms.author="cherylmc"/>

# Добавление VPN-шлюза в виртуальную сеть Resource Manager для конфигурации ExpressRoute 

В этой статье последовательно описано, как добавить подсеть шлюза и создать VPN-шлюз для существующей виртуальной сети. Приведенные действия по настройке предназначены для виртуальных сетей, созданных с помощью **модели развертывания Resource Manager**, которые будут использоваться в конфигурации ExpressRoute. Если вам необходима информация о создании шлюзов для виртуальных сетей, использующих классическую модель развертывания, см. раздел [Настройка виртуальной сети для ExpressRoute](expressroute-howto-vnet-portal-classic.md).

## Подготовка

Убедитесь, что вы установили командлеты Azure PowerShell, необходимые для этой конфигурации (1.0.2 или более поздней версии). Если вы еще не установили эти командлеты, необходимо сделать это перед началом настройки. Дополнительную информацию об установке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## Проверка создания шлюза

Используйте следующую команду, чтобы проверить, был ли создан шлюз.

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
	
## Дальнейшие действия

После создания VPN-шлюза вы можете связать виртуальную сеть с каналом ExpressRoute. См. раздел [Связывание виртуальной сети с каналами ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!---HONumber=AcomDC_0309_2016-->