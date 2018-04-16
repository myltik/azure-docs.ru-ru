---
title: Поставщики услуг подключения и расположения Azure ExpressRoute | Документация Майкрософт
description: В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по поставщику услуг подключения.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: pareshmu
ms.openlocfilehash: e675600b326bb5269f8bb91aaa067a5842c0f8dd
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
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
| **Европа** |Северная Европа, Западная Европа, запад Соединенного Королевства, юг Соединенного Королевства |Амстердам, Дублин, Лондон, Ньюпорт (Уэльс), Париж |
| **Азия** |Восточная Азия, Юго-Восточная Азия |Гонконг, Сингапур, Сингапур 2 |
| **Япония** |Западная Япония, Восточная Япония |Осака, Токио |
| **Австралия** |Восточная Австралия, Юго-Восточная Австралия |Мельбурн, Сидней |
| **Индия** |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Мумбаи |
| **Южная Корея** |Центральная Корея, Южная Корея |Пусан, Сеул |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Облако правительства США** |Айова (для обслуживания государственных организаций США), Аризона (для обслуживания государственных организаций США), Виргиния (для обслуживания государственных организаций США), Техас (для обслуживания государственных организаций США), центральная часть США (US DOD), восточная часть США (US DOD)  |Чикаго, Даллас, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **Китай** |Северный Китай, Восточный Китай |Пекин, Шанхай |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="locations"></a>Расположения поставщиков услуг подключения

