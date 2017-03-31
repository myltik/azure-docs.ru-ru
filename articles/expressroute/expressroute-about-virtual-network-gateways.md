---
title: "Сведения о шлюзах виртуальных сетей ExpressRoute | Документация Майкрософт"
description: "Сведения о шлюзах виртуальных сетей ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b58a8f7f87a231bd44c9224e3c889c31336ee0b1
ms.lasthandoff: 03/21/2017


---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Сведения о шлюзах виртуальных сетей ExpressRoute
Шлюз виртуальной сети используется для обмена сетевым трафиком между виртуальными и локальными сетями Azure. В процессе настройки подключения ExpressRoute необходимо создать и настроить шлюз виртуальной сети и подключение для него.

При создании шлюза виртуальной сети нужно указать несколько параметров. Один из обязательных параметров указывает, будет ли шлюз использоваться для трафика ExpressRoute или VPN "сеть — сеть". В модели развертывания Resource Manager это параметр -GatewayType.

Если сетевой трафик направляется через выделенное частное подключение, используется тип шлюза ExpressRoute. Он также называется шлюзом ExpressRoute. Если сетевой трафик направляется в зашифрованном виде через общедоступный Интернет, то используется тип шлюза Vpn. Он также называется VPN-шлюзом. При подключениях типа "сеть — сеть", "точка — сеть" и "виртуальная сеть — виртуальная сеть" используется VPN-шлюз. 

В виртуальной сети каждому типу шлюза может соответствовать только один шлюз виртуальной сети. Например, у вас может быть только один шлюз виртуальной сети типа VPN и только один типа ExpressRoute. Эта статья посвящена шлюзам виртуальных сетей ExpressRoute.

## <a name="gwsku"></a>SKU шлюзов
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Если требуется выполнить обновление до шлюза с большими возможностями согласно SKU, в большинстве случаев можно использовать командлет PowerShell Resize-AzureRmVirtualNetworkGateway. Это подойдет для обновлений до SKU Standard и HighPerformance. Однако для обновления до SKU UltraPerformance шлюз необходимо создать заново.

### <a name="aggthroughput"></a>Расчетная суммарная пропускная способность в зависимости от SKU шлюза
В следующей таблице приведены типы шлюзов с приблизительной суммарной пропускной способностью. Эта таблица относится к классической модели развертывания и модели диспетчера ресурсов.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Пропускная способность приложения зависит от нескольких факторов, таких как сквозная задержка и количество потоков трафика, которые открывает приложение. Числа в таблице представляют собой верхний предел, которого приложение может теоретически достичь в идеальных условиях. 
> 
>

## <a name="resources"></a>Интерфейсы REST API и командлеты PowerShell
Дополнительные технические материалы и специальные требования к синтаксису, действующие при использовании интерфейсов REST API и командлетов PowerShell для настройки конфигураций шлюзов виртуальных сетей, доступны по приведенным ниже ссылкам.

| **Классический** | **Диспетчер ресурсов** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о доступных конфигурациях подключений см. в статье [Технический обзор ExpressRoute](expressroute-introduction.md). 


