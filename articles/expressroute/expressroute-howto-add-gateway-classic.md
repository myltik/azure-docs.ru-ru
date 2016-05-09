<properties
   pageTitle="Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell | Microsoft Azure"
   description="Узнайте, как настроить шлюз виртуальной сети для виртуальной сети на основе классической модели развертывания с помощью командлетов PowerShell для настройки ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/06/2016"
   ms.author="charwen"/>

# Настройка шлюза виртуальной сети для ExpressRoute с помощью классической модели развертывания и PowerShell

> [AZURE.SELECTOR]
- [PowerShell — Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell — классическая модель](expressroute-howto-add-gateway-classic.md)

Эта статья содержит инструкции по добавлению, изменению размера и удалению шлюза виртуальной сети для существующей виртуальной сети. Приведенные действия по настройке предназначены для виртуальных сетей, созданных с помощью **классической модели развертывания**, которые будут использоваться в конфигурации ExpressRoute.

**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Подготовка

Убедитесь, что вы установили командлеты Azure PowerShell, необходимые для этой конфигурации (1.0.2 или более поздней версии). Если вы еще не установили эти командлеты, необходимо сделать это перед началом настройки. Дополнительную информацию об установке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

	
## Дальнейшие действия

После создания шлюза виртуальной сети вы можете связать виртуальную сеть с каналом ExpressRoute. См. статью [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md).

<!---HONumber=AcomDC_0427_2016-->