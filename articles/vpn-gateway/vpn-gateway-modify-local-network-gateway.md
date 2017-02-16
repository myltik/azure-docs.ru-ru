---
title: "Изменение префиксов IP-адресов шлюза локальной сети и IP-адреса шлюза | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как изменять префиксы IP-адресов для шлюза локальной сети."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: 2459f106e90616b05062224be82a06ef20fbc7b4


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Изменение параметров шлюза локальной сети с помощью PowerShell
Иногда такие параметры шлюза локальной сети, как AddressPrefix или GatewayIPAddress, могут изменяться. Приведенные ниже инструкции помогут вам изменить нужные параметры. Кроме того, эти параметры можно изменить на портале Azure.

## <a name="before-you-begin"></a>Перед началом работы
Вам потребуется установить последнюю версию командлетов PowerShell диспетчера ресурсов Azure. Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="to-modify-ip-address-prefixes"></a>Изменение префиксов IP-адресов
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Изменение IP-адреса шлюза
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
Проверьте подключение шлюза. См. статью [Проверка подключения шлюза](vpn-gateway-verify-connection-resource-manager.md).




<!--HONumber=Dec16_HO1-->


