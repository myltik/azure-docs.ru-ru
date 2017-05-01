---
title: "Изменение префиксов IP-адресов шлюза локальной сети и IP-адреса VPN- шлюза | Azure | PowerShell | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как изменять префиксы IP-адресов для шлюза локальной сети."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a26cbe2172dc27c152246d70b7f6b504ec4a08a5
ms.lasthandoff: 04/12/2017


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


