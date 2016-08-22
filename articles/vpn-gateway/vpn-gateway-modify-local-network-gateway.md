<properties
   pageTitle="Изменение префиксов IP-адресов шлюза локальной сети и IP-адреса шлюза | Microsoft Azure"
   description="Из этой статьи вы узнаете, как изменять префиксы IP-адресов для шлюза локальной сети."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# Изменение параметров шлюза локальной сети с помощью PowerShell

Иногда такие параметры шлюза локальной сети, как AddressPrefix или GatewayIPAddress, могут изменяться. Приведенные ниже инструкции помогут вам изменить нужные параметры. Кроме того, эти параметры можно изменить на портале Azure.

## Перед началом работы
	
Вам потребуется установить последнюю версию командлетов PowerShell диспетчера ресурсов Azure. Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Изменение префиксов IP-адресов

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## Изменение IP-адреса шлюза

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## Дальнейшие действия

Проверьте подключение шлюза. См. статью [Проверка подключения шлюза](vpn-gateway-verify-connection-resource-manager.md).

<!---HONumber=AcomDC_0810_2016-->