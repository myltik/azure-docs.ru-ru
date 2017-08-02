---
title: "Часто задаваемые вопросы по Azure ExpressRoute | Документация Майкрософт"
description: "В разделе \"Вопросы и ответы по ExpressRoute\" содержатся сведения о поддерживаемых службах Azure, стоимости, данных и подключениях, соглашении об уровне обслуживания, поставщиках и расположениях, пропускной способности, а также дополнительные технические сведения."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/14/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: fc11bf194b648cefea32feada4fe9e7fb2119fff
ms.contentlocale: ru-ru
ms.lasthandoff: 07/15/2017

---
# <a name="expressroute-faq"></a>Вопросы и ответы по ExpressRoute
## <a name="what-is-expressroute"></a>Что такое ExpressRoute?
ExpressRoute — это служба Azure, которая позволяет создавать частные подключения между центрами обработки данных Microsoft и инфраструктурой вашей локальной среды или среды для совместной работы. Подключения ExpressRoute не осуществляются через общедоступный Интернет и обеспечивают повышенный уровень безопасности, надежности и быстродействия с низким уровнем задержки по сравнению с обычными подключениями через Интернет.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Каковы преимущества использования ExpressRoute и частных сетевых подключений?
Подключения ExpressRoute не осуществляются через общедоступный Интернет и обеспечивают повышенный уровень безопасности, надежности и быстродействия с низкими и последовательными задержками по сравнению с обычными подключениями через Интернет. В некоторых случаях использование подключений ExpressRoute для передачи данных между локальными устройствами и Azure дает возможность значительно сократить затраты.

