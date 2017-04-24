---
title: "Расположения и поставщики услуг подключения Azure ExpressRoute | Документация Майкрософт"
description: "В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по расположению."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 32b8cd51c983b2a66f3631ec399aa88e34555196
ms.lasthandoff: 04/17/2017

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
| **Северная Америка** |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, юго-центральная часть США, северо-центральная часть США, западно-центральная часть США, центральная часть Канады, восточная часть Канады |Атланта, Чикаго, Даллас, Лас-Вегас, Лос-Анджелес, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия), Монреаль, Квебек, Торонто |
| **Северная Америка** |Южная часть Бразилии |Сан-Паулу |
| **Европа** |Северная Европа, Западная Европа, запад Соединенного Королевства, юг Соединенного Королевства |Амстердам, Дублин, Лондон, Ньюпорт (Уэльс), Париж |
| **Азия** |Восточная Азия, Юго-Восточная Азия |Гонконг, Сингапур |
| **Япония** |Западная Япония, Восточная Япония |Осака, Токио |
| **Австралия** |Восточная Австралия, Юго-Восточная Австралия |Мельбурн, Сидней |
| **Индия** |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Мумбаи |
| **Южная Корея** |Центральная Корея, Южная Корея |Пусан, Сеул |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- | --- |
| **Облако правительства США** |Айова (для обслуживания государственных организаций США), Виргиния (для обслуживания государственных организаций США), центральная часть США (US DoD), восточная часть США (US DoD)  |Чикаго, Даллас, Нью-Йорк, Кремниевая долина, Вашингтон (округ Колумбия) |
| **Китай** |Северный Китай, Восточный Китай |Пекин, Шанхай |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="locations"></a>Расположения поставщиков услуг подключения

В таблице ниже приведены расположения для подключения и поставщики услуг в каждом расположении. Поставщики услуг и расположения, в которых они предоставляют услуги, перечислены в разделе [Расположения поставщиков услуг подключения](expressroute-locations.md#locations). 


### <a name="production-azure"></a>Рабочая среда Azure
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Амстердам** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions - Cloud Connect, Interxion, KPN, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telefonica+, Telenor, Verizon |
| **Атланта** |Equinix |
| **Пусан** |LG CNS |
| **Ченнай** |Global CloudXchange (GCX), SIFY, Tata Communications |
| **Чикаго** |AT&T NetBond, Comcast, Equinix, Level 3 Communications, Verizon, Zayo Group |
| **Даллас** |Aryaka Networks, AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport, Verizon, Zayo Group+ |
| **Дублин** |Colt, Telecity Group |
| **Гонконг** |Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Лас-Вегас** |Level 3 Communications+, Megaport |
| **Лондон** |AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telehouse - KDDI, Telenor, Verizon, Vodafone, Zayo Group+ |
| **Лос-Анджелес** |CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Мельбурн** |AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Нью-Йорк** |Coresite, Equinix, Megaport, Zayo Group |
| **Ньюпорт (Уэльс)** |Next Generation Data |
| **Монреаль** |Bell Canada, Cologix |
| **Мумбай** |Airtel+, Tata Communications |
| **Осака** |Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank |
| **Париж** |Interxion, Equinix+ |
| **Квебек** | Megaport |
| **Сан-Паулу** |Equinix Telefonica |
| **Сиэтл;** |Equinix, Level 3 Communications, Megaport |
| **Сеул** |KINX, Sejong Telecom |
| **Кремниевая долина** |Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Console, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon, Zayo Group |
| **Сингапур** |Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Verizon |
| **Сидней** |AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Токио** |Aryaka Networks, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Торонто** |Bell Canada, Cologix, Console, Equinix, Megaport, Zayo Group |
| **Вашингтон, округ Колумбия** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Verizon, Zayo Group |

 **+** означает "скоро"

### <a name="national-cloud-environments"></a>Национальные облачные среды

### <a name="us-government-cloud"></a>Облако правительства США
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Чикаго** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Даллас** |Equinix, Megaport, Verizon |
| **Нью-Йорк** |Equinix, Level 3 Communications+, Verizon |
| **Кремниевая долина** | Equinix |
| **Сиэтл;** | Equinix+ |
| **Вашингтон, округ Колумбия** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |

### <a name="china"></a>Китай
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Пекин** |China Telecom |
| **Шанхай** |China Telecom |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Берлин** |Colt+, e-shelter, Megaport+ |
| **Франкфурт** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Подключение через других поставщиков услуг
Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equnix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](http://www.interxion.com/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

| **Расположение** | **Exchange** | **Поставщики услуг подключения** |
| --- | --- | --- |
| **Амстердам** | Equinix, Telecity | Eurofiber , Fastweb S.p.A, Nianet |
| **Чикаго** | Equinix | Windstream |
| **Даллас** | Equinix, Megaport | C3ntro, Data Foundry, Transtelco |
| **Франкфурт** | Telecity | Nianet, QSC AG |
| **Лондон** | Equinix, euNetworks, Telecity | Bezeq International Ltd., Exponential E, HSO, NexGen Networks, Tamares Telecom |
| **Лос-Анджелес** | Equinix |Transtelco |
| **Мадрид** | Level3 | Zertia |
| **Монреаль** | Cologix, Equinix | Airgate Technologies. Inc, Cogeco Peer 1 |
| **Нью-Йорк** |Equinix | Lightower |
| **Сиэтл;** |Equinix | Alaska Communications |
| **Кремниевая долина** |Equinix | Windstream |
| **Сингапур** |Equinix |1CLOUDSTAR, Epsilon Telecommunications Limited |
| **Slough** | Equinix | HSO|
| **Сидней** | Megaport | Macquarie Telecom Group|
| **Токио** | Equinix | ARTERIA Networks Corporation |
| **Торонто** | Equinix | Airgate Technologies. Inc, Cogeco Peer 1 |
| **Вашингтон, округ Колумбия** |Equinix | Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Континент** | **Системные интеграторы** |
| --- | --- |
| **Азия** |Avanade Inc., OneAs1a |
| **Австралия** | IT Consultancy, Vigilant.IT |
| **Европа** |Avanade Inc., Altogee, Bright Skies GmbH, Dotnet Solutions, MSG Services, Nelite, Orange Networks, sol-tec |
| **Северная Америка** |Avanade Inc., Equinix Professional Services, Perficient, Presidio, Project Leadership |
| **Северная Америка** |Avanade Inc. |
## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. См. статью [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"

