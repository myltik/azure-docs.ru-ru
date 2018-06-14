---
title: 'Модели подключения ExpressRoute: подключение к Microsoft Azure с помощью поставщиков сетевых услуг, поставщиков Exchange и Ethernet | Документация Майкрософт'
description: В этой статье описаны различные режимы подключения между сетью клиента и службами Microsoft Azure, Office 365 и Dynamics 365. Клиенты могут использовать поставщиков MPLS, поставщиков облачных служб Exchange и поставщиков Ethernet.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: cherylmc
ms.openlocfilehash: 00f97da2189491103c461b49ac82feb92d8f4b9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "22709901"
---
# <a name="expressroute-connectivity-models"></a>Модели подключения ExpressRoute
Вы можете создать между локальной сетью и облаком Майкрософт подключение трех разных типов: [совместное размещение в Cloud Exchange](#CloudExchange), [Ethernet-подключение типа "точка — точка"](#Ethernet) и [подключение типа "любой к любому" (IPVPN)](#IPVPN). Поставщики услуг подключения могут предлагать одну или несколько моделей подключения. Обратитесь к поставщику услуг подключения и подберите наиболее подходящую вам модель.
<br><br>

![Схема моделей подключения ExpressRoute](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="CloudExchange"></a>Совместное размещение в Cloud Exchange
При размещении на одном сервере с Cloud Exchange можно заказать виртуальное кросс-подключение к облаку Майкрософт через Ethernet Exchange поставщика совместного размещения. Поставщики услуг совместного размещения могут предлагать либо кросс-подключения второго уровня, либо управляемые кросс-подключения третьего уровня между вашей инфраструктурой на сервере совместного размещения и облаком Майкрософт.

## <a name="Ethernet"></a>Подключения Ethernet типа "точка — точка"
Локальные центры обработки данных и офисы можно подключить к облаку Майкрософт через связи Ethernet типа "точка-точка". Поставщики подключений Ethernet типа "точка-точка" могут предлагать подключения второго уровня или управляемые подключения третьего уровня между вашей сетью и облаком Майкрософт.

## <a name="IPVPN"></a>Сети типа "любой к любому" (IPVPN)
Глобальную вычислительную сеть можно интегрировать с облаком Майкрософт. Поставщики IP VPN (обычно это MPLS VPN) предлагают подключение между филиалами и центрами обработки данных типа "любой к любому". Облако Майкрософт можно связать с вашей глобальной вычислительной сетью, так чтобы оно выглядело как любой другой филиал. Обычно поставщики глобальных вычислительных сетей предлагают управляемые подключения третьего уровня. Возможности и функции ExpressRoute одинаковы для всех описанных выше моделей. 

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше о подключениях ExpressRoute и доменах маршрутизации. См. статью [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).
* Узнайте о функциях ExpressRoute. См. статью [Технический обзор ExpressRoute](expressroute-introduction.md).
* Найти поставщика услуг. См. статью [Партнеры и одноранговые расположения ExpressRoute](expressroute-locations.md).
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).
* См. сведения о требованиях для [маршрутизации](expressroute-routing.md) и [NAT](expressroute-nat.md) и [QoS](expressroute-qos.md).
* Настройте подключение ExpressRoute.
  * [Создайте канал ExpressRoute.](expressroute-howto-circuit-portal-resource-manager.md)
  * [Настройка маршрутизации](expressroute-howto-routing-portal-resource-manager.md)
  * [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)