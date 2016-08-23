<properties
   pageTitle="Расположения ExpressRoute | Microsoft Azure"
   description="В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="cherylmc" />

# Партнеры и одноранговые расположения ExpressRoute

В данной статье приведены таблицы со сведениями о поставщиках услуг подключения ExpressRoute, географическом покрытии ExpressRoute, облачных службах Майкрософт, поддерживаемых через ExpressRoute, и системных интеграторах ExpressRoute.

## <a name="partners"></a>Поставщики услуг подключения ExpressRoute

ExpressRoute поддерживается во всех регионах и расположениях Azure. На следующей карте обозначены регионы Azure и расположения ExpressRoute. Расположения ExpressRoute соответствуют тем территориям, где Майкрософт взаимодействует с несколькими одноранговыми поставщиками услуг.

![Карта расположения][0]

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе. В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

|**Геополитический регион**|**Регионы Azure**|**Расположения ExpressRoute**|
|---|---|---|
|**Северная Америка**|Восточная часть США, западная часть США, восточная часть США 2, центральная часть США, юго-центральная часть США, северо-центральная часть США, центральная часть Канады, восточная часть Канады|Атланта, Чикаго, Даллас, Лас-Вегас+, Лос-Анджелес, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия), Монреаль+, Квебек+, Торонто|
|**Северная Америка**|Южная часть Бразилии|Сан-Паулу|
|**Европа**|Северная Европа, Западная Европа|Амстердам, Дублин, Лондон, Ньюпорт (Уэльс)+, Париж|
|**Азия**|Восточная Азия, Юго-Восточная Азия|Гонконг, Сингапур|
|**Япония**|Западная Япония, Восточная Япония|Осака, Токио|
|**Австралия**|Восточная Австралия, Юго-Восточная Австралия|Мельбурн, Сидней|
|**Индия**|Западная Индия, Центральная Индия, Южная Индия|Ченнаи, Мумбаи|



В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

|**Геополитический регион**|**Регионы Azure**|**Расположения ExpressRoute**|
|---|---|---|---|
|**Облако правительства США**|Правительство штата Айова, США, Правительство штата Вирджиния, США|Чикаго, Даллас+, Нью-Йорк, Вашингтон (округ Колумбия)|
|**Китай**|Северный Китай, Восточный Китай|Пекин, Шанхай|
|**Германия**|Центральная Германия, восточная Германия|Берлин, Франкфурт|


В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.


## Расположения поставщиков услуг подключения

> [AZURE.SELECTOR]
[Locations By Provider](expressroute-locations.md#connectivity-provider-locations)
[Providers By Location](expressroute-locations-providers.md#connectivity-provider-locations)

### Рабочая среда Azure
| **Расположение** | **Поставщики услуг** |
|---------------|-----------------------|
| **Амстердам** | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT+, InterCloud, Internet Solutions - Cloud Connect, Interxion, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor, Verizon |
| **Атланта** | Equinix |
| **Ченнай** | Tata Communications |
| **Чикаго** | AT&T NetBond, Comcast, Equinix, Level 3 Communications, Zayo Group |
| **Даллас** | AT&T NetBond, Equinix, Level 3 Communications, Megaport |
| **Дублин** | Colt |
| **Гонконг** | British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Лондон** | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc+, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon, Vodafone |
| **Лас-Вегас** | Level 3 Communications+, Megaport
| **Лос-Анджелес** | CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Мельбурн** | Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Нью-Йорк** | Equinix, Megaport, Zayo Group |
| **Монреаль** | Cologix + |
| **Мумбай** | Tata Communications |
| **Осака** | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank |
| **Париж** | Interxion |
| **Сан-Паулу** | Equinix, Telefonica+ |
| **Сиэтл;** | Equinix, Level 3 Communications, Megaport |
| **Кремниевая долина** | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon, Zayo Group |
| **Сингапур** | Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Megaport, Orange, SingTel, Tata Communications, Verizon |
| **Сидней** | AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Telstra Corporation, Verizon |
| **Токио** | Aryaka Networks, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Торонто** | Cologix, Equinix, Zayo Group |
| **Вашингтон, округ Колумбия** | Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |

 Знак **+** означает "скоро"

### Национальные облачные среды

#### Облако правительства США

| **Расположение** |**Поставщики услуг** |
|---------------|--------------------|
| **Чикаго** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Даллас** | Equinix+, Verizon+ |
| **Нью-Йорк** | Equinix, Level 3 Communications+, Verizon |
| **Вашингтон, округ Колумбия** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |

#### Китай

| **Расположение** | **Поставщики услуг** |
|---------------|-----------------------|
| **Пекин** | China Telecom |
| **Шанхай** | China Telecom |
Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/)

#### Германия

| **Расположение** | **Поставщики услуг** |
|---------------|-----------------------|
| **Берлин** | Colt+, e-shelter+ |
| **Франкфурт** | Colt, Equinix+, Interxion |

## <a name="nonpartners"></a>Подключение через других поставщиков услуг

Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

- Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.

	- [Equnix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
	- [Cologix](http://www.cologix.com/)
- Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
	- Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
- Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
	- Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md).

|**Расположение**|**Exchange**|**Поставщики услуг подключения**|
|-------------|------------|-------------------------|
| **Нью-Йорк** | Equinix | Lightower |
| **Сиэтл;** | Equinix | Alaska Communications |
| **Кремниевая долина** | Equinix | XO Communications |
| **Сингапур** | Equinix | 1CLOUDSTAR |
| **Вашингтон, округ Колумбия** | Equinix | Lightower |

## Системные интеграторы ExpressRoute

Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

|**Континент**|**Системные интеграторы**|
|-------------|---------------------|
| **Азия** | Avanade Inc., OneAs1a|
| **Европа** | Avanade Inc., Dotnet Solutions|
| **США** | Avanade Inc., Equinix Professional Services, Perficient, Project Leadership|

## Дальнейшие действия

- Дополнительную информацию об ExpressRoute см. в статье [Часто задаваемые вопросы об ExpressRoute](expressroute-faqs.md).
- Убедитесь, что выполнены все необходимые условия. См. статью [Предварительные требования для ExpressRoute](expressroute-prerequisites.md).

.<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"

<!---HONumber=AcomDC_0817_2016-->