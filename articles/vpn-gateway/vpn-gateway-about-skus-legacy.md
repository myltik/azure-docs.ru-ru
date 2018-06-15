---
title: Устаревшие SKU VPN-шлюзов виртуальной сети Azure | Документация Майкрософт
description: 'Как работать со старыми номерами SKU шлюзов виртуальной сети: Basic, Standard и HighPerformance.'
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 4feecb9c1e91e1bc6c66a610c092e7bf894886e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190234"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Работа со SKU шлюза виртуальной сети (старые версии SKU)

Эта статья содержит сведения о старых версиях SKU шлюза виртуальной сети. Старые версий SKU по-прежнему работают в обеих моделях развертывания для созданных VPN-шлюзов. Классические VPN-шлюзы по-прежнему используют старые номера SKU как для имеющихся шлюзов, так и для новых. При создании VPN-шлюзов диспетчера ресурсов используйте новые номера SKU шлюза. Сведения о новых номерах SKU см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU шлюзов

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Расчетная суммарная пропускная способность в зависимости от SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Поддерживаемые конфигурации в зависимости от SKU и типа VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Изменение размера шлюза

Вы можете изменить размер шлюза, сменив номер SKU шлюза на другой в пределах одного семейства SKU. Например, при наличии номера SKU " Стандартный" можно изменить размер до SKU HighPerformance. Но размер VPN-шлюза невозможно изменить путем перехода со старого номера SKU на номер из нового семейства SKU. Например, вы не сможете перейти с номера SKU типа Standard на VpnGw2 или с номера SKU типа Basic на VpnGw1.

Чтобы изменить размер шлюза, созданный на базе классической модели развертывания, используйте следующую команду:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Чтобы изменить размер шлюза, созданный на базе модели развертывания с помощью PowerShell, используйте следующую команду:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Можно также изменить размер шлюза на портале Azure.

## <a name="change"></a>Переход на новые номера SKU шлюзов

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о новых версиях SKU шлюзов см. в разделе [SKU шлюзов](vpn-gateway-about-vpngateways.md#gwsku).

Дополнительные сведения о параметрах конфигурации VPN-шлюза см. в [этой статье](vpn-gateway-about-vpn-gateway-settings.md).