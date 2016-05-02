<properties 
   pageTitle="Связывание виртуальной сети с каналом ExpressRoute с помощью модели развертывания Resource Manager и портала Azure | Microsoft Azure"
   description="В этой статье кратко описывается процедура связывания виртуальных сетей с каналами ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/14/2016"
   ms.author="cherylmc" />

# Связывание виртуальной сети с каналом ExpressRoute

> [AZURE.SELECTOR]
- [Портал Azure — Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell — Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell — классическая модель](expressroute-howto-linkvnet-classic.md)



В этой статье содержатся сведения о связывании виртуальных сетей с каналами ExpressRoute с помощью модели развертывания Resource Manager и портала Azure. Виртуальные сети могут входить в одну и ту же подписку или в разные подписки.


**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Предварительные требования для настройки

- Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
- Вам потребуется активный канал ExpressRoute. 
	- Выполните инструкции по [созданию канала ExpressRoute](expressroute-howto-circuit-arm.md) и включите его на стороне поставщика услуг подключения. 
	
	- Убедитесь, что для вашего канала настроен частный пиринг Azure. Инструкции по маршрутизации см. в статье [Настройка маршрутизации](expressroute-howto-routing-portal-resource-manager.md).
	
	- Для создания сквозного подключения вам потребуется настроить частный пиринг Azure, а также пиринг BGP между вашей сетью и сетью Майкрософт.
	
	- Вам необходимо иметь созданные и полностью подготовленные виртуальную сеть и шлюз виртуальной сети. Следуйте инструкциям по созданию [VPN-шлюза](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (выполните только шаги 1-5).

Вы можете связать с каналом ExpressRoute до 10 виртуальных сетей включительно. Все каналы ExpressRoute должны находиться в одном геополитическом регионе. При использовании надстройки Premium вы сможете связать больше виртуальных сетей с каналом ExpressRoute. Подробная информация о надстройке Premium приведена в статье [Вопросы и ответы](expressroute-faqs.md).

## Подключение виртуальной сети в той же подписке к каналу


### Создание подключения

1. Убедитесь, что канал ExpressRoute и частный пиринг Azure настроены. Следуйте инструкциям в соответствующих статьях, чтобы [создать канал ExpressRoute](expressroute-howto-circuit-arm.md) и [настроить маршрутизацию](expressroute-howto-routing-arm.md). Ваш канал ExpressRoute должен выглядеть так, как показано на следующем рисунке.

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

	>[AZURE.NOTE] Если пиринги были настроены поставщиком уровня 3, сведения о конфигурации BGP отображаться не будут. Если канал находится в подготовленном состоянии, то вы сможете создавать подключения.

2. Теперь можно приступить к подготовке подключения для связи шлюза виртуальной сети с каналом ExpressRoute. Щелкните **Подключение** **>** **Добавить**, чтобы открыть колонку **Добавление подключения**, и укажите необходимые параметры. См. справочный пример ниже.
	

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
 
	
3. После успешной настройки подключения в вашем объекте подключения будут показаны сведения о подключении.

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### Удаление подключения

Подключение можно удалить, щелкнув значок **Удалить** значок в колонке для подключения.

## Подключение виртуальной сети в другой подписке к каналу

Сейчас подключать виртуальные сети между разными подписками с помощью портала Azurе нельзя. Однако это можно сделать с помощью PowerShell. Дополнительные сведения см. в статье [PowerShell](expressroute-howto-linkvnet-arm.md).

## Дальнейшие действия

Дополнительную информацию об ExpressRoute см. в статье [Часто задаваемые вопросы об ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0420_2016-->