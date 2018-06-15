---
title: Сведения о шлюзах виртуальных сетей ExpressRoute | Документация Майкрософт
description: Сведения о шлюзах виртуальных сетей ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: cherylmc
ms.openlocfilehash: ae971e7743d6dd3269c0a4f976bd2a5316300f58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30235331"
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Сведения о шлюзах виртуальных сетей ExpressRoute
Шлюз виртуальной сети используется для обмена сетевым трафиком между виртуальными и локальными сетями Azure. В процессе настройки подключения ExpressRoute необходимо создать и настроить шлюз виртуальной сети и подключение для него.

При создании шлюза виртуальной сети нужно указать несколько параметров. Один из обязательных параметров указывает, будет ли шлюз использоваться для трафика ExpressRoute или VPN "сеть — сеть". В модели развертывания Resource Manager это параметр -GatewayType.

Если сетевой трафик направляется через частное подключение, используется тип шлюза ExpressRoute. Он также называется шлюзом ExpressRoute. Если сетевой трафик направляется в зашифрованном виде через общедоступный Интернет, то используется тип шлюза Vpn. Он также называется VPN-шлюзом. При подключениях типа "сеть — сеть", "точка — сеть" и "виртуальная сеть — виртуальная сеть" используется VPN-шлюз.

В виртуальной сети каждому типу шлюза может соответствовать только один шлюз виртуальной сети. Например, у вас может быть только один шлюз виртуальной сети типа VPN и только один типа ExpressRoute. Эта статья посвящена шлюзам виртуальных сетей ExpressRoute.

## <a name="gwsku"></a>SKU шлюзов
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Если требуется выполнить обновление до шлюза с большими возможностями согласно SKU, в большинстве случаев можно использовать командлет PowerShell Resize-AzureRmVirtualNetworkGateway. Это подойдет для обновлений до SKU Standard и HighPerformance. Однако для обновления до SKU UltraPerformance шлюз необходимо создать заново.

### <a name="aggthroughput"></a>Приблизительная производительность в зависимости от номера SKU шлюза
В таблице ниже приведены типы шлюзов с приблизительной производительностью. Эта таблица относится к классической модели развертывания и модели диспетчера ресурсов.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Производительность приложения зависит от нескольких факторов, таких как сквозная задержка и количество потоков трафика, которые открывает приложение. Числа в таблице представляют собой верхний предел, которого приложение может теоретически достичь в идеальных условиях. 
> 
>

## <a name="resources"></a>Интерфейсы REST API и командлеты PowerShell
Дополнительные технические материалы и специальные требования к синтаксису, действующие при использовании интерфейсов REST API и командлетов PowerShell для настройки конфигураций шлюзов виртуальных сетей, доступны по приведенным ниже ссылкам.

| **Классический** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о доступных конфигурациях подключений см. в статье [Технический обзор ExpressRoute](expressroute-introduction.md).

В разделе [Создание шлюза виртуальной сети для ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) доступны дополнительные сведения о создании шлюзов ExpressRoute.
