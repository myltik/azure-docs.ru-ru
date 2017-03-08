---
title: "Связывание виртуальной сети с каналом ExpressRoute с помощью портала Azure | Документация Майкрософт"
description: "В этой статье кратко описывается процедура связывания виртуальных сетей с каналами ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b3c0cca9a6d5171b1248b0f463cbbb26641fc5f2
ms.openlocfilehash: a1a689dbfc35107b52f9b84f74ac8bfac0727a0e
ms.lasthandoff: 02/10/2017


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Подключение виртуальной сети к каналу ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager — PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Классическая модель: PowerShell](expressroute-howto-linkvnet-classic.md)
> * [Видео — портал Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
>  

В этой статье содержатся сведения о связывании виртуальных сетей с каналами Azure ExpressRoute с помощью модели развертывания Resource Manager и портала Azure. Виртуальные сети могут быть в той же или другой подписке.

**О моделях развертывания Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Предварительные требования для настройки
* Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
* Вам потребуется активный канал ExpressRoute.
  
  * Следуйте инструкциям, чтобы [создать канал ExpressRoute](expressroute-howto-circuit-arm.md) и включить его на стороне поставщика услуг подключения.
  * Убедитесь, что для вашего канала настроен частный пиринг Azure. Инструкции по маршрутизации см. в статье [Настройка маршрутизации](expressroute-howto-routing-portal-resource-manager.md).
  * Для создания сквозного подключения обязательно настройте частный пиринг Azure, а также пиринг BGP между вашей сетью и сетью Майкрософт.
  * Вам необходимо создать и полностью подготовить виртуальную сеть и шлюз виртуальной сети. Следуйте инструкциям по созданию [VPN-шлюза](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (выполните только шаги 1–5).

* К стандартному каналу ExpressRoute можно подключить не более 10 виртуальных сетей. Если используется стандартный канал ExpressRoute, все виртуальные сети должны находиться в одном геополитическом регионе. 

* Если включить надстройку ExpressRoute Premium, вы сможете подключить к каналу ExpressRoute больше виртуальных сетей, включая сети из других геополитических регионов. Дополнительную информацию о надстройке Premium см. в разделе [Вопросы и ответы](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Подключение к каналу виртуальной сети в той же подписке

### <a name="to-create-a-connection"></a>Создание подключения

> [!NOTE]
> Если пиринги были настроены поставщиком уровня 3, сведения о конфигурации BGP отображаться не будут. Если канал находится в подготовленном состоянии, то вы сможете создавать подключения.
>

1. Убедитесь, что канал ExpressRoute и частный пиринг Azure настроены. Следуйте инструкциям в разделах [Создание и изменение канала ExpressRoute](expressroute-howto-circuit-arm.md) и [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md). Ваш канал ExpressRoute должен выглядеть, как показано на рисунке ниже.

    ![Снимок экрана канала ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
2. Теперь можно приступить к подготовке подключения для связи шлюза виртуальной сети с каналом ExpressRoute. Щелкните **Подключение** > **Добавить**, чтобы открыть колонку **Добавление подключения**, и укажите необходимые параметры.

    ![Снимок экрана добавления подключения](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. После успешной настройки подключения в вашем объекте подключения будут показаны сведения о подключении.

     ![Снимок экрана объекта подключения](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>Удаление подключения
Подключение можно удалить, щелкнув значок **Удалить** в колонке для подключения.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Подключение к каналу виртуальной сети в другой подписке
Канал ExpressRoute может совместно использоваться несколькими подписками. На рисунке ниже схематично показан способ совместного использования каналов ExpressRoute несколькими подписками.

![Подключение между подписками](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Каждое маленькое облако внутри большого облака представляет подписки, принадлежащие различным подразделениям одной организации.
- Любое подразделение в организации может использовать свою собственную подписку для развертывания служб. Кроме того, оно может совместно использовать один выделенный канал ExpressRoute для подключения к корпоративной сети.
- Владельцем канала ExpressRoute может выступать одно подразделение (в данном примере — ИТ-подразделение). Другие подписки в организации могут использовать канал ExpressRoute.

    > [!NOTE]
    > Плата за подключение выделенного канала ExpressRoute и использование полосы пропускания будет взиматься с владельца выделенного канала. Полоса пропускания распределяется между всеми виртуальными сетями.
    > 
    >

### <a name="administration"></a>Администрирование
Владельцем канала является уполномоченный опытный пользователь ресурса канала ExpressRoute. Владелец канала может создавать разрешения, которые могут быть активированы пользователями канала. Пользователи канала являются владельцами шлюзов виртуальных сетей (которые находятся в разных подписках с каналом ExpressRoute). Пользователи канала могут активировать разрешения (по одному разрешению для каждой виртуальной сети).

Владелец канала имеет право изменить или отменить авторизацию в любое время. Отмена разрешения приводит к удалению всех связывающих подключений из подписки, доступ к которой был отменен.

### <a name="circuit-owner-operations"></a>Действия владельца канала

#### <a name="creating-an-authorization"></a>Создание разрешения

Владелец канала создает разрешение. Это приводит к созданию ключа разрешения, который может использоваться пользователем канала для подключения шлюзов виртуальной сети к каналу ExpressRoute. Разрешение действительно только для одного подключения.

1. В колонке "ExpressRoute" щелкните **Разрешения**, а затем введите **имя** авторизации и нажмите кнопку **Сохранить**.

    ![Authorizations](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. После сохранения конфигурации скопируйте **идентификатор ресурса** и **ключ авторизации**.

    ![Ключ авторизации](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

#### <a name="deleting-authorizations"></a>Удаление разрешений

Подключение можно удалить, щелкнув значок **Удалить** в колонке для подключения.

### <a name="circuit-user-operations"></a>Действия пользователя канала

   > [!NOTE]
   > Пользователь канала должен получить от владельца канала идентификатор ресурса и ключ авторизации. 

#### <a name="redeeming-connection-authorizations"></a>Активация разрешений на подключение


Введите необходимые сведения и нажмите кнопку **ОК** на вкладке "Основные".

1.    Нажмите кнопку **+Создать**.

    ![Нажмите кнопку "Создать"](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.    Найдите запись **Подключение** на сайте Marketplace, выберите ее и щелкните **Создать**.

    ![Поиск подключений](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.    Убедитесь, что выбран **тип подключения** ExpressRoute.


4.    Введите необходимые сведения и нажмите кнопку **ОК** в колонке "Основные".

    ![Колонка «Основные»](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.    В колонке **Параметры** выберите **Шлюз виртуальной сети** и установите флажок **Redeem authorization** (Активировать авторизацию).

6.    Введите значения **Ключ авторизации** и **Peer circuit URI** (URI однорангового канала) и присвойте подключению имя. Нажмите кнопку **ОК**.

    ![Колонка "Настройки"](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7. Просмотрите сведения в колонке **Сводка** и нажмите кнопку **ОК**.

#### <a name="releasing-connection-authorizations"></a>Освобождение разрешений на подключение

Разрешение можно освободить, удалив подключение, связывающее канал ExpressRoute и виртуальную сеть.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

