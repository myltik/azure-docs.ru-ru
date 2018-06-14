---
title: Предварительные требования к использованию Azure ExpressRoute | Документация Майкрософт
description: На этой странице приведен список требований, которые необходимо выполнить, прежде чем заказать канал ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2018
ms.author: cherylmc
ms.openlocfilehash: 332bfba0664391b6126669c4c89e38d1af4cf4fa
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
ms.locfileid: "30265090"
---
# <a name="expressroute-prerequisites--checklist"></a>Предварительные требования и контрольный список для ExpressRoute
Чтобы подключиться к службам Microsoft Cloud через ExpressRoute, вам нужно выполнить условия в следующих разделах.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Учетная запись Azure
* Наличие действительной и активной учетной записи Microsoft Azure. Она необходима для организации канала ExpressRoute. Каналы ExpressRoute являются ресурсами в подписках Azure. Подписка Azure обязательна, даже если будет выполняться только подключение к облачным службам (Майкрософт), отличным от Azure, например Office 365 и Dynamics 365.
* Активная подписка на Office 365 (при использовании служб Office 365). Дополнительные сведения об определенных требованиях к Office 365 см. в [соответствующем разделе](#office-365-specific-requirements) этой статьи.

## <a name="connectivity-provider"></a>Поставщик услуг подключения

* Чтобы подключиться к Microsoft Cloud, можно работать с [партнером по подключению ExpressRoute](expressroute-locations.md#partners) . Подключение между локальной сетью и Майкрософт можно настроить [тремя различными способами](expressroute-introduction.md).
* Если ваш поставщик не является партнером по подключению ExpressRoute, вы можете подключиться к Microsoft Cloud через [поставщика облачных служб Exchange](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Требования к сети
* **Избыточное подключение.** Для физического соединения между вами и поставщиком услуг отсутствуют требования к избыточности. Майкрософт требует устанавливать избыточные сеансы BGP между маршрутизаторами Майкрософт и маршрутизаторами одноранговых участников, даже если у вас есть только [одно физическое подключение к облачной службе Exchange](expressroute-faqs.md#onep2plink).
* **Маршрутизация.** В зависимости от способа подключения к Microsoft Cloud вам или вашему поставщику необходимо настроить сеансы BGP для [доменов маршрутизации](expressroute-circuit-peerings.md) и управлять этими сеансами. Некоторые поставщики подключений Ethernet или облачных услуг Exchange могут предлагать управление BGP в качестве услуги с добавленной стоимостью.
* **Преобразование сетевых адресов (NAT).** Майкрософт принимает только общедоступные IP-адреса через пиринг. Если в локальной сети используются частные IP-адреса, вам или вашему поставщику услуг необходимо преобразовать их в общедоступные IP-адреса [с помощью NAT](expressroute-nat.md).
* **Качество обслуживания.** Skype для бизнеса предоставляет различные услуги (например, передача голоса, видео, текста), которые требуют дифференцированного подхода к качеству обслуживания. Вам и вашему поставщику услуг необходимо соблюдать [требования к качеству обслуживания](expressroute-qos.md).
* **Сетевая безопасность.** При подключении к Microsoft Cloud через ExpressRoute необходимо помнить о [сетевой безопасности](../best-practices-network-security.md).

## <a name="office-365"></a>Office 365
Если вы планируете включить Office 365 через ExpressRoute, просмотрите следующие документы для получения дополнительных сведений о требованиях к Office 365.

* [Azure ExpressRoute для Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Маршрутизация с помощью ExpressRoute для Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Высокий уровень доступности и отработка отказов при использовании Azure ExpressRoute](https://aka.ms/erhighavailability)
* [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Сетевое планирование и настройка производительности для Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Планирование сети и миграции при переходе на Office 365](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Интеграция Office 365 с локальными средами](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Расширенные учебные видеоматериалы об использовании ExpressRoute в Office 365](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Если вы планируете включить Dynamics 365 через ExpressRoute, просмотрите следующие документы для получения дополнительных сведений о требованиях к Dynamics 365.

* [Документация по Dynamics 365 и ExpressRoute](http://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [URL-адреса Dynamics 365](https://support.microsoft.com/kb/2655102) и [Диапазоны IP-адресов](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Найдите поставщика услуг подключения ExpressRoute. См. статью [Партнеры и одноранговые расположения ExpressRoute](expressroute-locations.md).
* Ознакомьтесь с требованиями к [маршрутизации](expressroute-routing.md), [NAT](expressroute-nat.md) и [качеству обслуживания](expressroute-qos.md).
* Настройте подключение ExpressRoute.
  * [Создайте канал ExpressRoute.](expressroute-howto-circuit-classic.md)
  * [Настройка маршрутизации](expressroute-howto-routing-classic.md)
  * [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md)