В следующей таблице показаны расположения по поставщикам услуг. См. дополнительные сведения о [службах доступных поставщиков по расположению](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Рабочая среда Azure
| **Поставщик услуг** | **Microsoft Azure** | **Office 365 и Dynamics 365** | **Расположения** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[Airtel](http://www.airtel.in/business/connexion)** | Поддерживаются | Поддерживаются | Ченнаи, Мумбаи |
| **[Aryaka Networks](http://www.aryaka.com/)** |Поддерживаются |Поддерживаются |Амстердам, Даллас, Гонконг, Кремниевая долина, Сингапур, Токио, Вашингтон (округ Колумбия) |
| **[Ascenty Data Centers](https://ascenty.com/solucoes/conectividade-e-interconexoes/Microsoft-express-route/)** |Скоро |Скоро |Сан-Паулу |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживаются |Поддерживаются |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Сидней, Сингапур, Токио, Торонто, Чикаго |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Поддерживаются |Поддерживаются |Квебек, Мурманск, Торонто |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[C3ntro](https://c3ntro.com/)** |Скоро |Скоро |Майами |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Поддерживаются |Поддерживаются |Нью-Йорк, Сан-Антонио, Токио, Торонто |
| **China Telecom Global** |Поддерживаются |Не поддерживается |Гонконг |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Поддерживаются |Поддерживаются |Даллас, Монреаль, Торонто |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживаются |Поддерживаются |Амстердам, Дублин, Лондон, Париж, Токио |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Поддерживаются |Поддерживаются |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Поддерживаются |Поддерживаются |Чикаго, Денвер, Лос-Анджелес, Нью-Йорк, Кремниевая долина, Вашингтон (округ Колумбия) |
| **eir** |Поддерживаются |Поддерживаются |Дублин|
| **Epsilon Global Communications** |Поддерживаются |Поддерживаются |Сингапур |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Поддерживаются |Амстердам, Атланта, Вашингтон (округ Колумбия), Гонконг (САР), Даллас, Кремниевая долина, Лондон, Лос-Анджелес, Майами, Мельбурн, Нью-Йорк, Осака, Париж, Сан-Пауло, Сидней, Сингапур, Сиэтл, Токио, Торонто, Чикаго |
| **euNetworks** |Поддерживаются |Поддерживаются |Амстердам, Лондон |
| **GÉANT** |Поддерживаются |Поддерживаются |Амстердам |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Поддерживаются| Поддерживаются | Ченнаи, Мумбаи |
| **[InterCloud](https://www.intercloud.com/)** |Поддерживаются |Поддерживаются |Амстердам, Лондон, Париж, Сингапур, Вашингтон (округ Колумбия) |
| **Internet2** |Поддерживаются |Поддерживаются |Вашингтон, округ Колумбия |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Поддерживаются |Поддерживаются |Осака, Токио |
| **Internet Solutions - Cloud Connect** |Поддерживаются |Поддерживаются |Амстердам, Лондон |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Поддерживаются |Поддерживаются |Амстердам, Дублин, Лондон, Париж |
| **[IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)**|Поддерживаются |Поддерживаются |Торонто
| **Jisc** |Поддерживаются |Поддерживаются |Лондон |
| **KINX** |Поддерживаются |Поддерживаются |Сеул |
| **[KPN](http://www.kpn.com/cloudconnect)** | Поддерживаются | Поддерживаются | Амстердам | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживаются |Поддерживаются |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лас-Вегас, Лондон, Ньюпорт, Сан-Паулу, Сингапур, Сиэтл, Чикаго |
| **LG CNS** |Поддерживаются |Поддерживаются |Пусан, Сеул |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Даллас, Денвер, Дублин, Гонконг, Лас-Вегас, Лондон, Лос-Анджелес, Мельбурн, Майами, Нью-Йорк, Квебек, Сан-Антонио, Сиэтл, Кремниевая долина, Сингапур, Сингапур 2, Сидней, Торонто, Вашингтон |
| **MTN** |Поддерживаются |Поддерживаются |Лондон |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Поддерживаются |Поддерживаются |Майами, Сан-Паулу |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Поддерживаются |Поддерживаются |Ньюпорт (Уэльс) |
| **NEXTDC** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, Лондон, Лос-Анджелес, Осака, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[NTT SmartConnect](http://cloud.nttsmc.com/cxc/azure.html)** |Поддерживаются |Поддерживаются |Осака |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Поддерживаются |Поддерживаются |Мельбурн+, Сидней |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, Лондон, Париж, Кремниевая долина, Сингапур, Сидней, Вашингтон (округ Колумбия) |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Поддерживаются |Поддерживаются |Чикаго, Кремниевая долина |
| **PCCW Global Limited** |Поддерживаются |Поддерживаются |Гонконг |
| **Sejong Telecom** |Поддерживаются |Поддерживаются |Сеул |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Поддерживаются |Поддерживаются |Ченнаи, Мумбаи |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Поддерживаются |Поддерживаются |Сингапур, Сингапур 2 |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Поддерживаются |Поддерживаются |Осака, Токио |
| **[Спринт](https://business.sprint.com/solutions/cloud-networking/)** |Поддерживаются |Поддерживаются |Вашингтон, округ Колумбия |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Поддерживаются |Поддерживаются |Амстердам, Ченнаи, Гонконг, Лондон, Мумбаи, Кремниевая долина, Сингапур, Вашингтон (округ Колумбия) |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Поддерживаются |Поддерживаются |Амстердам, Дублин, Лондон |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Поддерживаются |Поддерживаются |Амстердам, Сан-Паулу |
| **[Telehouse — KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Поддерживаются |Поддерживаются |Лондон |
| **Telmex Uninet**| Скоро | Скоро | Dallas+ |
| **Telenor** |Поддерживаются |Поддерживаются |Амстердам, Лондон |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[Telus](http://www.telus.com)** |Поддерживаются |Поддерживаются |Монреаль, Торонто |
| **[UOLDIVEO](http://www.uoldiveo.com.br/solucoes/cloud.html#rmcl)** |Поддерживаются |Поддерживаются |Сан-Паулу |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Даллас, Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Поддерживаются |Не поддерживается |Лондон |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Поддерживаются |Поддерживаются |Амстердам, Вашингтон (округ Колумбия), Даллас, Кремниевая долина, Лондон, Лос-Анджелес, Монреаль, Нью-Йорк, Торонто, Чикаго |

 **+** означает "скоро"

### <a name="national-cloud-environment"></a>Национальная облачная среда

### <a name="us-government-cloud"></a>Облако правительства США
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживаются |Поддерживаются |Чикаго, Вашингтон (округ Колумбия) |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживаются |Поддерживаются |Вашингтон (округ Колумбия), Кремниевая долина, Нью-Йорк+, Чикаго |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются | Поддерживаются | Чикаго, Даллас |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Нью-Йорк, Вашингтон (округ Колумбия) |

### <a name="china"></a>Китай
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **China Telecom** |Поддерживаются |Не поддерживается |Пекин, Шанхай |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживаются |Не поддерживается |Берлин+, Франкфурт |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Не поддерживается |Франкфурт |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Поддерживаются |Не поддерживается |Берлин |
| **Interxion** |Поддерживаются |Не поддерживается |Франкфурт |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются  | Не поддерживается | Берлин |
| **T-Systems** |Поддерживаются |Не поддерживается |Берлин |

## <a name="connectivity-through-exchange-providers"></a>Подключение через поставщиков Exchange

Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equnix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-additional-service-providers"></a>Подключение через дополнительных поставщиков услуг

| **Поставщик услуг подключения** | **Exchange** | **Расположения** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Сингапур |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Торонто, Монреаль |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Сиэтл; |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Нью-Йорк, Вашингтон (округ Колумбия) |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Токио |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Лондон |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Амстердам, Вашингтон (округ Колумбия), Лондон, Сингапур,Франкфурт |
| **[BroadBand Tower, Inc.](http://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Токио |
| **[C3ntro Telecom](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Даллас |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Франкфурт, Германия |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Монреаль, Торонто |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Даллас, Кремниевая долина, Вашингтон, округ Колумбия | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Даллас |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Лондон, Сингапур, Вашингтон, округ Колумбия |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Амстердам |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Лондон |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Амстердам |
| **[Gtt Communications Inc](https://www.gtt.net/wp-content/uploads/2017/04/EtherCloud-Data-Sheet.pdf)** |Equinix | Вашингтон, округ Колумбия |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Лондон, Слау |
| **LGA Telecom** |Equinix |Сингапур|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Вашингтон (округ Колумбия), Нью-Йорк, Чикаго |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Гонконг |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Сидней |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Амстердам |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Вашингтон, округ Колумбия |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Лондон |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Амстердам, Франкфурт |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Франкфурт |  
| **Rogers** | Cologix, Equinix | Монреаль, Торонто |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Лондон | 
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Амстердам |
| **[ThinkTel](http://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Торонто | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Даллас, Лос-Анджелес |  
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Сингапур |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Нью-Йорк |
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **Zain** |Equinix |Лондон|
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Мадрид |
| **[Zirro](https://zirro.com/services/)**| Equinix | Монреаль, Торонто |

## <a name="connectivity-through-datacenter-providers"></a>Подключение с помощью поставщиков центров обработки данных
| **Поставщик** | **Exchange** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire Data Centers](http://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 Datacenters](http://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Подключение через национальные исследовательские и образовательные сети (NREN)

| **Поставщик**|
| --- |
| **AARNET**| 
| **DeIC, через GÉANT**|
| **GARR, через GÉANT**|
| **GÉANT**|
| **HEAnet, через GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, через GÉANT**|
| **SINET**|
| **Surfnet, через GÉANT**|

* Если вашего поставщика услуг подключения нет в списке, проверьте, подключены ли они к любому из перечисленных выше партнеров ExpressRoute Exchange.

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Системный интегратор** | **Континент** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | Европа |
| **[Avanade Inc.](http://www.avanade.com/)** | Азия, Европа, Северная Америка, Южная Америка |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Европа
| **[Ensyst](http://www.ensyst.com.au)** | Азия
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Северная Америка |
| **[FlexManage](http://www.flexmanage.com/cloud)** | Северная Америка |
| **[Inframon](http://www.inframon.com/partner/microsoft/)** | Европа |
| **[Lightstream](https://www.ltstream.com/expressroute)** | Северная Америка |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Австралия |
| **[MOQdigital](http://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Австралия |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Европа (Германия) |
| **[Nelite](http://nelite.com/)** | Европа |
| **[New Signature](https://www.newsignature.co.uk/)** | Европа |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Азия |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Европа |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Северная Америка |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | Северная Америка |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | Европа |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Австралия |


## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. Ознакомьтесь с разделом [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"
