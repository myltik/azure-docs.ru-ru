<properties
   pageTitle="Добавление шлюза виртуальной сети в виртуальную сеть для ExpressRoute с помощью Resource Manager и PowerShell | Microsoft Azure"
   description="В этой статье рассматривается добавление шлюза виртуальной сети в уже созданную виртуальную сеть диспетчера ресурсов для ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="07/19/2016"
   ms.author="charwen"/>

# Настройка шлюза виртуальной сети для ExpressRoute с помощью диспетчера ресурсов и PowerShell


> [AZURE.SELECTOR]
- [PowerShell — Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell — классическая модель](expressroute-howto-add-gateway-classic.md)


Эта статья содержит инструкции по добавлению, изменению размера и удалению шлюза виртуальной сети для существующей виртуальной сети. Приведенные действия по настройке предназначены для виртуальных сетей, созданных с помощью **модели развертывания Resource Manager**, которые будут использоваться в конфигурации ExpressRoute.

**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Подготовка

Убедитесь, что вы установили командлеты Azure PowerShell, необходимые для этой конфигурации (1.0.2 или более поздней версии). Если вы еще не установили эти командлеты, необходимо сделать это перед началом настройки. Дополнительную информацию об установке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

	
## Дальнейшие действия

После создания шлюза виртуальной сети вы можете связать виртуальную сеть с каналом ExpressRoute. См. статью [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!---HONumber=AcomDC_0720_2016-->