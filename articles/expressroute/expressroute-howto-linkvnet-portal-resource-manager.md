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
   ms.date="10/10/2016"
   ms.author="cherylmc" />


# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Связывание виртуальной сети с каналом ExpressRoute

> [AZURE.SELECTOR]
- [Портал Azure — Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell — Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell — классическая модель](expressroute-howto-linkvnet-classic.md)



В этой статье содержатся сведения о связывании виртуальных сетей с каналами Azure ExpressRoute с помощью модели развертывания Resource Manager и портала Azure. Виртуальные сети могут быть в той же или другой подписке.


**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Предварительные требования для настройки

- Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
- Вам потребуется активный канал ExpressRoute.
    - Следуйте инструкциям, чтобы [создать канал ExpressRoute](expressroute-howto-circuit-arm.md) и включить его на стороне поставщика услуг подключения.

    - Убедитесь, что для вашего канала настроен частный пиринг Azure. Инструкции по маршрутизации см. в статье [Настройка маршрутизации](expressroute-howto-routing-portal-resource-manager.md).

    - Для создания сквозного подключения обязательно настройте частный пиринг Azure, а также пиринг BGP между вашей сетью и сетью Майкрософт.

    - Вам необходимо создать и полностью подготовить виртуальную сеть и шлюз виртуальной сети. Следуйте инструкциям по созданию [VPN-шлюза](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (выполните только шаги 1–5).

К стандартному каналу ExpressRoute можно подключить не более 10 виртуальных сетей. Если используется стандартный канал ExpressRoute, все виртуальные сети должны находиться в одном геополитическом регионе. Если включить надстройку ExpressRoute Premium, вы сможете подключить к каналу ExpressRoute больше виртуальных сетей, включая сети из других геополитических регионов. Дополнительную информацию о надстройке Premium см. в разделе [Вопросы и ответы](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Подключение к каналу виртуальной сети в той же подписке


### <a name="to-create-a-connection"></a>Создание подключения

1. Убедитесь, что канал ExpressRoute и частный пиринг Azure настроены. Следуйте инструкциям в разделах [Создание и изменение канала ExpressRoute](expressroute-howto-circuit-arm.md) и [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md). Ваш канал ExpressRoute должен выглядеть, как показано на рисунке ниже.

    ![Снимок экрана канала ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

    >[AZURE.NOTE] Если пиринги были настроены поставщиком уровня 3, сведения о конфигурации BGP отображаться не будут. Если канал находится в подготовленном состоянии, то вы сможете создавать подключения.

2. Теперь можно приступить к подготовке подключения для связи шлюза виртуальной сети с каналом ExpressRoute. Щелкните **Подключение** > **Добавить**, чтобы открыть колонку **Добавление подключения**, и укажите необходимые параметры. См. следующий пример для справки.


    ![Снимок экрана добавления подключения](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


3. После успешной настройки подключения в вашем объекте подключения будут показаны сведения о подключении.

    ![Снимок экрана объекта подключения](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### <a name="to-delete-a-connection"></a>Удаление подключения

Подключение можно удалить, щелкнув значок **Удалить** в колонке для подключения.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Подключение к каналу виртуальной сети в другой подписке

Сейчас подключать виртуальные сети между разными подписками с помощью портала Azurе нельзя. Однако это можно сделать с помощью PowerShell. Дополнительные сведения см. в статье [PowerShell](expressroute-howto-linkvnet-arm.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).



<!--HONumber=Oct16_HO2-->


