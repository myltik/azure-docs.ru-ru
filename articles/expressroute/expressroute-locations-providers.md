---
title: Расположения и поставщики услуг подключения Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по расположению.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: pareshmu
ms.openlocfilehash: b6ea9c64f8363821eb3e61c2979f249816675bf7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute

> [!div class="op_single_selector"]
> * [Расположения по поставщикам](expressroute-locations.md)
> * [Поставщики по расположению](expressroute-locations-providers.md)


В данной статье приведены таблицы со сведениями о поставщиках услуг подключения ExpressRoute, географическом покрытии ExpressRoute, облачных службах Майкрософт, поддерживаемых через ExpressRoute, и системных интеграторах ExpressRoute.

## <a name="partners"></a>Поставщики услуг подключения ExpressRoute
ExpressRoute поддерживается во всех регионах и расположениях Azure. На следующей карте обозначены регионы Azure и расположения ExpressRoute. Расположения ExpressRoute соответствуют тем территориям, где Майкрософт взаимодействует с несколькими одноранговыми поставщиками услуг.

![Карта расположения][0]

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Регионы Azure с расположениями ExpressRoute в пределах геополитических регионов
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Северная Америка** |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, юго-центральная часть США, северо-центральная часть США, западно-центральная часть США, центральная часть Канады, восточная часть Канады |Атланта, Вашингтон (округ Колумбия), Даллас, Денвер, Квебек, Кремниевая долина, Лас-Вегас, Лос-Анджелес, Майами, Монреаль, Нью-Йорк, Сан-Антонио, Сиэтл, Торонто, Чикаго |
| **Северная Америка** |Южная часть Бразилии |Сан-Паулу |
| **Европа** |Центральная Франция, Южная Франция, Северная Европа, Западная Европа, западная часть Соединенного Королевства, южная часть Соединенного Королевства |Амстердам, Дублин, Лондон, Ньюпорт (Уэльс), Париж |
| **Азия** |Восточная Азия, Юго-Восточная Азия |Гонконг, Сингапур, Сингапур 2 |
| **Япония** |Западная Япония, Восточная Япония |Осака, Токио |
| **Австралия** |Юго-восточная Австралия, восточная Австралия, центральная Австралия, центральная Австралия 2 |Мельбурн, Сидней |
| **Индия** |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Мумбаи |
| **Южная Корея** |Центральная Корея, Южная Корея |Пусан, Сеул |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Облако правительства США** |Айова (для обслуживания государственных организаций США), Виргиния (для обслуживания государственных организаций США), центральная часть США (US DoD), восточная часть США (US DoD)  |Чикаго, Даллас, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **Китай** |Северный Китай, Восточный Китай |Пекин, Шанхай |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="locations"></a>Расположения поставщиков услуг подключения

