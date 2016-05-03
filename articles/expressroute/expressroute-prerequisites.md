<properties
   pageTitle="Предварительные требования к использованию ExpressRoute | Microsoft Azure"
   description="На этой странице приведен список требований, которые необходимо выполнить, прежде чем заказать канал ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/19/2016"
   ms.author="cherylmc"/>


# Предварительные требования и контрольный список для ExpressRoute  

Чтобы подключиться к облачным службам (Майкрософт) через ExpressRoute, вам нужно выполнить следующие условия.

## Учетная запись Azure

- Наличие действительной и активной учетной записи Microsoft Azure. Она необходима для организации канала ExpressRoute. Каналы ExpressRoute являются ресурсами в подписках Azure. Подписка Azure обязательна, даже если будет выполняться только подключение к облачным службам (Майкрософт), отличным от Azure, например Office 365 и CRM Online.
- Активная подписка на Office 365 (при использовании служб Office 365). Дополнительные сведения см. в разделе [Особые требования к Office 365](#office-365-specific-requirements) этой статьи.

## Поставщик услуг подключения
- Можно работать с [партнером по подключению ExpressRoute](expressroute-locations.md#partners) для подключения к Microsoft Cloud. Подключение между локальной сетью и Майкрософт можно настроить [тремя различными способами](expressroute-introduction.md#howtoconnect). 
- Если ваш поставщик не является партнером по подключению ExpressRoute, вы можете подключиться к Microsoft Cloud [поставщика облачных служб Exchange](expressroute-locations.md#nonpartners).

## Требования к сети
- **Избыточное подключение**: для физического соединения между вами и поставщиком услуг нет требования избыточности. Майкрософт требует устанавливать избыточные сеансы BGP между маршрутизаторами Майкрософт и маршрутизаторами одноранговых участников, даже если у вас есть только [одно физическое подключение к облачной службе Exchange](expressroute-faqs.md#onep2plink). 
- **Маршрутизация**: в зависимости от способа подключения к Microsoft Cloud вам или вашему поставщику необходимо настроить сеансы BGP для [доменов маршрутизации](expressroute-circuit-peerings.md) и управлять этими сеансами. Некоторые поставщики подключений Ethernet или облачных услуг Exchange могут предлагать управление BGP в качестве услуги с добавленной стоимостью.
- **NAT**: Microsoft принимает только общедоступные IP-адреса через пиринг Майкрософт. Если в локальной сети вы используете частные IP-адреса, вам или вашему поставщику услуг необходимо преобразовать их в общедоступные IP-адреса [с помощью NAT](expressroute-nat.md).
- **Качество обслуживания**: в Skype для бизнеса имеются различные услуги (например, передача голоса, видео, текста), которые требуют дифференцированного подхода к качеству обслуживания. Вам и вашему поставщику услуг необходимо соблюдать [требования к качеству обслуживания](expressroute-qos.md).
- **Сетевая безопасность**: при подключении к Microsoft Cloud через ExpressRoute необходимо помнить о [сетевой безопасности](../best-practices-network-security.md).
 
## Office 365

Если вы планируете включить Office 365 через ExpressRoute, просмотрите следующие документы для получения дополнительных сведений о требованиях к Office 365.


- [Azure ExpressRoute for Office 365](https://support.office.com/ru-RU/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd) (Azure ExpressRoute для Office 365)
- [Управление сетевым трафиком Office 365](https://support.office.com/ru-RU/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/ru-RU/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Сетевое планирование и настройка производительности для Office 365.](https://support.office.com/ru-RU/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Тестирование сети с помощью калькуляторов и инструментов для расчета пропускной способности](https://support.office.com/ru-RU/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Интеграция Office 365 с локальными средами](https://support.office.com/ru-RU/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## CRM Online 
Если вы планируете включить CRM Online через ExpressRoute, просмотрите следующие документы для получения дополнительных сведений о требованиях к CRM Online.

- [URL-адреса CRM Online](https://support.microsoft.com/kb/2655102) и [диапазоны IP-адресов](https://support.microsoft.com/kb/2728473)

## Дальнейшие действия

- Дополнительную информацию об ExpressRoute см. в статье [Часто задаваемые вопросы об ExpressRoute](expressroute-faqs.md).
- Найдите поставщика услуг подключения ExpressRoute. См. [Партнеры и одноранговые расположения ExpressRoute](expressroute-locations.md).
- См. требования к [маршрутизации](expressroute-routing.md), [NAT](expressroute-nat.md) и [качеству обслуживания](expressroute-qos.md).
- Настройте подключение ExpressRoute.
	- [Создайте канал ExpressRoute.](expressroute-howto-circuit-classic.md)
	- [Настройка маршрутизации](expressroute-howto-routing-classic.md)
	- [Свяжите виртуальную сеть с каналом ExpressRoute.](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0427_2016-->