### <a name="where-is-the-service-available"></a>Где доступна эта служба?
Сведения о расположении этой службы и ее доступности см. на странице [Партнеры и расположения ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Как использовать ExpressRoute для подключения к Microsoft, если нет соглашений ни с одним из партнеров-поставщиков ExpressRoute?
Вы можете выбрать регионального поставщика и получить подключения Ethernet к одному из поддерживаемых расположений поставщиков Exchange. Затем вы можете взаимодействовать с Майкрософт в этом расположении поставщиков. Проверьте, есть ли ваш поставщик услуг в расположениях Exchange, в последнем разделе на странице [Партнеры и расположения ExpressRoute](expressroute-locations.md) . Затем можно заказать канал ExpressRoute через поставщика услуг, чтобы подключиться к Azure.

### <a name="how-much-does-expressroute-cost"></a>Сколько стоит ExpressRoute?
Сведения о ценах см. на [этой странице](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Если я оплачиваю канал ExpressRoute определенной пропускной способности, должно ли подключение VPN, приобретенное у моего поставщика сетевых услуг, иметь ту же скорость?
Нет. Вы можете приобрести у своего поставщика услуг VPN-подключение любой скорости. Однако ваше подключение к Azure будет ограничено пропускной способностью приобретенного вами канала ExpressRoute.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-required"></a>Если я оплачиваю канал ExpressRoute определенной пропускной способности, могу ли я при необходимости быстро увеличить его скорость?
Да. Каналы ExpressRoute настраиваются для поддержки ситуаций, когда вы можете вдвое повысить приобретенную пропускную способность без дополнительной оплаты. Уточните у своего поставщика услуг, поддерживает ли он эту возможность.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Можно ли использовать одно и то же частное сетевое подключение с виртуальной сетью и другими службами Azure одновременно?
Да. После настройки канал ExpressRoute будет позволять вам доступ к службам в виртуальной сети и к другим службам Azure одновременно. Вы будете подключаться к виртуальным сетям по пути частного пиринга, а к другим службам — по пути общедоступного пиринга.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Предлагается ли с ExpressRoute соглашение об уровне обслуживания (SLA)?
Дополнительные сведения см. на [странице соглашения об уровне обслуживания для ExpressRoute](https://azure.microsoft.com/support/legal/sla/).

## <a name="supported-services"></a>Поддерживаемые службы
ExpressRoute поддерживает [три домена маршрутизации](expressroute-circuit-peerings.md) для разных типов служб.

### <a name="private-peering"></a>Частный пиринг
* Виртуальные сети, в том числе все виртуальные машины и облачные службы.

### <a name="public-peering"></a>Общедоступный пиринг
* Power BI
* Dynamics 365 for Operations (ранее — Dynamics AX Online).
* Большинство служб Azure, за исключением перечисленных ниже.
  * CDN
  * Нагрузочное тестирование Visual Studio Team Services
  * Многофакторная Идентификация
  * Диспетчер трафика

### <a name="microsoft-peering"></a>Пиринг Майкрософт
* [Office 365](http://aka.ms/ExpressRouteOffice365)
* Dynamics 365 (ранее — CRM Online)
  * Dynamics 365 for Sales;
  * Dynamics 365 for Customer Service;
  * Dynamics 365 for Field Service;
  * Dynamics 365 for Project Service.

## <a name="data-and-connections"></a>Данные и подключения
### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Существуют ли ограничения на объем данных, передаваемых посредством ExpressRoute?
Мы не устанавливаем ограничения на объем передачи данных. Сведения о ценах на пропускную способность см. на [этой странице](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Какие скорости подключения поддерживаются в ExpressRoute?
Поддерживаемая пропускная способность:

|50 Мбит/с, 100 Мбит/с, 200 Мбит/с, 500 Мбит/с, 1 Гбит/с, 2 Гбит/с, 5 Гбит/с, 10 Гбит/с|

### <a name="which-service-providers-are-available"></a>Какие поставщики услуг доступны?
Список поставщиков и расположений услуг см. в разделе [Партнеры и расположения ExpressRoute](expressroute-locations.md).

## <a name="technical-details"></a>Технические сведения
### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Каковы технические требования для подключения моего локального расположения к Azure?
Требования см. на [странице предварительных требований ExpressRoute](expressroute-prerequisites.md).

### <a name="are-connections-to-expressroute-redundant"></a>Обладают ли подключения к ExpressRoute избыточностью?
Да. Каждый канал ExpressRoute имеет избыточную пару перекрестных подключений для обеспечения высокой доступности.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Будет ли потеряно подключение в случае сбоя одной из моих связей ExpressRoute?
Вы не потеряете подключение при сбое одного из перекрестных подключений. Для поддержки нагрузки вашей сети предусмотрено избыточное подключение. Вы можете дополнительно создать несколько каналов в другом расположении пиринга для обеспечения отказоустойчивости.

### <a name="onep2plink"></a>Если у меня отсутствует совместное размещение в Cloud Exchange, а поставщик услуг предлагает подключение "точка – точка", мне нужно заказать два физических подключения между моей локальной сетью и сетью Майкрософт?
Нет, требуется только одно физическое подключение, если ваш поставщик услуг может установить два виртуальных канала Ethernet через физическое подключение. Физическое подключение (например оптоволоконное) прерывается на устройстве уровня 1 (L1) (см. изображение ниже). Два виртуальных канала Ethernet имеют разные идентификаторы виртуальной локальной сети — один для основного канала и один для дополнительного. Эти идентификаторы виртуальной локальной сети находятся во внешнем заголовке 802.1Q Ethernet. Внутренний заголовок 802.1Q Ethernet (не показан) сопоставляется с конкретным [доменом маршрутизации ExpressRoute](expressroute-circuit-peerings.md). 

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Можно ли расширить одну из своих виртуальных локальных сетей до Azure с помощью ExpressRoute?
Нет. Мы не поддерживаем расширения подключений второго уровня до Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Можно ли получить в своей подписке несколько каналов ExpressRoute?
Да. Вы можете получить в своей подписке несколько каналов ExpressRoute. По умолчанию ограничение составляет 10. Если вам требуется увеличить это количество, обратитесь в службу поддержки Майкрософт.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Можно ли использовать каналы ExpressRoute от разных поставщиков услуг?
Да. Вы можете использовать каналы ExpressRoute от нескольких поставщиков услуг. Каждый канал ExpressRoute будет связан только с одним поставщиком услуг. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-location"></a>Можно ли использовать несколько каналов ExpressRoute в одном расположении?
Да. Вы можете использовать несколько каналов ExpressRoute от одного или нескольких разных поставщиков службы в одном расположении. Однако будет невозможно связать с одной и той же виртуальной сетью более одного канала ExpressRoute из одного расположения.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Как подключить мои виртуальные сети к каналу ExpressRoute?
Основные шаги приводятся ниже.

* Необходимо установить канал ExpressRoute и обратиться к поставщику услуг, чтобы он включил этот канал.
* Вы или поставщик должны настроить пиринг BGP.
* Необходимо связать виртуальную сеть с каналом ExpressRoute.

Дополнительные сведения см. в разделе [Процедуры ExpressRoute для подготовки каналов и состояний каналов](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Существуют границы подключения к каналу ExpressRoute?
Да. Обзор границ подключения для канала ExpressRoute см. на [странице партнеров и расположений ExpressRoute](expressroute-locations.md). Возможность подключения к каналу ExpressRoute ограничена одной геополитической областью. Подключение можно расширить за пределы границ геополитической области, подключив расширенную функцию ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Можно связать несколько виртуальных сетей с каналом ExpressRoute?
Да. Можно создать до 10 подключений к виртуальной сети на канал ExpressRoute уровня "Стандартный" и до 100 — на [канал ExpressRoute уровня "Премиум"](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>У меня есть несколько подписок Azure, содержащих виртуальные сети. Можно ли подключить к одному каналу ExpressRoute виртуальные сети, являющиеся отдельными подписками?
Да. Вы можете авторизовать до 10 разных подписок Azure для использования одного канала ExpressRoute. Это ограничение можно увеличить, подключив расширенную функцию ExpressRoute.

Дополнительные сведения см. в разделе [Совместное использование канала ExpressRoute несколькими подписками](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Являются ли виртуальные сети, подключенные к одному каналу, изолированными друг от друга?
Нет. Все виртуальные сети, связанные с одним каналом ExpressRoute, входят в один домен маршрутизации и не изолированы друг от друга с точки зрения маршрутизации. Если вам требуется изоляция, необходимо создать отдельный канал ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Можно ли связать одну виртуальную сеть с несколькими каналами ExpressRoute?
Да. Можно связать одну виртуальную сеть с несколькими каналами ExpressRoute, но не более чем с 4. Они должны быть заказаны из четырех разных [расположений ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Могу ли я получать доступ к Интернету из виртуальных сетей, подключенных к каналам ExpressRoute?
Да. Если вы не объявляли маршруты по умолчанию (0.0.0.0/0) или префиксы интернет-маршрутов в сеансе BGP, то сможете подключаться к Интернету из виртуальной сети, связанной с каналом ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Могу ли я блокировать возможность подключения к Интернету из виртуальных сетей, подключенных к каналам ExpressRoute?
Да. Вы можете объявлять маршруты по умолчанию (0.0.0.0/0), чтобы блокировать все интернет-подключения к виртуальным машинам, развернутым в виртуальной сети, и направлять весь трафик через канал ExpressRoute. Обратите внимание, что если вы объявляете маршруты по умолчанию, мы будем принудительно отправлять трафик к службам, предлагаемым через общедоступный пиринг (таким как служба хранилища Azure и база данных SQL) обратно в вашу локальную среду. Вы должны будете настроить свои маршруты для возврата трафика в Azure по пути общедоступного пиринга или через Интернет.

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Могут виртуальные сети, связанные с одним каналом ExpressRoute, взаимодействовать друг с другом?
Да. Виртуальные машины, развернутые в виртуальных сетях, подключенных к одному каналу ExpressRoute, могут взаимодействовать друг с другом.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Можно ли использовать подключения типа "сеть-сеть" для виртуальных сетей вместе с ExpressRoute?
Да. ExpressRoute может сосуществовать с VPN типа "сеть-сеть".

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>Можно ли переместить виртуальную сеть из конфигурации "сеть-сеть" или "точка-сеть" для использования ExpressRoute?
Да. Вам нужно будет создать шлюз ExpressRoute в своей виртуальной сети. В связи с этим процессом возникнет небольшой простой.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Почему со шлюзом ExpressRoute в виртуальной сети связан общедоступный IP-адрес?
Это используется только для внутреннего управления. Данный общедоступный IP-адрес не подключен к Интернету и не нарушает безопасность виртуальной сети.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>Что нужно сделать, чтобы подключить службу хранилища Azure через ExpressRoute?
Вы должны установить канал ExpressRoute и настроить маршруты для общедоступного пиринга.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Существуют ли ограничения на количество маршрутов, которые можно объявлять?
Да. Мы принимаем до 4000 префиксов маршрутов для частного пиринга и до 200 для общедоступного пиринга и пиринга Майкрософт. Это значение можно увеличить до 10 000 маршрутов для частного пиринга, если включить расширенную функцию ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Существуют ли ограничения на диапазоны IP-адресов, которые можно объявлять в сеансе BGP?
Мы не принимаем частные префиксы (RFC1918) в сеансе общедоступного пиринга BGP и пиринга BGP Майкрософт.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Что произойдет, если превысить ограничения BGP?
Сеансы BGP будут удалены. Они будут восстановлены, как только число префиксов станет ниже ограничения.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Что такое время удержания ExpressRoute BGP? Можно ли его изменить?
Время удержания — 180 секунд. Сообщения для проверки активности отправляются каждые 60 секунд. Это параметры зафиксированы корпорацией Майкрософт. Их невозможно изменить.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>После объявления маршрута по умолчанию (0.0.0.0/0) для моих виртуальных сетей я не могу активировать ОС Windows на моих виртуальных машинах Azure. Как устранить эту проблему?
Следующие действия помогут Azure распознать запрос на активацию.

1. Установите общедоступный пиринг для своего канала ExpressRoute.
2. Выполните поиск в DNS и найдите IP-адрес **kms.core.windows.net**
3. Затем выполните одно из следующих трех действий, чтобы служба управления ключами распознала, что запрос на активацию поступает из Azure, и обработала его.
   * В своей локальной сети направьте трафик, предназначенный для этого IP-адреса (полученного на шаге 2) обратно в Azure с помощью общедоступного пиринга.
   * Попросите своего поставщика NSP развернуть трафик обратно в Azure с помощью общедоступного пиринга.
   * Создайте определяемый пользователем маршрут, указывающий IP-адрес для Интернета в качестве следующего прыжка, и примените его к подсетям, в которых находятся эти виртуальные машины.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Можно ли изменить пропускную способность канала ExpressRoute?
Да, можно попытаться увеличить пропускную способность канала ExpressRoute на портале Azure или с помощью PowerShell. Если доступна емкость физического порта, на котором был создан канал, то изменение завершится успешно. Если внести изменение не удалось, это означает, что в текущем порту недостаточно емкости и необходимо создать новый канал ExpressRoute с более высокой пропускной способностью, ИЛИ что в этом расположении недостаточно емкости. Во втором случае увеличить пропускную способность будет невозможно. Необходимо также уточнить у своего поставщика услуг подключения, чтобы убедиться, обновлены ли параметры регулирования в его сетях для поддержки увеличения пропускной способности. Обратите внимание, что уменьшить пропускную способность канала ExpressRoute невозможно. Необходимо будет создать новый канал ExpressRoute с более низкой пропускной способностью, а затем удалить старый канал.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Как изменить пропускную способность канала ExpressRoute?
Вы можете изменить пропускную способность канала ExpressRoute с помощью REST API и командлета PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute Premium
### <a name="what-is-expressroute-premium"></a>Что такое ExpressRoute Premium?
ExpressRoute Premium — это набор функций, перечисленных ниже.

* Увеличенное ограничение таблицы маршрутизации с 4000 маршрутов до 10 000 маршрутов для общедоступного пиринга и частного пиринга.
* Увеличенное число виртуальных сетей, которое можно подключить к  ExpressRoute (по умолчанию — 10). Подробные сведения см. в следующей таблице.
* Глобальные подключения через основную сеть Microsoft. Теперь можно связать виртуальную сеть в одном геополитическом регионе с каналом ExpressRoute в другом регионе. **Пример.** Вы можете связать виртуальную сеть, созданную в Западной Европе, с каналом ExpressRoute, созданным в Кремниевой долине. **Другой пример:** в общедоступном пиринге префиксы из других геополитических регионов объявлены таким образом, что можно подключиться, например, к SQL Azure в Западной Европе из канала в Кремниевой долине.
* Подключение к Office 365 и Dynamics 365.

### <a name="how-many-vnets-can-i-link-to-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a>Сколько виртуальных сетей можно будет связать с каналом ExpressRoute, если включить ExpressRoute Premium?
В приведенных ниже таблицах показаны ограничения ExpressRoute и количество виртуальных сетей на канал ExpressRoute.

[!INCLUDE [expressroute-limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Как включить ExpressRoute Premium?
Функциональность ExpressRoute Premium можно включить, когда эта функция включена, и можно отключить, обновив состояние канала. Вы можете включить ExpressRoute Premium во время создания канала либо путем вызова REST API или командлета PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Как отключить ExpressRoute Premium?
Вы можете отключить ExpressRoute Premium, вызвав REST API или командлет PowerShell. Перед отключением ExpressRoute Premium необходимо убедиться, что потребности подключения были масштабированы для соответствия ограничениям по умолчанию. Мы не сможем удовлетворить запрос на отключение ExpressRoute Premium, если ваше использование выходит за рамки ограничений по умолчанию.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Можно ли выбирать в наборе функций Premium только нужные мне функции?
Нет. Вы не можете выбрать нужные вам функции. При включении ExpressRoute Premium мы включаем все функции.

### <a name="how-much-does-expressroute-premium-cost"></a>Сколько стоит ExpressRoute Premium?
Сведения о стоимости см. на [этой странице](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>ExpressRoute Premium оплачивается дополнительно к стандартной оплате ExpressRoute?
Да. Плата за ExpressRoute Premium начисляется дополнительно к плате за канал ExpressRoute и плате, взимаемой поставщиком услуг подключения.

## <a name="expressroute-for-office-365-and-dynamics-365"></a>ExpressRoute для Office 365 и Dynamics 365
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-dynamics-365"></a>Как создать канал ExpressRoute для подключения к службам Office 365 и Dynamics 365?
1. Просмотрите сведения на странице [Предварительные требования к подключению ExpressRoute](expressroute-prerequisites.md), чтобы убедиться, что вы выполнили требования.
2. Просмотрите список поставщиков и расположений услуг на странице [партнеров и расположений ExpressRoute](expressroute-locations.md) , чтобы убедиться, что ваши потребности подключения удовлетворяются.
3. Спланируйте действия по увеличению пропускной способности, просмотрев раздел [Сетевое планирование и настройка производительности для Office 365](http://aka.ms/tune/).
4. Настройте подключение, выполнив действия, описанные в статье [Процедуры ExpressRoute для подготовки каналов и состояний каналов](expressroute-workflows.md).

> [!IMPORTANT]
> Убедитесь, что вы включили надстройку ExpressRoute Premium при настройке подключения к службам Office 365 и Dynamics 365.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-dynamics-365"></a>Нужно ли включить общедоступный пиринг Azure для подключения к службам Office 365 и Dynamics 365?
Нет, необходимо включить только пиринг Майкрософт. Трафик проверки подлинности в Azure AD будет отправляться через пиринг Майкрософт. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>Могут ли мои существующие каналы ExpressRoute поддерживать подключение к службам Office 365 и Dynamics 365?
Да. Ваши существующие каналы ExpressRoute можно настроить для поддержки подключения к службам Office 365. Убедитесь, что вы имеете достаточную емкость для подключения к службам Office 365 и что вы включили надстройку Премиум. [Сетевое планирование и настройка производительности для Office 365](http://aka.ms/tune/) помогут вам спланировать потребности подключения. Также ознакомьтесь с разделом [Создание и изменение канала ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>К каким службам Office 365 можно получить доступ через подключение ExpressRoute?
Актуальный список служб, поддерживаемых в ExpressRoute, см. на странице [URL-адреса и диапазоны IP-адресов Office 365](http://aka.ms/o365endpoints).

### <a name="how-much-does-expressroute-for-office-365-services-and-dynamics-365-cost"></a>Каковы затраты на использование ExpressRoute для служб Office 365 и Dynamics 365?
Для использования служб Office 365 и Dynamics 365 необходимо включить надстройку Премиум. Подробные сведения о ценах на ExpressRoute см. на [странице стоимости услуг](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>В каких регионах поддерживается ExpressRoute для Office 365?
Дополнительные сведения о списке партнеров и расположений, поддерживаемых ExpressRoute, см. в разделе [Партнеры и расположения ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Можно ли получить доступ к Office 365 через Интернет, даже если в моей организации был настроен ExpressRoute?
Да. Конечные точки службы Office 365 доступны через Интернет, даже если в вашей сети был настроен ExpressRoute. Если вы находитесь в расположении, в котором настроено подключение к службам Office 365 через ExpressRoute, то будете подключаться через ExpressRoute.

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Можно ли получать доступ к службам сообщества Office 365 для государственных организаций США (GCC) через канал ExpressRoute Azure для государственных организаций США?
Да. Конечные точки служб Office 365 GCC доступны через ExpressRoute Azure для государственных организаций США. Однако сначала необходимо открыть запрос в службу поддержки на портале Azure, чтобы предоставить корпорации Майкрософт суффиксы, которые вы планируете публиковать. Подключение к службам Office 365 GCC будет установлено после выполнения запроса в службу поддержки. 

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>Можно ли получить доступ к Dynamics 365 for Operations (ранее — Dynamics AX Online) через подключение ExpressRoute?
Да. [Dynamics 365 for Operations](https://www.microsoft.com/dynamics365/operations) размещается в Azure. Чтобы получить доступ к этой службе, включите в канале ExpressRoute общедоступный пиринг Azure. 


