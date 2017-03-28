---
title: "Создание и изменение канала ExpressRoute с помощью портала Azure | Документация Майкрософт"
description: "В этой статье описывается создание, подготовка, проверка, обновление, удаление и отзыв канала ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: cherylmc;ganesr
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: e74ebd1d18aa3c5d278557940a765fe02c7182ff
ms.lasthandoff: 03/14/2017


---
# <a name="create-and-modify-an-expressroute-circuit"></a>Создание и изменение канала ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager — PowerShell](expressroute-howto-circuit-arm.md)
> * [Классическая модель: PowerShell](expressroute-howto-circuit-classic.md)
> * [Видео — портал Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

В этой статье объясняется, как создать канал Azure ExpressRoute, используя портал Azure и модель развертывания с помощью Azure Resource Manager. Описанные ниже действия показывают, как проверить состояние канала, обновить его или удалить и отозвать.

**О моделях развертывания Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Перед началом работы
* Изучите [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.
* Убедитесь в том, что у вас есть доступ к [порталу Azure](https://portal.azure.com).
* Убедитесь в том, что у вас есть разрешения на создание сетевых ресурсов. Если у вас нет нужных разрешений, обратитесь к администратору учетной записи.
* Вы можете [просмотреть видео](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit), прежде чем начать, чтобы лучше понять выполняемые действия.

## <a name="create-and-provision-an-expressroute-circuit"></a>Создание и предоставление канала ExpressRoute
### <a name="1-sign-in-to-the-azure-portal"></a>1. Выполните вход на портал Azure.
В браузере откройте [портал Azure](http://portal.azure.com) и выполните вход с помощью учетной записи Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Создание канала ExpressRoute
> [!IMPORTANT]
> Выставление счетов за использование ExpressRoute начинается после того, как клиент получает служебный ключ. Обязательно выполните эту операцию, как только поставщик услуг подключения будет готов предоставить канал.
> 
> 

1. Чтобы создать канал ExpressRoute, можно выбрать команду создания ресурса. Выберите **Создать** > **Сети** > **ExpressRoute**, как показано на рисунке ниже.
   
    ![Создание канала ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. После щелчка **ExpressRoute** отобразится колонка **Создание канала ExpressRoute**. При заполнении значений в этой колонке необходимо указать правильный уровень SKU и измерение данных.
   
   * **Уровень** определяет, какого уровня надстройка включена — ExpressRoute "Стандартный" или ExpressRoute "Премиум". Укажите **Standard**, чтобы получить SKU Standard, или **Premium**, чтобы получить надстройку Premium.
   * **Измерение данных** определяет тип выставления счетов. Выберите **С учетом трафика** для тарифного плана с оплатой за трафик или **Не ограничено** для безлимитного тарифного плана. Обратите внимание, что тип выставления счетов можно изменить со **С учетом трафика** на **Не ограничено**, но не наоборот. ********
     
     ![Настройка уровня SKU и измерения данных](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

> [!IMPORTANT]
> Имейте в виду, что "Расположение пиринга" указывает [физическое расположение](expressroute-locations.md) пиринга с корпорацией Майкрософт. Оно **не** связано со свойством Location, которое ссылается на географический регион, в котором находится поставщик сетевых ресурсов Azure. Хотя они не связаны, рекомендуется выбрать поставщик сетевых ресурсов, находящийся недалеко от расположения пиринга канала. 
> 
> 

### <a name="3-view-the-circuits-and-properties"></a>3. Просмотр каналов и свойств
**Просмотр всех каналов**

Вы можете просмотреть все созданные вами каналы, выбрав в меню слева пункт **Все ресурсы** .

![Просмотр каналов](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Просмотр свойств**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Свойства](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Отправка ключа службы поставщику услуг подключения для подготовки
В этой колонке параметр **Состояние поставщика** предоставляет сведения о текущем состоянии подготовки на стороне поставщика услуг. Параметр **Состояние цепи** предоставляет состояние на стороне инфраструктуры Майкрософт. Дополнительные сведения о состояниях подготовки канала см. в статье [Рабочие процессы](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Вновь созданный канал ExpressRoute будет иметь следующее состояние:

Состояние поставщика: Не подготовлено<BR>
Состояние канала: Включен

![Инициация процесса подготовки](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

Когда поставщик услуг подключения находится в процессе его активации, канал переходит в следующее состояние:

Состояние поставщика: Идет подготовка<BR>
Состояние канала: Включен

Для того чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии:

Состояние поставщика: Подготовлено<BR>
Состояние канала: Включен

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Периодическая проверка состояния и статуса ключа канала
Чтобы просмотреть свойства интересующего вас канала, выберите его. Проверьте **Состояние поставщика**. Чтобы вы могли продолжить работу, оно должно измениться на **Подготовлено**.

![Состояние канала и поставщика](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Создание конфигурации маршрутизации
Пошаговые инструкции по созданию и изменению пиринга каналов см. в статье [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-portal-resource-manager.md).

> [!IMPORTANT]
> Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно это IPVPN, например MPLS), то ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Связывание виртуальной сети с каналом ExpressRoute
Теперь свяжите виртуальную сеть с каналом ExpressRoute. При работе с моделью развертывания Resource Manager пользуйтесь статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Получение состояния канала ExpressRoute
Чтобы просмотреть состояние канала, выберите его. 

![Состояние канала ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

## <a name="modifying-an-expressroute-circuit"></a>Изменение канала ExpressRoute
Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Вы можете выполнять следующие действия без простоя:

* включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute;
* увеличивать пропускную способность канала ExpressRoute при условии, что в порту имеется доступная емкость. Обратите внимание, что снижение пропускной способности канала не поддерживается. 
* Перейдите с тарифного плана с оплатой за трафик на безлимитный тарифный план. Обратите внимание, что переход с безлимитного тарифного плана на тарифный план с оплатой за трафик не поддерживается.
* Параметр *Allow Classic Operations*(Разрешить классические операции) можно включать и отключать.

Дополнительные сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

Чтобы изменить канал ExpressRoute, щелкните **Конфигурация**, как показано на рисунке ниже.

![Изменение канала](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

В колонке "Конфигурация" можно изменить пропускную способность, номер SKU, модель выставления счетов и разрешить классические операции.

> [!IMPORTANT]
> Может потребоваться заново создать канал ExpressRoute, если существующий порт не обеспечивает достаточную емкость. Канал невозможно обновить, если в его расположении нет доступной дополнительной емкости.
>
> Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.
> 
> Отключение надстройки уровня "Премиум" может завершиться ошибкой, если используется больше ресурсов, чем разрешено для канала уровня "Стандартный".
> 
> 

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Отзыв и удаление канала ExpressRoute
Канал ExpressRoute можно удалить, щелкнув значок **Удалить** . Обратите внимание на следующее.

* Связь между ExpressRoute и всеми виртуальными сетями необходимо разорвать. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.
* Если подготовка поставщика услуг канала ExpressRoute находится в состоянии **Идет подготовка** или **Подготовлено** то свяжитесь с поставщиком услуг, чтобы отозвать канал с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.
* Если поставщик услуг отзовет канал (состояние подготовки поставщика услуг изменится на **Не подготовлено**), то вы можете удалить канал. В результате будет приостановлено выставление счетов за этот канал.

## <a name="next-steps"></a>Дальнейшие действия
После создания канала обязательно выполните действия, описанные в следующих статьях:

* [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)


