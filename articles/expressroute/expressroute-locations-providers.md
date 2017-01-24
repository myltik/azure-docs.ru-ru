---
title: "Расположения ExpressRoute | Документация Майкрософт"
description: "В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 8b82801a26c15248480a261945b37a0c38e3fa47
ms.openlocfilehash: e32d21efd8d90bac223cbc0a213df65d19c40670


---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute
В данной статье приведены таблицы со сведениями о поставщиках услуг подключения ExpressRoute, географическом покрытии ExpressRoute, облачных службах Майкрософт, поддерживаемых через ExpressRoute, и системных интеграторах ExpressRoute.

## <a name="a-namepartnersaexpressroute-connectivity-providers"></a><a name="partners"></a>Поставщики услуг подключения ExpressRoute
ExpressRoute поддерживается во всех регионах и расположениях Azure. На следующей карте обозначены регионы Azure и расположения ExpressRoute. Расположения ExpressRoute соответствуют тем территориям, где Майкрософт взаимодействует с несколькими одноранговыми поставщиками услуг.

![Карта расположения][0]

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Регионы Azure с расположениями ExpressRoute в пределах геополитических регионов
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Северная Америка** |Восточная часть США, западная часть США, восточная часть США 2, центральная часть США, юго-центральная часть США, северо-центральная часть США, центральная часть Канады, восточная часть Канады |Атланта, Чикаго, Даллас, Лас-Вегас, Лос-Анджелес, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон, округ Колумбия, Монреаль+, Квебек+, Торонто |
| **Северная Америка** |Южная часть Бразилии |Сан-Паулу |
| **Европа** |Северная Европа, Западная Европа, запад Соединенного Королевства, юг Соединенного Королевства |Амстердам, Дублин, Лондон, Ньюпорт (Уэльс), Париж |
| **Азия** |Восточная Азия, Юго-Восточная Азия |Гонконг, Сингапур |
| **Япония** |Западная Япония, Восточная Япония |Осака, Токио |
| **Австралия** |Восточная Австралия, Юго-Восточная Австралия |Мельбурн, Сидней |
| **Индия** |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Мумбаи |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- | --- |
| **Облако правительства США** |Правительство штата Айова, США, Правительство штата Вирджиния, США |Чикаго, Даллас, Нью-Йорк, Вашингтон (округ Колумбия) |
| **Китай** |Северный Китай, Восточный Китай |Пекин, Шанхай |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="a-namelocationsaconnectivity-provider-locations"></a><a name="locations"></a>Расположения поставщиков услуг подключения
> [!div class="op_single_selector"]
> * [Расположения по поставщикам](expressroute-locations.md#locations)
> * [Поставщики по расположению](expressroute-locations-providers.md#locations)
> 
> 

### <a name="production-azure"></a>Рабочая среда Azure
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Амстердам** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions - Cloud Connect, Interxion, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor, Verizon |
| **Атланта** |Equinix |
| **Ченнай** |SIFY, Tata Communications |
| **Чикаго** |AT&T NetBond, Comcast, Equinix, Level 3 Communications, Zayo Group |
| **Даллас** |Aryaka Networks, AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport |
| **Дублин** |Colt, Telecity Group |
| **Гонконг** |British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Лондон** |AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc+, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon, Vodafone |
| **Лас-Вегас** |Level 3 Communications+, Megaport |
| **Лос-Анджелес** |CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Мельбурн** |AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Нью-Йорк** |Equinix, Megaport, Zayo Group |
| **Ньюпорт (Уэльс)** |Next Generation Data |
| **Монреаль** |Cologix + |
| **Мумбай** |Tata Communications |
| **Осака** |Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank |
| **Париж** |Interxion, Equinix+ |
| **Сан-Паулу** |Equinix Telefonica |
| **Сиэтл;** |Equinix, Level 3 Communications, Megaport |
| **Кремниевая долина** |Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Console, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon, Zayo Group |
| **Сингапур** |Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Verizon |
| **Сидней** |AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Токио** |Aryaka Networks, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Торонто** |Bell Canada, Cologix, Equinix, Megaport, Zayo Group |
| **Вашингтон, округ Колумбия** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Verizon, Zayo Group |

 **+** означает "скоро"

### <a name="national-cloud-environments"></a>Национальные облачные среды

### <a name="us-government-cloud"></a>Облако правительства США
| **Расположение** | **Поставщики услуг** |
| --- | --- |
| **Чикаго** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Даллас** |Equinix, Verizon |
| **Нью-Йорк** |Equinix, Level 3 Communications+, Verizon |
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
| **Берлин** |Colt+, e-shelter |
| **Франкфурт** |Colt, Equinix, Interxion |

## <a name="a-namenonpartnersaconnectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>Подключение через других поставщиков услуг
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
| **Лондон** | Equinix | Exponential E |
| **Нью-Йорк** |Equinix |Lightower |
| **Сиэтл;** |Equinix |Alaska Communications |
| **Кремниевая долина** |Equinix |XO Communications |
| **Сингапур** |Equinix |1CLOUDSTAR |
| **Сидней** | Megaport | Macquarie |
| **Вашингтон, округ Колумбия** |Equinix | Lightower, Masergy |

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Континент** | **Системные интеграторы** |
| --- | --- |
| **Азия** |Avanade Inc., OneAs1a |
| **Европа** |Avanade Inc., Dotnet Solutions |
| **США** |Avanade Inc., Equinix Professional Services, Perficient, Project Leadership |

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. См. статью [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"



<!--HONumber=Jan17_HO1-->


