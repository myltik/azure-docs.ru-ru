---
title: "Устаревшие SKU шлюзов виртуальной сети Azure | Документация Майкрософт"
description: "Устаревшие SKU шлюзов виртуальной сети."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 3b2126b1ecd1613950bbf311ae08fafd4af0d51f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Работа со SKU шлюза виртуальной сети (старые версии SKU)

Эта статья содержит сведения о старых версиях SKU шлюза виртуальной сети. Старые версий SKU по-прежнему работают в обеих моделях развертывания для созданных VPN-шлюзов. Классические VPN-шлюзы по-прежнему используют старые номера SKU как для имеющихся шлюзов, так и для новых. При создании VPN-шлюзов диспетчера ресурсов используйте новые номера SKU шлюза. Сведения о новых номерах SKU см. в статье [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU шлюзов

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Расчетная суммарная пропускная способность в зависимости от SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Поддерживаемые конфигурации в зависимости от SKU и типа VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Изменение размера SKU шлюза (изменение SKU шлюза)

Вы можете изменить номер SKU шлюза в пределах одного семейства SKU. Например, при наличии номера SKU " Стандартный" можно изменить размер до SKU HighPerformance. Размер VPN-шлюза невозможно изменить путем перехода от старых SKU на новые семейства SKU. Например, вы не сможете перейти со SKU " Стандартный" на SKU VpnGw2. 

Чтобы изменить размер номера SKU шлюза для классической модели развертывания, используйте следующую команду:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Чтобы изменить размер номера SKU шлюза для модели развертывания с помощью Resource Manager, используйте следующую команду:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="migrate"></a>Переход на новые версии SKU шлюзов

При работе с моделью развертывания с помощью Resource Manager можно перейти к новой версии номеров SKU шлюзов. При работе с классической моделью развертывания вы не сможете этого сделать. Вместо этого необходимо использовать старые номера SKU.

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о новых версиях SKU шлюзов см. в разделе [SKU шлюзов](vpn-gateway-about-vpngateways.md#gwsku).

Дополнительные сведения о параметрах конфигурации VPN-шлюза см. в [этой статье](vpn-gateway-about-vpn-gateway-settings.md).