В таблице ниже приведены расположения для подключения и поставщики услуг в каждом расположении. Поставщики услуг и расположения, в которых они предоставляют услуги, перечислены в разделе [Расположения поставщиков услуг подключения](expressroute-locations.md#locations). 


### <a name="production-azure"></a>Рабочая среда Azure
| **Местоположение.** | **Поставщики услуг** |
| --- | --- |
| **Амстердам** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions — Cloud Connect, Interxion, KPN, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Verizon, Zayo |
| **Атланта** |Equinix |
| **Пусан** |LG CNS |
| **Ченнай** | Airtel+, Global CloudXchange (GCX), SIFY, Tata Communications |
| **Чикаго** |AT&T NetBond, Comcast, Coresite, Equinix, PacketFabric, Level 3 Communications, Megaport, Verizon, Zayo |
| **Даллас** |Aryaka Networks, AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport, Telmex Uninet+, Verizon, Zayo|
| **Денвер** |CoreSite, Megaport |
| **Дублин** |Colt, eir, Interxion, Megaport, Telecity Group |
| **Специальный административный регион Гонконг** |Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Лас-Вегас** |Level 3 Communications, Megaport |
| **Лондон** |AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions — Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telehouse — KDDI, Telenor, Verizon, Vodafone, Zayo |
| **Лос-Анджелес** |CoreSite, Equinix, Megaport, NTT, Zayo |
| **Мельбурн** |AARNet, Equinix, Megaport, NEXTDC, Optus+, Telstra Corporation |
| **Майами** |C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Монреаль** |Bell Canada, Cologix, Telus, Zayo |
| **Мумбай** |Airtel+, Global CloudXchange (GCX), Sify, Tata Communications |
| **Нью-Йорк** |CenturyLink Cloud Connect, Coresite, Equinix, Megaport, Zayo |
| **Ньюпорт (Уэльс)** |Level 3 Communications, Next Generation Data |
| **Осака** |Equinix, Internet Initiative Japan Inc. (IIJ), NTT Communications, NTT SmartConnect, Softbank |
| **Париж** |Colt, Intercloud, Interxion, Equinix, Orange |
| **Квебек** | Bell Canada, Megaport |
| **Сан-Антонио** |CenturyLink Cloud Connect, Megaport |
| **Сан-Паулу** |Ascenty Data Centers+, Equinix, Level 3 Communications, Neutrona Networks, Telefonica, UOLDIVEO |
| **Сиэтл;** |Equinix, Level 3 Communications, Megaport |
| **Сеул** |KINX, LG CNS, Sejong Telecom |
| **Кремниевая долина** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Coresite, Equinix, PacketFabric, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |
| **Сингапур** |Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Verizon |
| **Сингапур 2** |Megaport, SingTel |
| **Сидней** |AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, Verizon |
| **Токио** |Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Торонто** |AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Console, Equinix, Megaport, Telus, Zayo |
| **Вашингтон, округ Колумбия** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, Sprint, Tata Communications, Verizon, Zayo |

 **+** означает "скоро"

### <a name="national-cloud-environments"></a>Национальные облачные среды

### <a name="us-government-cloud"></a>Облако правительства США
| **Местоположение.** | **Поставщики услуг** |
| --- | --- |
| **Чикаго** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Даллас** |Equinix, Megaport, Verizon |
| **Нью-Йорк** |Equinix, Level 3 Communications+, Verizon |
| **Кремниевая долина** | Equinix, Level 3 Communications |
| **Сиэтл;** | Equinix |
| **Вашингтон, округ Колумбия** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |

### <a name="china"></a>Китай
| **Местоположение.** | **Поставщики услуг** |
| --- | --- |
| **Пекин** |China Telecom |
| **Шанхай** |China Telecom |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Берлин** |Colt+, e-shelter, Megaport+, T-Systems |
| **Франкфурт** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Подключение через поставщиков Exchange
Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equnix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](http://www.interxion.com/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

## <a name="c1partners"></a>Подключение через дополнительных поставщиков услуг
| **Местоположение.** | **Exchange** | **Поставщики услуг подключения** |
| --- | --- | --- |
| **Амстердам** | Equinix, Telecity | BICS, Eurofiber, Fastweb S.p.A, MainOne, Nianet, Post, Proximus, Telia |
| **Чикаго** | Equinix | Lightower, Windstream |
| **Даллас** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **Франкфурт** | Telecity | BICS, Nianet, QSC AG |
| **Специальный административный регион Гонконг** | Equinix | Macroview Telecom |
| **Лондон** | Equinix, euNetworks, Telecity | Bezeq International Ltd., Epsilon, Exponential E, HSO, NexGen Networks, Tamares Telecom, Zain |
| **Лос-Анджелес** | Equinix |Transtelco |
| **Мадрид** | Level3 | Zertia |
| **Монреаль** | Cologix, Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Zirro |
| **Нью-Йорк** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **Сиэтл;** |Equinix | Alaska Communications |
| **Кремниевая долина** |Equinix | Cox Business, Windstream |
| **Сингапур** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Сидней** | Megaport | Macquarie Telecom Group|
| **Токио** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Торонто** | Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Thinktel, Zirro|
| **Вашингтон, округ Колумбия** |Equinix | Altice Business, BICS, Gtt Communications Inc, Epsilon, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Континент** | **Системные интеграторы** |
| --- | --- |
| **Азия** |Avanade Inc., OneAs1a |
| **Австралия** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Европа** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Северная Америка** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Северная Америка** |Avanade Inc. |
## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"
