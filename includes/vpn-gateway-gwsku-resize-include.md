---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
Чтобы изменить размер шлюза для текущих номеров SKU (VpnGw1, VpnGw2 и VPNGW3) для обновления до более мощной конфигурации, вы можете использовать командлет PowerShell `Resize-AzureRmVirtualNetworkGateway`. С помощью этого командлета также можно перейти на более низкий уровень SKU. Если вы используете номер SKU шлюза уровня "Базовый", чтобы изменить размер шлюза, [вместо указанного командлета используйте эти инструкции](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize).

В следующем примере PowerShell показано изменение размера шлюза до SKU со значением VpnGw2.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Кроме того, вы можете изменить размер шлюза на портале Azure. Для этого откройте страницу **Конфигурация** для своего шлюза виртуальной сети и выберите другой номер SKU в раскрывающемся списке.